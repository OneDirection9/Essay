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
- `s(tep)`: Execute the current line, stop at the first possible occasion (either in a function that is called or in the current function).
- `r(eturn)`: Continue execution until the current function returns.
- `c(ont(inue))`: Continue execution, only stop when a breakpoint is encountered.
- `p expression`: Print the value of the expression.
- `q(uit)`: Quit from the debugger. The program being executed is aborted.
- `l(ist)` and `ll`: 查看当前行的代码段
- `b(reak) [ ([filename:]lineno | function) [, condition] ]`:
  - Without argument, list all breaks.
  - With a line number argument, set a break at this line in the current file.  With a function name, set a break at the first executable line of that function.  If a second argument is present, it is a string specifying an expression which must evaluate to true before the breakpoint is honored.
  > The line number may be prefixed with a filename and a colon, to specify a breakpoint in another file (probably one that hasn't been loaded yet). The file is searched for on `sys.path`; the `.py` suffix may be omitted.
- `disable/enable <breakpoint No.>`: disable/enable specified breakpoint.
- `cl(ear) filename:lineno` or `cl(ear) [bpnumber [bpnumber...]]`:
  - With a space separated list of breakpoint numbers, clear those breakpoints.
  - Without argument, clear all breaks (but first ask confirmation).
  - With a `filename:lineno` argument, clear all breaks at that line in that file.
- `!<python expression>`: Execute python code. e.g. \`from IPython import embed; embed()\`\`
- `unt(il) [lineno]`:
  - Without argument, continue execution until the line with a number greater than the current one is reached.
  - With a line number, continue execution until a line with a number greater or equal to that is reached.
  > In both cases, also stop when the current frame returns.
