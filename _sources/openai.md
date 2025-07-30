# OpenAI API

Templates for building AI workflow, agents, or systems 

OpenAI available LLMs https://platform.openai.com/docs/models 

## Simple text generation
```{code-block} python
from openai import OpenAI
client = OpenAI()

response = client.responses.create(
    model="gpt-4.1",
    input="Write a one-sentence bedtime story about a unicorn."
)

print(response.output_text)
```

## Analyze images

### Passing an URL
```{code-block} python
from openai import OpenAI
client = OpenAI()

response = client.responses.create(
    model="gpt-4.1-mini",
    input=[{
        "role": "user",
        "content": [
            {"type": "input_text", "text": "what's in this image?"},
            {
                "type": "input_image",
                "image_url": "https://domain.name.com/image.jpg",
            },
        ],
    }],
)

print(response.output_text)
```
### Passing a file id
```{code-block} python
from openai import OpenAI
client = OpenAI()

# Function to create a file with the Files API
def create_file(file_path):
  with open(file_path, "rb") as file_content:
    result = client.files.create(
        file=file_content,
        purpose="vision",
    )
    return result.id

# Getting the file ID
file_id = create_file("path_to_your_image.jpg")

response = client.responses.create(
    model="gpt-4.1-mini",
    input=[{
        "role": "user",
        "content": [
            {"type": "input_text", "text": "what's in this image?"},
            {
                "type": "input_image",
                "file_id": file_id,
            },
        ],
    }],
)

print(response.output_text)
```
### Passing a Base64 encoded image
```{code-block} python
import base64
from openai import OpenAI

client = OpenAI()

# Function to encode the image
def encode_image(image_path):
    with open(image_path, "rb") as image_file:
        return base64.b64encode(image_file.read()).decode("utf-8")

# Path to your image
image_path = "path_to_your_image.jpg"

# Getting the Base64 string
base64_image = encode_image(image_path)

response = client.responses.create(
    model="gpt-4.1",
    input=[
        {
            "role": "user",
            "content": [
                { "type": "input_text", "text": "what's in this image?" },
                {
                    "type": "input_image",
                    "image_url": f"data:image/jpeg;base64,{base64_image}",
                },
            ],
        }
    ],
)

print(response.output_text)
```

