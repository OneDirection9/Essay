# Linux

## sed

documentation: https://www.gnu.org/software/sed/manual/sed.html

### Overview

`sed [option] SCRIPT INPUTFILE...`

### Options

documentation: https://www.gnu.org/software/sed/manual/sed.html#Command_002dLine-Options

- `-n`: Same as `--quiet` or `--silent`. By default, `sed` prints out the pattern space
  at the end of each cycle through the script. These options disable this automatic printing,
  and `sed` only produces output when explicitly told to via the `p` command.
- `-i[SUFFIX]`: Same as `--in-place[=SUFFIX]`. This option specifies that files are to be edited in-place.
  If `SUFFIX` is provided, a backup file will be created.
- `-r`: Same as `--regexp-extended`. Use extended regular expressions rather than basic regular expressions.
  - [extended regular expression syntax](https://www.gnu.org/software/sed/manual/sed.html#ERE-syntax)
- `--debug`: Print the input sed program in canonical form, and annotate program execution.

### Scripts

documentation: https://www.gnu.org/software/sed/manual/sed.html#sed-scripts

Syntax:

```text
[addr]X[options]
```

`X` is a single-letter sed command. `[addr]` is an optional line address.
If `[addr]` is specified, the command `X` will be executed only on the matched lines.
`[addr]` can be a single line number, a regular expression, or a range of lines.
Additional `[options]` are used for some sed commands.

**Command**:

- `a text`: Append `text` after a line.
- `c text`: Change/Replace lines with `text`.
- `d`: Delete the pattern space; immediately start next cycle.
- `i text`: Insert `text` before a line.
- `p`: Print the pattern space.
- `s/regexp/replacement/[flags]`: Match the regular-expression against the content of the pattern space.
  If found, replace matched string with `replacement`.
-
