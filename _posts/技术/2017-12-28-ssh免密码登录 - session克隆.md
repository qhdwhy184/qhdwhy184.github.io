---
layout: post
title: ssh免密码登录 - session克隆
category: 技术
tags: ssh,login
keywords: ssh,session克隆
---

[ssh自动登录 | ssh免密码登录](https://qii404.me/2016/07/29/ssh-clone-session.html)

vi ~/.ssh/config, 没有的话则创建一个，将下面的内容写入

```
host *
ControlMaster auto
ControlPath ~/.ssh/master-%r@%h:%p
```

第一次登陆时放心的ssh qishibo@10.10.10.10输入你的密码，然后新打开一个tab，再 ssh qishibo@10.10.10.10 , 看看还需要密码么？是不是直接就登陆进去了~

其实他会在~/.ssh目录中生成一个类似master-qishibo@10.10.10.10:21的文件，记录的就是你已经登陆过这个连接了，姑且把它看成Cookie吧，下次开新tab就能直接免密码了。

