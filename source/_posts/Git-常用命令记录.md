---
title: Git 常用命令记录
date: 2019-12-25 22:33:06
tags: Git
categories: Tools
---

### Git的安装

Linux上的安装：

首先，安装Git需要的依赖包：sudo apt-get install libcurl4-gnutls-dev libexpat1-dev gettext libz-dev libssl-dev

然后，sudo apt-get install git

### 初次运行Git的配置

可以通过 `git config` 命令来修改配置。有 `--system`、`--global`、`--local` 三个参数。

`git config --system` 读写的是/etc/gitconfig这个文件，系统上所有的用户共享的配置

`git config --global` 用户宿主目录下的 ~/.gitconfig

`git config --local`  本工程下的.git/config

如果想要检查你的配置，可以使用 `git config --list` 命令来列出所有 Git 当时能找到的配置。


```shell
# 常用示例
git config --global user.name zhangyaaoo
git config --global user.email zhangyaaoo@gmail.com
git config --global core.editor vim

# 别名
git config --global alias.co checkout
git config --global alias.st status
git config --global alias.ci commit
git config --global alias.br branch
git config --global alias.unstage 'reset HEAD --'

# 列出所有配置
git config --list

# 保存账号密码
git config --global credential.helper store
```

### Git仓库初始化

```shell
# 在现有的目录中初始化仓库
git init

# 克隆已有的仓库
git clone https://github.com/zhangyaaoo/Hello-World
git clone https://github.com/zhangyaaoo/Hello-World my-hello-world
```

### .gitignore文件

对于不想追踪管理的文件，可以将文件名写入 `.gitignore` 文件中。

对于已经被Git管理跟踪的文件，后来又想添加到 .gitignore 文件中，停止跟踪管理，则用命令

```shell
git rm --cached filename
```

从Git管理中删除。然后再将该文件名加入到 .gitignore 文件中，则可以停止跟踪管理该文件。

### 添加修改到暂存区和本地提交

修改一经`commit`，就安全的保存到了本地仓库中，很难将其丢失，特别是如果你定期的推送数据库到其它仓库的话。任何你未提交的东西丢失后很可能再也找不到了。  

```shell
# 添加文件到暂存区
git add <files>
# 或者使用下面的命令添加所有文件
git add . # include untracked files

# commit 只会提交 已暂存到暂存区的 修改到本地仓库
git commit -m 'log messages'

# -a(add) 选项只会暂存 修改 和 删除的 文件，对未跟踪的文件无影响
git commit -a -m 'made a change'

# 查看 工作区的文件 相对于 暂存区的文件 的修改
git diff

# 查看 暂存区的文件 相对于 已提交的文件 的修改
git diff --staged
```

### Git log  & status

```shell
# 查看log
git log
git log -p -2
git log --stat
git log --pretty=oneline
git log --pretty=format:"%h - %an, %ar : %s"
git log --pretty=format:"%h %s" --graph
git log --oneline --decorate

# 查看状态
git status 
git status -s
```


### Git分支

```shell
# 创建分支
git branch <branch_name>

# 切换分支
git checkout <branch_name>

## 创建分支并切换到该分支
git checkout -b <branch_name>
# 相当于：
git branch <branch_name>
git checkout <branch_name>

# 删除分支
git branch -d <branch_name>
git branch -D <branch_name> # 如果删除的分支有未合入当前分支的修改时，需要删除用 D

# 分支合并
git checkout <branch_name_1>
git merge <branch_name_2>

# 查看当前所有分支的列表
git branch
# 同时显示每个分支的最后一次提交
git branch -v

# --merged 与 --no-merged 这两个有用的选项可以过滤列表中已经合并或尚未合并到当前分支的分支
git branch --merged
git branch --no-merged
```

### 历史 回撤



### 远程仓库 拉取 推送



### 标签 别名



### 危险的命令

- `git checkout -- <files>`
- `git reset --hard`
- 