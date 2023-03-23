---
title: git 版本管理
date: 2023-03-23 09:25:00
author: 乔彬
top: true
hide: false
cover: true
summary: git下载，安装，创建，修改版本库，版本转换，GitHub在线代码管理
categories: 工具
tags:
  - git
---


## 一、下载

国内镜像源下载地址

https://npm.taobao.org/mirrors/git-for-windows/

git官网下载

https://git-scm.com/downloads

## 二、安装

**推荐使用默认安装参数, 然后一路`下一步`到底**. 安装好之后, 在你的所有程序中, 将会出现 git 的文件夹, 而且里面会有一个程序叫做 `git bash`. 这个 `git bash` 是 git 在 Windows 上为了方便使用所设置的一个 Unix 的环境. 如果你是 Windows 用户, 之后的教程你也能用这个来学习使用 git.

## 三、创建/修改版本库

### 3.1创建版本库（init）

#### 3.1.1 确定管理文件夹

首先确定对哪个文件夹里的文件进行管理，比如我选的D:/xx研究生文件集合/code 文件；然后打开git bush，把目录调至文件夹`code`,相关代码

~~~git
$ cd D:/xx研究生文件集合/code 文件
~~~

#### 3.1.2 创建管理员用户名和email

为了更好使用git，同时记录每一个施加修改的人，故在git中添加用户名`user.name`和用户email`user.email`

~~~git
$ git config --global user.name "B Q"
$ git config --global user.email "XX@qq.com"
~~~

#### 3.1.3 初始化git

在文件夹中初始化 git 的管理文件库

~~~git
$ git init
# Initialized empty Git repository in D:/xx研究生文件集合/code/.git/
~~~

### 3.2 添加文件管理（add）

