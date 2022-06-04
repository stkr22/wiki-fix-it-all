# VsCode

## How do I run bash code directly from VsCode?

++ctrl+shift+p++ and choose run selected text. Click on the small symbol on the right and set a keybinding.

Sources:

- <https://code.visualstudio.com/docs/editor/integrated-terminal#_run-selected-text>

## Python

### Vscode Plugins

- Python
- Bracket Colorizer 2
- Community Material Theme (ocean dark) equinusocio.vsc-material-theme
- Docker
- indent Rainbow
- Material Icon Theme pkief.material-icon-theme
- Python Docstring Generator njpwerner.autodocstring
- Python Indent kevinrose.vsc-python-indent

### How do I debug stuff?

set breakpoints and click on run&debug on the left hand side.

### How do I select the jupyter kernel?

++ctrl+p++ -> Select jupyter kernel to start with

### Run code interactively with shorts

you can simply run code interactively by entering

``` py
# %%
```

This will give you the option to run or debug cells below. No need for IPython notebooks. Note that you can switch the env down below. Also note that you then need to restart the current session strg.

### Run test automatically

Use the little flask menu on the left. But before you can use that you need to enable it in the python extension settings. Search for "test" and set the Python > Testing Enabled.  

Also set the Python > Testing Pytest Path so that is knows where to find tests

### Linting

Add your virtual env to the Python > Linting Ignore Patterns. And activate linting. I use Flake8, Pylint and Bandit for security.