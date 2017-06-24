### content

1. [git命令](#git命令)

2. [git远程操作](#git远程操作)

# git命令

[git clone、git remote、git fetch、git pull、git push详细参考网址](http://www.ruanyifeng.com/blog/2014/06/git_remote.html)

### git pull

取回远程主机某个分支的更新，再与本地的指定分支合并。
`git pull [--rebase]  <远程主机名>  <远程分支名>：<本地分支名>`

### git push

将本地分支推送到远程分支。
`git push <远程主机名> <本地分支名>：<远程分支名>`

### git commit

- http://blog.csdn.net/tangkegagalikaiwu/article/details/8542827/

- git commit --amend：修改最后一次提交的信息

- 对于历史的提交使用rebase：

   - git rebase -i HEAD~N

   - 将pick修改为edit，保存退出

   - git commit --amend

   - git rebase --continue

- git commit -p命令，输入m会有提示信息。提交部分修改

### git reset

- git reset HEAD^：回退到上一个版本

- git reset HEAD^ a.py：将a.py回退到上一个版本

- git reset HEAD~3：向前回退到第3个版本

- git reset 057d：回退到某个版本

### git reflog

- git你删除一个分支，重置一个分支之后仍然可以恢复

- git reflog：记录所有HEAD的历史。再使用git reset 版本号 进行回退就行。

### git patch

- git format-patch命令， -o：输出到目录下；-n2：输出最新的2个提交。

- 应用patch文件：

   - git am patch-filename

   - cat patch-filename | patch -p1

### git statch

- 暂存未提交的修改：git statch

- 恢复之前的修改：git statch pop

- 查看暂存的修改：git statch -list

### git rm

- git rm --cached filename：删除filename的跟踪，并保存文件在本地

- git rm --f filename：删除filename的跟踪，并删除文件

### git diff

- diff跟文件是否被add有关，与是否被commit无关。即diff比较的是最后一次被add之后的内容，和再次进行add之前的内容。

- git diff --cached：比较暂存起来的文件和上次提交时的差异。

- git diff HEAD：工作区和HEAD相比较

### git checkout

- git checkout -- filename：撤销文件的修改

- git checkout branchName：切换分支

### git log

[参考网址](http://blog.csdn.net/wh_19910525/article/details/7468549)

- `-p`: 选项展开显示每次提交的内容差异；

- `-2`: 则仅显示最近的两次更新。

### git branch

- git clone的时候，克隆的是master分支的提交。*git clone -o remoteName URL：使用-o指定仓库名*

- git 提交到远程分支（非主分支）：

   - git branch -a： 查看本地分之和远程分支

   - git checkout -b local_branch_name remote_branch_name: 新建一个本地分支，并与远程分支建立联系。

      > 远程分支名字名称：origin/deepmask类似这种

- git merge branchname：将branchname合并到现在分支。

- git mv oldname newname：修改文件名。


***

# git远程操作

### git协作

- git fork之后，将原始仓库的同步到自己仓库：

   - 参考[网址](http://m.blog.csdn.net/article/details?id=50477139)
   - 首先把fork到自己账号下的仓库clone下来

   - 配置原始仓库的路径

   - git fetch从原始仓库取文件

   - 切换到master分支，将原始仓库内容合并到当前分支

   - 然后你git push 到自己的远端仓库。

   - 可以直接在github网站上进行pull request操作，不需要一些命令。

### git子模块

参考[网址](http://www.jianshu.com/p/491609b1c426)

- 修改子模块的远程仓库：

   - 先修改.gitmodules修改远程仓库URL

   - 再运行git submodule sync命令

- 修改子模块的内容：

   - 需要先checkout到一个分支，再进行修改

   - add, commit之后推送子模块的修改。

   - add, commit之后推送主模块的修改

***

Author: WarmerHan

Time: 2017/06/24
