title: "配置安卓开发环境"
date: 2015-04-22 00:06:52
tags: Android
category: [Java, Android]
---

> TBD

## 安装JDK

下载最新的Java JDK进行安装，病加入系统环境变量

* 我的电脑->属性->高级->环境变量->系统变量中添加以下环境变量

* JAVA_HOME值为：安装JDK的目录， 我的为C:\Program Files\Java\jdk1.8.0_05(安装时候有路径选择，复制下来即可)

* Path: 在开始追加 .;%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;(注意前面的".;"要加上，如果没有则新建)