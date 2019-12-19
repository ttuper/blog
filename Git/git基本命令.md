### 仓库操作
配置当前机器用户名：`git config --global user.name "Your Name"`

配置当前机器y邮箱：`git config --global user.email "email@example.com"`

创建SSH KEY：`ssh-keygen -t rsa -C "youremail@example.com"`

关联远程仓库：`git remote add origin git@server-name:path/repo-name.git`

首次推送本地内容至远程仓库：`git push -u origin master`

非首次推送本地修改内容至远程仓库：`git push origin master`

克隆远程库至本地：`git clone <仓库地址>`

查看远程库信息：`git remote -v`

在本地创建和远程分支对应的分支：`git checkout -b branch-name origin/branch-name`

建立本地和远程分支的关联：`git branch --set-upstream branch-name origin/branch-name`

从远程库拉取内容到本地仓库：`git pull`

把本地未push的分叉提交历史整理成直线：`git rebase`

### 基本操作
将文件添加到仓库暂存区：`git add <file>`

将暂存区文件提交到仓库分支：`git commit -m "注释"`

掌握工作区状态：`git status`

查看修改内容：`git diff <file>`

查看版本历史：`git log --pretty=oneline`

查看分支合并图：`git log --graph --pretty=oneline --abbrev-commit`

回退版本：`git reset --hard commit_id`

查看命令历史：`git reflog`

使用`git add`添加到暂存区如何撤销：`git checkout -- file`

使用`git commit`提交到版本库分支如何撤销：`git reset HEAD <file>`

从版本库中删除文件：`git rm <file>`

保存当前工作现场：`git stash`

返回指定工作现场：`git stash stash@{0}`

返回工作现场并删除stash内容：`git stash pop`

### 分支操作
查看分支：`git branch`

创建分支：`git branch <name>`

切换分支：`git checkout <name>`

创建+切换分支：`git checkout -b <name>`

合并某分支到当前分支：`git merge <name>`

删除本地分支：`git branch -d <name>`

删除远程分支：`git push origin --delete <name>`

删除未合并的分支：`git branch -D <name>`

取消当前合并：`git merge --abort`

### 标签操作
新建一个标签：`git tag <tagname> <commitid>`

指定标签信息：`git tag -a <tagname> -m "blablabla..."`

查看标签详细信息：`git show <tagname>`

推送一个本地标签：`git push origin <tagname>`

推送全部未推送过的本地标签：`git push origin --tags`

删除一个本地标签：`git tag -d <tagname>`

删除一个远程标签：`git push origin :refs/tags/<tagname>`

### 别名操作
设置命令别名：`git config --global alias.<aliasname> <name>`

`git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"`
