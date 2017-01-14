---
layout: post
title:  "实现一个自动生成依赖jar包的 gradle 插件"
date:   2017-01-06 14:00:00
categories: gradle auto-build-dependent-projects
---

# 用到的 Groovy 语法

## Groovy 教程

[Fluently Groovy](http://www.ibm.com/developerworks/java/tutorials/j-groovy/j-groovy.html)
[Groovy meta-programming](http://www.ibm.com/developerworks/java/library/j-pg06239/index.html)

# 编写 gradle 插件的方法

[Writing Custom Plugins](https://docs.gradle.org/current/userguide/custom_plugins.html)

# 自动检出 git 项目

# 获取当前项目的 git 分支名

# 让 git 忽略 'libs/' 下的项目目录，但要加入 *.jar。

.gitignore 的配置方法可以用

    !libs/*.jar
    libs/*