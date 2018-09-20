---
layout: post
title: JVM debug学习
category: 技术
tags: JVM
keywords: 
description: 
---



### 查看dump
gdb $java_home/bin/java core.java.1537168663

### 查看调用栈
(gdb) backtrace

### Some useful article
[Extracting memory and thread dumps from a running JRE based JVM](https://medium.com/@chamilad/extracting-memory-and-thread-dumps-from-a-running-jre-based-jvm-26de1e37a080)

[Analysing a Java Core Dump](http://fahdshariff.blogspot.com/2012/08/analysing-java-core-dump.html)

[gdb Debugging Full Example (Tutorial): ncurses](http://www.brendangregg.com/blog/2016-08-09/gdb-example-ncurses.html)

