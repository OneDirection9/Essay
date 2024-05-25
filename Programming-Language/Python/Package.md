# Package

## re

[Documentation](https://docs.python.org/3/library/re.html)

- `(...)`: Matches whatever regular expression is inside the parentheses, and indicates the start and end of a group; the contents of a group can be retrieved after a match has been performed.
- `(?:...)`: A non-capturing version of regular parentheses. Matches whatever regular expression is inside the parentheses, but the substring matched by the group **cannot** be retrieved after performing a match or referenced later in the pattern.
- `(?P<name>...)`: Similar to regular parentheses, but the substring matched by the group is accessible via the symbolic group name name

## Hydra

[Documentation](https://hydra.cc/docs/intro/) | [GitHub Repository](https://github.com/facebookresearch/hydra)

- [optional](https://hydra.cc/docs/patterns/specializing_config/#optional)

## OmegaConf

[Documentation](https://omegaconf.readthedocs.io/en/latest/index.html#)

- [Variable interpolation](https://omegaconf.readthedocs.io/en/latest/usage.html#variable-interpolation)
- [Custom resolvers](https://omegaconf.readthedocs.io/en/latest/custom_resolvers.html#)

# Debug

## IPython

[Documentation](https://ipython.readthedocs.io/en/stable/)

Enter interactive python shell in code with:

```python
from IPython import embed

embed()
```

## ipdb

[GitHub Repository](https://github.com/gotcha/ipdb)

- `?/h [<command>]`: help \[for command \<command>\]. Only `?/h` to list all commands.
- `n(ext)`: Continue execution until the next line in the current function is reached or it returns. This command will not enter the subfunction.

3. `s(tep)`: Execute the current line, stop at the first possible occasion (either in a function that is called or in the current function).
1. `r(eturn)`: Continue execution until the current function returns.
1. `c(ont(inue))`: Continue execution, only stop when a breakpoint is encountered.
1. `p expression`: Print the value of the expression.
1. `q(uit)`: Quit from the debugger. The program being executed is aborted.
1. `l(ist)` and `ll`: 查看当前行的代码段
1. `b(reak) [ ([filename:]lineno | function) [, condition] ]`:
   - Without argument, list all breaks.
   - With a line number argument, set a break at this line in the current file.  With a function name, set a break at the first executable line of that function.  If a second argument is present, it is a string specifying an expression which must evaluate to true before the breakpoint is honored.
   > The line number may be prefixed with a filename and a colon, to specify a breakpoint in another file (probably one that hasn't been loaded yet). The file is searched for on `sys.path`; the `.py` suffix may be omitted.
1. `disable/enable <breakpoint No.>`: disable/enable specified breakpoint.
1. `cl(ear) filename:lineno` or `cl(ear) [bpnumber [bpnumber...]]`:
   - With a space separated list of breakpoint numbers, clear those breakpoints.
   - Without argument, clear all breaks (but first ask confirmation).
   - With a `filename:lineno` argument, clear all breaks at that line in that file.
1. `!<python expression>`: Execute python code. e.g. \`from IPython import embed; embed()\`\`
1. `unt(il) [lineno]`:
   - Without argument, continue execution until the line with a number greater than the current one is reached.
   - With a line number, continue execution until a line with a number greater or equal to that is reached.
   > In both cases, also stop when the current frame returns.

# Development

## isort

[Documentation](https://pycqa.github.io/isort/) | [GitHub Repository](https://github.com/PyCQA/isort)

Configuration:

```toml
# Put in pyproject.toml
[tool.isort]
profile = "black"
line_length = 100
multi_line_output = 3
include_trailing_comma = true
default_section= "FIRSTPARTY"
sections= ["FUTURE", "STDLIB", "THIRDPARTY", "FIRSTPARTY", "LOCALFOLDER"]
no_lines_before = ["STDLIB", "LOCALFOLDER"]
```

## black

[Documentation](https://black.readthedocs.io/en/stable/) | [GitHub Repository](https://github.com/psf/black)

Configuration:

```toml
# Put in pyproject.toml
[tool.black]
line-length = 100
include = '\.pyi?$'
exclude = '''
(
  /(
      \.eggs         # exclude a few common directories in the
    | \.git          # root of the project
    | \.hg
    | \.mypy_cache
    | \.tox
    | \.venv
    | _build
    | buck-out
    | build
    | dist
  )/
)
'''
```

## flake8

[Documentation](https://flake8.pycqa.org/en/latest/) | [GitHub Repository](https://github.com/PyCQA/flake8)

Configutation:

```cfg
# Put in setup.cfg
[flake8]
ignore = W503, E203, E221, C901, C408, E741, C407, E741, C404
# C404 dict comprehension ignored due to https://github.com/pytorch/pytorch/issues/41448
max-line-length = 100
max-complexity = 18
select = B,C,E,F,W,T4,B9
exclude = build
per-file-ignores = **/__init__.py:F401,F403
```

## pre-commit

[Documentation](https://pre-commit.com/) | [GitHub Repository](https://github.com/pre-commit/pre-commit)

Configuration:

```yaml
# Put in .pre-commit-config.yaml
exclude: ^tests/data/
repos:
- repo: https://gitlab.com/pycqa/flake8
  rev: 3.8.4
  hooks:
    - id: flake8
      additional_dependencies: ["flake8-bugbear", "flake8-comprehensions"]
- repo: https://github.com/timothycrosley/isort
  rev: 5.6.4
  hooks:
    - id: isort
- repo: https://github.com/psf/black
  rev: 20.8b1
  hooks:
    - id: black
      language_version: python3
- repo: https://github.com/pre-commit/pre-commit-hooks
  rev: v3.1.0
  hooks:
    - id: trailing-whitespace
    - id: check-yaml
      args: ['--unsafe']
    - id: end-of-file-fixer
    - id: requirements-txt-fixer
    - id: fix-encoding-pragma
      args: ['--remove']
    - id: mixed-line-ending
      args: ['--fix=lf']
```