![在这里插入图片描述](https://img-blog.csdnimg.cn/137d7ac8556548e5aab4a6490ac9e9ee.png)

#### 3.2.1 查看所有文件

执行 `$ ls` 就能看到文件夹中的所有文件，git 创建的管理库文件 `.git` 是被隐藏起来的需执行`$ ls -a`

~~~git
$ ls -a
# . ..  .git
~~~

#### 3.2.2 创建新的文件

法一：直接在管理库文件夹下右击创建文件

法二：用代码`touch`，如下：

~~~git
$ touch 1.py
~~~

#### 3.2.3 查看版本库状态（`status`）

~~~git
$ git status
# 1.py        # 1.py 文件没有被加入版本库 (unstaged)
~~~

#### 3.2.4 将文件放入`add`版本库中，即（untracked--staged）

~~~git
$ git add 1.py
~~~

若想**一次性添加文件夹中所有未被添加的文件**, 可以使用这个:

~~~git
$ git add .
~~~

#### 3.2.5 提交改变（`commit`）即（staged--unmodified）

添加好了 `1.py` 文件, 最后一步就是提交这次的改变, 并在 `-m` 自定义这次版本名:

~~~git
$ git commit -m "create 1"
~~~

### 3.3 查看记录修改（`log&diff`）

#### 3.3.1 文件被提交 看修改记录用`git log`

输入`git log`就可以看到上次更改的信息（修改的时间及修改的管理员）

~~~git
$ git add 1.py
$ git commit -m "change 1"
$ git log
~~~

#### 3.3.2 查看没提交的信息(` git status`)

如果我们对`1.py`文件进行一次修改, 添加这行代码:

~~~python
a = 1
~~~

然后我们就能在 `status` 中看到修改还没被提交的信息了.

~~~git
$ git status

# 输出
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   1.py    # 这里显示有一个修改还没被提交

no changes added to commit (use "git add" and/or "git commit -a")
~~~

### 3.3.3 文件未提交 查看记录修改（`git diff`)

**三种情况的的diff形式**

~~~git
# 对比三种不同 diff 形式
$ git diff          # unstaged

@@ -1,2 +1,3 @@
 a = 2  # 注: 前面没有 +
 b = 1  # 注: 前面没有 +
+c = b  # 还没 add 去 stage (unstaged)
-----------------------
$ git add .   # add 全部修改文件
$ git diff --cached # staged

@@ -1 +1,2 @@
-a = 1  # 已 staged
+a = 2  # 已 staged
+b = 1  # 已 staged
-----------------------
$ git diff HEAD     # staged & unstaged

@@ -1 +1,3 @@
-a = 1  # 已 staged
+a = 2  # 已 staged
+b = 1  # 已 staged
+c = b  # 还没 add 去 stage (unstaged)
~~~

## 四、版本转换

### 4.1针对全部文件的版本转换（git reset）

#### 4.1.1修改已 commit 的版本

例子：已经提交了 `commit` 却发现在这个 `commit` 中忘了附上另一个文件

操作：添加一个文件, 将这个修改也 `commit` 进 `上一个版本`

~~~git
$ git add 2.py
$ git commit --amend --no-edit   # "--no-edit": 不编辑, 直接合并到上一个 commit
$ git log --oneline    # "--oneline": 每个 commit 内容显示在一行
~~~

#### 4.1.2 reset到上一个add之前

例子：添加 `add` 了修改, 但是又后悔, 并想补充一些内容再 `add`.

操作：想要回到add之前。 比如在 `1.py` 文件中添加这一行:

~~~python
d = 3
~~~

然后 `add` 去 `staged` 再返回到 `add` 之前:

~~~git
$ git add 1.py
$ git status -s # "-s": status 的缩写模式
# 输出
M  1.py     # staged
-----------------------
$ git reset 1.py
$ git status -s
# 输出
M 1.py     # unstaged
~~~

#### 4.1.3 reset 回到 commit 之前

每个 `commit` 都有自己的 `id` 数字号, `HEAD` 是一个指针, 指引当前的状态是在哪个 `commit`. 最近的一次 `commit` 在最右边, 我们如果要回到过去, 就是让 `HEAD` 回到过去并 `reset` 此时的 `HEAD` 到过去的位置.

~~~git
# 看看所有的log
$ git log --oneline
# 输出
904e1ba change 2
c6762a1 change 1
13be9a7 create 1.py
-----------------------
# 回到 c6762a1 change 1
# 方式1: "HEAD^"
$ git reset --hard HEAD^  

# 方式2: "commit id"
$ git reset --hard c6762a1
-----------------------
# 看看现在的 log
$ git log --oneline
# 输出
c6762a1 change 1
13be9a7 create 1.py
~~~

目标：挽救消失的`change 2`

操作：查看 `$ git reflog` 里面最近做的所有 `HEAD` 的改动, 并选择想要挽救的 `commit id`，重复 `reset` 步骤就能回到 `commit (amend): change 2` (id=904e1ba)这一步了::

~~~git
$ git reflog
# 输出
c6762a1 HEAD@{0}: reset: moving to c6762a1
904e1ba HEAD@{1}: commit (amend): change 2
0107760 HEAD@{2}: commit: change 2
c6762a1 HEAD@{3}: commit: change 1
13be9a7 HEAD@{4}: commit (initial): create 1.py
$ git reset --hard 904e1ba
$ git log --oneline
~~~

### 4.2 针对单个文件的版本转换（check out）

#### 4.2.1 改写文件（checkout）

**目标：**

我们要对 `1.py` 进行回到过去操作, 回到 `c6762a1 change 1` 这一个 `commit` 

**操作：**

使用 `checkout` + id `c6762a1` + `--` + 文件目录 `1.py`, 我们就能将 `1.py` 的指针 `HEAD` 放在这个时刻 `c6762a1`:

~~~git
$ git log --oneline
# 输出
904e1ba change 2
c6762a1 change 1
13be9a7 create 1.py
---------------------
$ git checkout c6762a1 -- 1.py
~~~

然后重新add commit

~~~git
$ git add 1.py
$ git commit -m "back to change 1 and add comment for 1.py"
$ git log --oneline

# 输出
47f167e back to change 1 and add comment for 1.py
904e1ba change 2
c6762a1 change 1
13be9a7 create 1.py
~~~

可以看出, 不像 `reset` 时那样, 我们的 `change 2` 并没有消失, 但是 `1.py` 却已经回去了过去, 并改写了未来.

## 五、github在线代码管理

#### 5.1 创建一个github库

在 github/gitee注册一个 账户, 这个不用我多说, 大家都知道注册.

然后添加你的一个 online 版本库 repository:

#### 5.2 连接本地版本库

使用这节内容的初始例子文件, 然后将本地的版本库推送到网上:

~~~git
# 关联git仓库
$ git remote add origin https://github.com/MorvanZhou/git-demo.git
# 将你的仓库和你的github库合并了
$ git pull --rebase origin master
# 将本地仓库推送到远程仓库
$ git push origin master
~~~

#### 5.3 推送修改

如果在本地再进行修改, 比如在 `1.py` 文件中加上 `# happy github`, 然后 `commit` 并推上去:

~~~git
$ cd D:/xx研究生文件集合/code 文件
$ git remote add origin https://github.com/MorvanZhou/git-demo.git
$ git add .
$ git commit -am "诊断代码"
$ git pull --rebase origin master
$ git push -u origin master
~~~