title: 总有后悔药吃的git
date: 2018-04-28
tag:
 - git

photos:
 - /img/2017/5.jpg 

---

记录一些常用的git操作，以便回顾和查询。理解这些指令，觉得最重要的是理解Git的内部原理，比如Git的分布式版本控制，分清楚工作区、暂存区、版本库，还有就是理解Git跟踪并管理的是修改，而非文件。

<!--more-->

### 我的骚操作
重说三：mac系统“最近使用”里面的文件不能删，不能删，千万不要随便删。那天我以为这里(最近使用)的文件是对别的地方的拷贝，我一向都有洁癖，经常会删除电脑那些基本不用的东西或者是重复的东西，结果一删造成千古恨，至今为止我都还没处理完造成的影响，只能发现再处理，好在有git仓库的文件可以有后悔药吃，其他的也就只能哭了。

### 几个重要的名词
![GitHub set up](https://shuangmuyingzi.github.io/img/git.jpeg)

* Workspace：工作区
* Index / Stage：暂存区
* Repository：仓库区（或本地仓库）
* Remote：远程仓库

#### 设置

```
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```
#### 提交
git 跟踪的是修改，而不是文件
![GitHub set up](https://shuangmuyingzi.github.io/img/git2.png)

```
#将“当前修改”移动到暂存区(stage)
 git add a.txt
#将暂存区修改提交
 git commit -m "Add a.txt."
```
#### 查看状态

```
 git status
 //显示暂存区和工作区的差异
 git diff
```
#### 回退

```
# 放弃工作区修改
$ git checkout -- file.name
$ git checkout -- .

# 取消commit(比如需要重写commit信息)
$ git reset --soft HEAD

# 取消commit、add(重新提交代码和commit)
$ git reset HEAD
$ git reset --mixed HEAD

# 取消commit、add、工作区修改(需要完全重置)
$ git reset --hard HEAD
```
#### 记录

```
$ git reflog
$ git log
```
#### 删除

```
$ rm file.name
$ git rm file.name
$ git commit -m "Del"
```
#### 远程操作

```
$ git remote add origin git@github.com:shuangmuyingzi/loadingModule.git
# 第一次推送，-u(--set-upstream)指定默认上游
$ git push -u origin master
$ git push origin master
```
#### 克隆

```
$ git clone https://github.com/shuangmuyingzi/loadingModule.git
$ git clone git@github.com:shuangmuyingzi/loadingModule.git
```
#### 分支
创建分支

```
$ git branch [name]
```
切换分支

```
$ git checkout [name]
```
创建并切换分支

```
$ git checkout -b [name]
```
合并分支

```
$ git merge [name]
```
删除分支

```
$ git branch -d [name]
```
查看分支

```
$ git branch
```
#### 撤销
撤销本地修改

```
$ git checkout -- [filename]
$ git checkout -- .
```
重置暂存区和工作区，与上一次commit保持一致

```
$ git reset --hard ［filename］
```
#### Git fetch和pull的区别
Git中从远程的分支获取最新的版本到本地有这样2个命令：

##### git fetch：相当于是从远程获取最新版本到本地，不会自动merge

git fetch origin master 
git log -p master..origin/master 
git merge origin/master

以上命令的含义：

首先从远程的origin的master主分支下载最新的版本到origin/master分支上；然后比较本地的master分支和origin/master分支的差别；最后进行合并。上述过程其实可以用以下更清晰的方式来进行： 
git fetch origin master:tmp 
git diff tmp 
git merge tmp 
从远程获取最新的版本到本地的tmp分支上之后再进行比较合并

##### git pull：相当于是从远程获取最新版本并merge到本地

git pull origin master

上述命令其实相当于git fetch 和 git merge 在实际使用中，git fetch更安全一些。因为在merge前，我们可以查看更新情况，然后再决定是否合并结束。

参考链接：
https://aotu.io/notes/2015/11/17/Git-Commands/index.html






