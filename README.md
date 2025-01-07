# ds-hub

A place to document and share my data science journey

Please visit https://alexkychen.github.io/ds-hub

![](ds-book/pic/welcome_small.png)

### jupyter-book API

- Create a jupyter-book template
```{code-block}bash
$ jupyter-book create mynewbook/
```
Ref: https://jupyterbook.org/en/stable/start/create.html

- Build book html
```{code-block}bash
$ jupyter-book build mybookname/

$ jupyter-book build --all mybookname/ 
```

- Publish to Github page
```{code-block}bash
## under the folder with _build folder
$ ghp-import -n -p -f _build/html
```
Ref: https://jupyterbook.org/en/stable/start/publish.html
