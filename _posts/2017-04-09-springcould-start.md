---
layout: post
title:  "spring cloud 开发笔记"
date:   2017-04-09 13:36:00
categories: spring-cloud micro-service
---

# 准备开发环境

## 使用 sdk man 安装 gradle

	curl -s "https://get.sdkman.io" | bash
	sdk install gradle
	
安装结束后，gradle 的安装路径在
	
	maven { url 'http://maven.aliyun.com/nexus/content/groups/public/'}
	
## 使用阿里的 maven 仓库

	maven { url 'http://maven.aliyun.com/nexus/content/groups/public/'}
	
## 用 start.spring.io 创建一个初始项目

访问 http://start.spring.io 按照提示创建初始项目模板，下载后解压开。

## 记得配置 spring-boot-start-web 依赖

更多的spring boot 文档可以参考下面：

http://docs.spring.io/spring-boot/docs/1.5.2.RELEASE/reference/htmlsingle

