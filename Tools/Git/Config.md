# config优先级

1. `.git/.gitconfig`: `git config --local(默认) ...... `

2. `~/.gitconfig`: `git config --global ......`

3. `/etc/gitconfig`: `git config --system ......`

```
git config [--local | --global | --system] -e：使用默认编辑器进行编辑
										   -l: 列出config配置
```

配置用户名和邮箱：

```
git config --global user.name "hanzhipeng"
git config --global user.email "hanzhipeng@pku.edu.cn"
```


# .gitignore文件

**.gitignore 只对 untracked 文件起作用。** .gitignore文件的路径是，仓库根目录下。详细语法见本文末。

忽略文件的原则是：

1. 忽略操作系统自动生成的文件，比如缩略图等；

2. 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的.class文件；

3. 忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。

有些时候，你想添加一个文件到Git，但发现添加不了，原因是这个文件被.gitignore忽略了：
```
$ git add App.class
The following paths are ignored by one of your .gitignore files:
App.class
Use -f if you really want to add them.
```

如果你确实想添加该文件，可以用-f强制添加到Git：
```
$ git add -f App.class
```

或者你发现，可能是.gitignore写得有问题，需要找出来到底哪个规则写错了，可以用git check-ignore命令检查：
```
$ git check-ignore -v App.class
.gitignore:3:*.class    App.class
```

Git会告诉我们，.gitignore的第3行规则忽略了该文件，于是我们就可以知道应该修订哪个规则。

# editor

为了方便git commit -s的时候进行编辑，可以使用如下命令修改默认编辑器：

```
git config --global core.editor vim
```

# git alias

   1. status: `git config --global alias.st status`

   2. checkout: `git config --global alias.co checkout`

   3. branch: `git config --global alias.br branch`

   4. git log \-\-pretty\=onelone: `git config --global alias.ls "log --pretty=oneline"`

# ssh key

1. 生成ssh key:

   - 查看是否已经有了ssh密钥，cd ~/.ssh 如果没有密钥则不会有此文件夹，有则备份删除。

   - 生成密钥：

      - `ssh-keygen -t rsa -C “hanzhipeng@pku.edu.cn”`

      - 按三个回车，密码为空。最后得到在两个文件`id_rsa`和`id_rsa.pub`

2. 添加key到ssh：

   - `:ssh-add id_rsa`

   - 若出现这个错误:`Could not open a connection to your authentication agent`，则先执行如下命令即可：`sh-agent bash`

3. 在github上添加ssh密钥，密钥在`id_rsa.pub`里面。

   - 测试：ssh git@github.com

4. 修改远程仓库的URL协议为ssh：

   - git remote set-url remoteName URL

   - 假如一开始的是https的协议： `https://github.com/USERNAME/REPOSITORY.git`

   - 修改为ssh的样式应该是： `git@github.com:USERNAME/REPOSITORY.git`

***

# ignore语法

1. 空行或是以`#`开头的行即注释行将被忽略；

2. 可以在前面添加正斜杠`/`来避免递归，下面的例子中可以很明白的看出来与下一条的区别；

3. 可以在后面添加正斜杠`/`来忽略文件夹，例如 build/ 即忽略build文件夹；

4. 可以使用`!`来否定忽略，即比如在前面用了`*.apk`，然后使用`!a.apk`，则这个`a.apk`不会被忽略；

5. `*`用来匹配零个或多个字符，如`*.[oa]`忽略所有以`.o`或`.a`结尾，`*~`忽略所有以`~`结尾的文件（这种文件通常被许多编辑器标记为临时文件）；`[]`用来匹配括号内的任一字符，如`[abc]`，也可以在括号内加连接符，如`[0-9]`匹配0至9的数；`?`用来匹配单个字符。

例子：
```
# 忽略 .a 文件
*.a
# 但否定忽略 lib.a, 尽管已经在前面忽略了 .a 文件
!lib.a
# 仅在当前目录下忽略 TODO 文件， 但不包括子目录下的 subdir/TODO
/TODO
# 忽略 build/ 文件夹下的所有文件
build/
# 忽略 doc/notes.txt, 不包括 doc/server/arch.txt
doc/*.txt
# 忽略所有的 .pdf 文件 在 doc/ directory 下的
doc/**/*.pdf
```

***

Author: WarmerHan

Time: 2017/06/23
