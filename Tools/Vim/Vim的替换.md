# 引言

使用`[addr]s/原字符串/目的字符串/[option]`

# 命令详解

## [addr] 检索范围

省略时表示当前行。

1. `n, m`：表示从第n行到第m行；
2. `%`：表示全局，等价于`1, $`；
3. `., $`：表示当前行到文件尾；
4. `., +2`：表示当前行与接下来两行

## [option] 操作类型

省略option时表示仅对每行第一个匹配串进行操作。

1. `g`：g表示全局；
2. `c`：表示进行确认；
3. `p`：表示替代结果逐行显示；
4. `i`：i表示大小写不敏感查找，`I`表示大小写敏感查找；
5. 如果源字符串和目的字符串之间出现特殊字符，需要用`\`转义。、

例子：

全局查找`foo`替换为`bar`需要进行确认

```shell
:%s/foo/bar/gc
```

回车后光标移动到每一次`far`匹配的位置，并提示：

```shell
replace with bar (y/n/a/q/l/^E/^Y)?
```

`y`表示替换，`n`表示不替换，`a`表示替换所有，`q`表示退出查找模式，`l`表示替换当前位置并退出，`^E`和`^Y`是光标移动快捷键。

# 删除特定的行

1. 删除包含特定字符的行：
   ```shell
   g/pattern/d
   ```   

2. 删除不包含指定字符的行：
   
   ```shell
   v/pattern/d
   g!/pattern/d
   ```

# 使用技巧

## 替换某个字符为换行

When searching: `\n` is newline, `\r` is CR (carriage return = Ctrl-M = ^M)

When replacing: `\r` is newline, `\n` is a null byte (0×00).

意思是：

字符串查找时，`\n` 是换行，`\r` 是回车，也就是经常会看到的 ^M（备注-1）。

字符串替换时，`\r` 是换行，`\n` 是空字符（0×00）。

## 交换字符串的位置

把文中的所有字符串“abc……xyz”替换为“xyz……abc”可以有下列写法

```shell
:%s/abc\(.*\)xyz/xyz\1abc/g
:%s/\(abc\)\(.*\)\(xyz\)/\3\2\1/g
```

***

Author: Zhipeng Han

Time: 2017/04/20
