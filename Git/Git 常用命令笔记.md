---
tags: [Git]
title: Git 常用命令笔记
created: '2021-01-11T03:52:58.176Z'
modified: '2021-02-01T03:40:57.037Z'
---

# Git 常用命令笔记

## 常用
一个文件被改后，想恢复到之前的
`$git checkout XXX`

## 切换分支
1）首先通过
`$ git branch -a `

来查看所在目录的分支
```
$ git branch -a
  master
* trunk
  remotes/origin/HEAD -> origin/master
  remotes/origin/master
  remotes/origin/zhanghanlun
```

2)然后输入命令切换分支
`$ git checkout -b zhanghanlun origin/zhanghanlun`

切换到origin/zhanghanlun分支命令本地分支为”zhanghanlun”


