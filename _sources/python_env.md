# Python virtual environment

```{admonition} Install multiple Python versions
Click link below to see how to install and use different version of Python

https://k0nze.dev/posts/install-pyenv-venv-vscode/

```

## Create Python virtual environment for projects

1. Create virtual env. under your project folder 
```{code-block} bash
python -m venv [your_venv]
```
**Note**: It will create a folder named [your_venv] under your project folder. All installed Python libraries will be stored here.

2. Activate your virtual env.

```{code-block} bash
for Windows:

[your_venv]\Scripts\activate

for MacOS/Linux:

source [your_venv]/bin/activate
```

3. To deactivate the virtual env.
```{code-block} bash
deactivate
```

## Export library list

- Check installed libraries
```{code-block} bash
pip list 
# or
pip freeze
```

- Save installed libraries to `requirements.txt`
```{code-block} bash
pip freeze > requirements.txt
```
## Install required libraries

Under the project folder with the requirements.txt file
```{code-block} bash
pip install -r requirements.txt
```