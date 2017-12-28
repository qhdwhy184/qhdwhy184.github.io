---
layout: post
title: ssh免密码登录 - session克隆
category: 技术
tags: ssh,login
keywords: ssh,session克隆
---

引用来源：[ssh自动登录 | ssh免密码登录](https://qii404.me/2016/07/29/ssh-clone-session.html)

vi ~/.ssh/config, 没有的话则创建一个，将下面的内容写入

```
host *
ControlMaster auto
ControlPath ~/.ssh/master-%r@%h:%p
```

第一次登陆时放心的ssh qishibo@10.10.10.10输入你的密码，然后新打开一个tab，再 ssh qishibo@10.10.10.10 , 看看还需要密码么？是不是直接就登陆进去了~

其实他会在~/.ssh目录中生成一个类似master-qishibo@10.10.10.10:21的文件，记录的就是你已经登陆过这个连接了，姑且把它看成Cookie吧，下次开新tab就能直接免密码了。


引用来源：[SSH原理与运用（一）：远程登录](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html)

#### 公钥登录

使用密码登录，每次都必须输入密码，非常麻烦。好在SSH还提供了公钥登录，可以省去输入密码的步骤。

所谓"公钥登录"，原理很简单，就是用户将自己的公钥储存在远程主机上。登录的时候，远程主机会向用户发送一段随机字符串，用户用自己的私钥加密后，再发回来。远程主机用事先储存的公钥进行解密，如果成功，就证明用户是可信的，直接允许登录shell，不再要求密码。

这种方法要求用户必须提供自己的公钥。如果没有现成的，可以直接用ssh-keygen生成一个
```
　　$ ssh-keygen
```

运行上面的命令以后，系统会出现一系列提示，可以一路回车。其中有一个问题是，要不要对私钥设置口令（passphrase），如果担心私钥的安全，这里可以设置一个。

运行结束以后，在$HOME/.ssh/目录下，会新生成两个文件：id_rsa.pub和id_rsa。前者是你的公钥，后者是你的私钥。

这时再输入下面的命令，将公钥传送到远程主机host上面：
```
　　$ ssh-copy-id user@host
```

好了，从此你再登录，就不需要输入密码了。

如果还是不行，就打开远程主机的/etc/ssh/sshd_config这个文件，检查下面几行前面"#"注释是否取掉。
```
　　RSAAuthentication yes
　　PubkeyAuthentication yes
　　AuthorizedKeysFile .ssh/authorized_keys
```

然后，重启远程主机的ssh服务。
```
　　// ubuntu系统
　　service ssh restart

　　// debian系统
　　/etc/init.d/ssh restart
```