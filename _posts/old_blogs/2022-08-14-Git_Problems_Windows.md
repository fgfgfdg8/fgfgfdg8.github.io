---
layout: post
title: 【技术备忘录】Git使用过程问题记录
date: '2022-08-14 11:59:13 +0800'
description: 这是本人在Widnows平台下学习使用Git的过程中进行的记录，用于记录自己在学习实践过程中遇到的问题，会随着时间慢慢更新。
categories: [Technical, Memorandum]
tags: [memorandum, embedding technique, archive]     # TAG names should always be lowercase
toc: true
comments: true
pin: false
math: true
mermaid: true
published: true
sitemap: false
image:
---

## 前言
这是本人在Widnows平台下学习使用Git的过程中进行的记录，用于记录自己在学习实践过程中遇到的问题，会随着时间慢慢更新。

## 1. Git Bash无回显

**Q:**在Git Bash窗口中键入命令时，窗口没有回显，但是文字可以正常键入，且命令可以正常执行，如何解决？

**A:**在Git Bash窗口中输入`reset`命令，回车即可解决。该`reset`指令仅会重启Git，不会影响原先的配置文件。

## 2. 在本地建立仓库，并且推送到远程仓库

**Q:**如何在本地新建一个Git仓库，并且推送到远程仓库（如Gitee）？

**A:**在Git Bash中依次执行以下命令（命令来源Gitee官方新建仓库示例）：

​	简易的命令行入门教程:

​	Git 全局设置:

```
git config --global user.name "你的用户名"
git config --global user.email "你的邮箱"
```

​	创建 git 仓库:

```
mkdir 本地git仓库目录名
cd 本地git仓库目录名
git init 
touch README.md
git add README.md
git commit -m "first commit"
git remote add origin https://gitee.com/你的用户名/远程仓库名.git
git push -u origin "master"
```

​	已有仓库?

```
cd existing_git_repo
git remote add origin https://gitee.com/你的用户名/远程仓库名.git
git push -u origin "master"
```

## 3. 撤回上一次提交

**Q:** 提交完更改后悔了，如何撤回这次提交？

**A:** 可以撤回提交的命令：
（以下部分内容有转载部分，且有部分自己改写）
> 作者：明谣_罗潇
> 链接：https://www.jianshu.com/p/d43594deb0a8
> 来源：简书
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
1. 已经Push到远程仓库的情况
> 注意：撤回上次提交后，本地和远程仓库中的数据都将删除，所以撤回上次提交前，一定要记得备份数据！！！

（1）使用reset命令，直接删除上次提交。

```undefined
git reset --hard HEAD^

git push origin master -f  或  git push -f
```

HEAD是指向最新的提交，上一次提交是HEAD^, 上上次是HEAD^^,也可以写成HEAD～2 ,以此类推。
（2）使用revert命令

```undefined
git revert HEAD

git push origin master
```
两者差别：
revert：是放弃指定提交的修改，但是会生成一次新的提交，需要填写提交注释，以前的历史记录都在；
reset：是指将HEAD指针指到指定提交，历史记录中不会出现放弃的提交记录。commit记录、merge时间线....都会消失，删除彻底。
2. 尚未Push到远程仓库的情况
如果还没有push到远程仓库，可以使用命令`git -reset --soft HEAD^`，即可回到上一次提交。