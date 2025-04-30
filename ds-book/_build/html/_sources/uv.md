# uv project manager

**uv** is an extremely fast Python package and project manager, written in Rust.

https://docs.astral.sh/uv/

## Install uv

```{code-block} bash
pip install uv
```

## Start a project

```{code-block} bash
uv init my_project

cd my_project
```

Alternatively, you can init a python project from an existing folder

```{code-block} bash
cd my_project_folder

uv init
```

It will create a project folder with the following structure

```{code-block} bash
.
├── .venv
│   ├── bin
│   ├── lib
│   └── pyvenv.cfg
├── .python-version
├── README.md
├── main.py
├── pyproject.toml
└── uv.lock
```

## Run Python script

```{code-block} bash
uv run main.py
```

## Manage dependencies

Add a Python package
```{code-block} bash
uv add requests

# specify a version
uv add 'requests==2.31.0'

# add from git repository
uv add git+https://github.com/psf/requests
```

Add python packages from requirements.txt
```{code-block} bash
uv add -r requirements.txt
```

Remove a package
```{code-block} bash
uv remove requests
```
