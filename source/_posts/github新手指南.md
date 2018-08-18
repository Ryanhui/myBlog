---
title: github新手指南
date: 2017-12-24 15:48:55
tags: 分享
---

# 目标

本地项目文件夹推送到自己的github

# 方法
### （1） 先从github创建一个空的仓库

先从github创建一个空的仓库，并复制链接地址

### （2）初始化本地仓库，并提交内容到本地

1. touch README.md
2. git init
3. git add .
4. git commit -m ‘提交说明’

### （3）连接到远程仓库，并将代码同步到远程仓库

1. git remote add origin 远程仓库地址
2. git push -u origin master

### （4）继续修改本地代码，然后提交并推送到github

1. git add .
2. git commit -m ‘提交说明’
3. git pull
4. git push

[转载地址及详细内容](https://www.jianshu.com/p/6f3324e4f335)
 
 