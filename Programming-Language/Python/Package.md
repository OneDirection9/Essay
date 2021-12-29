# Package

## re

[Documentation](https://docs.python.org/3/library/re.html)

- `(...)`: Matches whatever regular expression is inside the parentheses, and indicates the start and end of a group; the contents of a group can be retrieved after a match has been performed.
- `(?:...)`: A non-capturing version of regular parentheses. Matches whatever regular expression is inside the parentheses, but the substring matched by the group **cannot** be retrieved after performing a match or referenced later in the pattern.
- `(?P<name>...)`: Similar to regular parentheses, but the substring matched by the group is accessible via the symbolic group name name

## Hydra

[Documentation](https://hydra.cc/docs/intro/) | [GitHub Code Repository](https://github.com/facebookresearch/hydra)

- [optional](https://hydra.cc/docs/patterns/specializing_config/#optional)

## OmegaConf

[Documentation](https://omegaconf.readthedocs.io/en/latest/index.html#)

- [Variable interpolation](https://omegaconf.readthedocs.io/en/latest/usage.html#variable-interpolation)
- [Custom resolvers](https://omegaconf.readthedocs.io/en/latest/custom_resolvers.html#)
