---
title: git 常用命令
tags: git
date: 2019/09/18
categories: git
---

# git 常用命令

初始化仓库

    git init

拉取远程仓库

    git clone <仓库地址>  //拉取时会在当前创建一个与远程仓库同名的文件夹

    git clone <仓库地址> <文件名>  //拉取时会在当前创建一个文件夹，然后将文件拉取到改文件夹下

更新远程仓库

    git fetch

更新并合并远程仓库到本地

    git pull

查看所有分支

    git branch -a

查看本地分支

    git branch

创建分支

    git checkout -b <分支名>

切换到本地分支分支

    git checkout <分支名>

切换到master

    git checkout master

拉取远程分支到本地

    git checkout -b <本地分支名> <远程分支名>

查看日志

    git log

远程仓库添加分支

    git  push origin <本地分支名>:<远程分支名>

删除远程分支

    git branch -r -d <远程分支名>  //慎重

关联当前分支到远程分支

    git branch -u <远程仓库分支名>

关联当前分支到远程分支另一种方式

    git branch --set-upstream-to=origin/<远程分支名>


查看远程仓库地址

    git remote -v

合并目标分支到当前分支

    git merge <目标分支名>






## 持续更新中。。。