## Structured output
```{code-block} python
from openai import OpenAI
from pydantic import BaseModel

client = OpenAI()

class CalendarEvent(BaseModel):
    name: str
    date: str
    participants: list[str]

response = client.responses.parse(
    model="gpt-4o-2024-08-06",
    input=[
        {"role": "system", "content": "Extract the event information."},
        {
            "role": "user",
            "content": "Alice and Bob are going to a science fair on Friday.",
        },
    ],
    text_format=CalendarEvent,
)

event = response.output_parsed
```
More in-depth examples of structured output: [OpenAI page](https://platform.openai.com/docs/guides/structured-outputs?api-mode=responses)

## Single agent
```{code-block} python
# import openai library
from agents import Agent, Runner, trace

agent = Agent(name="Joke teller", instructions="You're a good joke teller", model="gpt-4o-mini")
# consider the above instructions as system_prompt

with trace("Tell a joke"):
    result = await Runner.run(agent, "Tell a joke about Autonomous AI Agents")
    print(result.final_output)
```
- Using trace() allows you to track how your agent workflow via https://platform.openai.com/traces 
- Reference for running agents, https://openai.github.io/openai-agents-python/running_agents/

## Multi agents

Prepare for agents other than OpenAI's
```{code-block} python
from openai import AsyncOpenAI
from agents import OpenAIChatCompletionsModel

GEMINI_BASE_URL = "https://generativelanguage.googleapis.com/v1beta/openai/"
DEEPSEEK_BASE_URL = "https://api.deepseek.com/v1"
GROQ_BASE_URL = "https://api.groq.com/openai/v1"

gemini_client = AsyncOpenAI(base_url=GEMINI_BASE_URL, api_key=os.getenv('GOOGLE_API_KEY'))
deepseek_client = AsyncOpenAI(base_url=DEEPSEEK_BASE_URL, api_key=os.getenv('DEEPSEEK_API_KEY'))
groq_client = AsyncOpenAI(base_url=GROQ_BASE_URL, api_key=os.getenv('GROQ_API_KEY'))

gemini_model = OpenAIChatCompletionsModel(model="gemini-2.0-flash", openai_client=gemini_client)
deepseek_model = OpenAIChatCompletionsModel(model="deepseek-chat", openai_client=deepseek_client)
llama3_3_model = OpenAIChatCompletionsModel(model="llama-3.3-70b-versatile", openai_client=groq_client)
```

Start with instructions 
```{code-block} python
instructions1 = "Write your instructions here \
    more instructions"
instructions2 = " another instructions \
    more things to say"
instructions3 = " " 
```

Construct agents
```{code-block} python
agent1 = Agent(
    name="",
    instructions=instructions1, 
    model=gemini_model)

agent2 = Agent(
    name=" ",
    instructions=instructions2,
    model=deepseek_model
)

agent3 = Agent(
    name=" ",
    instructions=instructions3,
    model=llama3_3_model
)
```
Run three agents in parallel
```{code-block} python
import asyncio

message = "Do something"

with trace("Parallel call agents"):
    results = await asyncio.gather(
        Runner.run(agent1, message),
        Runner.run(agent2, message),
        Runner.run(agent3, message),
    )

outputs = [result.final_output for result in results]

for output in outputs:
    print(output + "\n\n")
```

## Tools

Tools, used in agents or LLM calls, could be as a function or agent. It allows agents to get information returned from a function/agent and then output the results.  

### Function as tools

Use decorator @ to make a function as a tool to be used in LLM API calls
```{code-block} python
from agents import function_tool

@function_tool
def my_function_tool(body: str):
    """Write a description about this function"""
    " Do something in Python "
    return {"status": "success"}
```
- Reference for tools, https://openai.github.io/openai-agents-python/tools/#function-tools

### Agents as tools

Use `.as_tool` to turn an agent to a tool, and wrap tools and functions in a list
```{code-block} python
agent_tool1 = agent1.as_tool(tool_name="Tool No1", tool_description=" ")
agent_tool2 = agent2.as_tool(tool_name="Tool No2", tool_description=" ")
```

### Use tools in an agent
```{code-block} python
from agents.model_settings import ModelSettings

instructions = "Write the instruction for running agent manager \
    more instructions"

# Put tools in a list to be called in an agent next step
tools = [my_function_tool, agent_tool1, agent_tool2]

manager = Agent(
    name="agent manager", 
    instructions=instructions, 
    tools = tools,
    model="gpt-4o-mini",
    # forcing tools to be used
    model_settings=ModelSettings(tool_choice="required")
    )

message = "what to do"

with trace("agent manager"):
    result = await Runner.run(manager, message)
```
- Tool settings https://openai.github.io/openai-agents-python/agents/#forcing-tool-use

### Build a web API tool
Create a function tool that can search a website via its web API
```{code-block} python

```

## Handoffs

Hanoffs and agent-as-tools are similar. It's an agent handles processes between tools (function tool or/and agent as tool).

In this scenario, we create a set of tools and one handoffs (some agents and function tools inside the handoffs), and finally call a agent manager to run.

Note: In agents as tool, we use `.as_tool` to convert an agent into a tool, but handoffs simply contains agents. 

```{code-block} python
# create agents that should run step by step
agent_step1 = Agent(name="step1", instructions="Do first step", model="gpt-4o-mini")
agent_step2 = Agent(name="step2", insturctions="Do second step", model="gpt-4o-mini)

step1_maker = agent_step1.as_tool(tool_name=" ", tool_description=" ")
step2_maker = agent_step2.as_tool(tool_name=" ", tool_description=" ")
```

Create a handoff agent that handle step-by-step tools 
```{code-block} python
instructions="You are...you first use step1_maker tool to ...\
    then use step2_maker tool to... \ 
    finally, you use my_special_tool tool to... "

process_agent = Agent(
    name=" ",
    instructions = instructions,
    tools = [step1_maker, step2_maker, my_special_tool],
    model="gpt-4o-mini"
    handoff_description = " "
)
```

Create a final agent manager to handle all the processes
```{code-block} python
manager_instructions = " " 

tools = [tool1, tool2, tool3]
handoffs = [process_agent]

manager_agent = Agent(
    name = "",
    instructions = manager_instructions,
    tools = tools,
    handoffs = handoffs,
    model = "gpt-4o-mini"
)

message = " "

with trace("handoff agents"):
    result = await Runner.run(manager_agent, message)
```

## Structured output and guardrail

Guardrails along with structured output can help perform sanitization check for user input and LLM output.

### Create input guardrail

An agent embedded in a main agent to verify certain condition in user input

```{code-block} python
# Create a class (data model) using pydantic
from pydantic import BaseModel
from agents import input_guardrail

class CheckInput(BaseModel):
    is_name_in_message: bool
    name: str

input_guardrail_agent = Agent(
    name = " ",
    instructions = "check if user includes someone's name in the input prompt",
    output_type = CheckInput,
    model = "gpt-4o-mini"
)

# Define guardrail function
@input_guardrail
async def my_guardrail(ctx, agent, message):
    result = await Runner.run(input_guardrail_agent, message, context=ctx.context)
    is_name_in_message = result.final_output.is_name_in_message
    return GuardrailFunctionOutput(output_info={"found_name": result.final_output},tripwire_triggered=is_name_in_message)
```

```{code-block} python
manager = Agent(
    name=" ",
    instructions = " ",
    tools = tools, # list of tools
    handoffs = handoffs, # handoffs in a list
    model = "gpt-4o-mini",
    input_guardrails = [my_guardrail]
)

message = "user input with someone's name - Edison"

with trace("Protected with input guardrail"):
    results = await Runner.run(manager, message)
```

**In the above case, input prompt (message) contains a person's name. Therefore, the LLM call will raise an error.**

- Guardrail reference: https://openai.github.io/openai-agents-python/guardrails/