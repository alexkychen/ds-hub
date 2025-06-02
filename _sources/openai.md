# OpenAI SDK

Templates for building AI workflow, agents, or systems 

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
Using trace() allows you to track how your agent workflow via https://platform.openai.com/traces 

## Multi agents

Prepare for agents other than OpenAI's
```{code-block} python
from openai import AsyncOpenAI

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

## Function as tools

## Function and agents as tools

## Handoff