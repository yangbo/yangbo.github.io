---
layout: post
title:  "Eclipse的git插件 EGit 的一个bug"
date:   2017-02-14 09:10:00
categories: git eclipse bug
---

Egit是eclipse中用来使用git仓库的一个插件，在windows系统下，删除文件、对文件更名时可能因为
文件被其他进程占用而导致操作失败，这时 git 操作进行到中途，导致后续git操作出现问题，可能会因此
覆盖其他人已经提交的代码。
