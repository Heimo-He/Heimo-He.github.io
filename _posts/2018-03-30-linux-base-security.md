---
title: 服务器安全系列之基础安全配置
categories: Linux
tags: 服务器安全
---

## 创建新用户
在之前的指引中，创建了 root 帐号，并都使用 root 登陆 Linux。但作为最高权限的用户 root，可以执行任何命令，稍加不慎误执行了命令，就会破坏系统。因此，我们建议你创建另一个新的帐号，并使用他登陆服务器，执行任何操作。当使用新帐号登陆后，你可以通过 sudo 命令来获得超级用户的权限。

如何增加新用户，通过 SSH 登陆 Linux 系统，不同版本的 Linux 使用命令也略有不同，如下：

<!-- more -->

**CentOS / Fedora**

使用以下命令创建新帐号，使用你想增加的用户名，替换 exampleuser :
`adduser exampleuser`

为新用户设置密码:
`passwd exampleuser`

增加新用户到 wheel 用户组，以获得执行 sudo 的权限：CentOS 7 / Fedora
`usermod exampleuser -a -G wheelCentOS 6`
`usermod -a -G wheel exampleuser`

**Debian / Ubuntu**

使用以下命令创建新帐号，使用你想增加的用户名，替换 exampleuser :
`adduser exampleuser`

增加新用户到 wheel 用户组，以获得执行 sudo 的权限：
`usermod -a -G sudo exampleuser`

当新用户创建后，退出 root 登陆:
`logout`

重新使用刚刚创建的新用户登陆系统. 使用你的新用户名替换下方的 exampleuser ，以及你的实际 IP 地址替换 123.456.78.90:
`ssh exampleuser@123.456.78.90`

现在你就已经使用了新账号登陆 Linux，而不是危险的 root帐号。当然，在需要执行超级用户（root）才有权限的命令时，在命令前面加上 `sudo` 即可。例如，你可以执行 `sudo iptables -L` 去查看 iptables 的配置。另外，所有使用 sudo 执行的命令都将记录在` /var/log/auth.log` 这份日志中，如果被入侵了，可以查查这份日志，也许能有所发现。

## 使用 SSH 密钥认证

前面你连接 SSH 登陆系统，都是通过密码认证的方式，不得不提，有一种更加安全的方式：密钥认证。这部分，我们会教你如何生成公钥和私钥，并上传私钥到你的 Linux 主机。SSH 连接将会匹配远程机器的公钥以及存储在本地机器中的私钥，以认证身份，而且登陆的过程不需要输入任何密码。最后，去掉 SSH 密码登陆的方式，你的系统已经不惧任何暴力破解了。

下面是如何使用 SSH 密钥连接 Linux：
在本地 Linux / Mac 机器可以通过在终端中输入以下命令，生成 SSH 密钥。Windows 的 PuTTY 用户请看其他教程。
`ssh-keygen`

当输入命令后，会出现引导提示，按照提示，输入私钥默认存放位置，回车即使用括号内的默认地址。

```
Generatingpublic/privatersa key pair.
Enterfileinwhich to save the key(/home/xxx/.ssh/id_rsa):
```

接着询问，输入『通行短语』，可以是一句话，相比密码更长，当然偷懒的话，也可以直接回车，不使用通行短语。

```
Enter passphrase (empty for no passphrase):
```

重复输入，这样就完成了 SSH 密钥的生成。

***注意：两个密钥文件已经创建，默认路径在 \~/.ssh文件夹中，两个文件分别是: 私钥（id_rsa）和 公钥（id_rsa.pub）.公钥需要上传到远程 Linux 服务器中，另一个文件则藏好，莫被别人偷去!***

使用 SCP 命令上传公钥到远程服务器(非root用户)：
`scp ~/.ssh/id_rsa.pub example_user@123.456.78.90:`

如果远程服务器上没有~/.ssh 这个目录，就创建一个 ssh 目录(非root用户操作)
`mkdir .ssh`

将上传上去的公钥放到这个目录中(非root用户操作)：
`mv id_rsa.pub .ssh/authorized_keys`

修改 .ssh 的所有者（注意：所有者不要是 root 帐号）和执行权限（仅所有者可读写）：
`chown -R example_user:example_user .ssh`
`chmod 700 .ssh`
`chmod 600 .ssh/authorized_keys`

现在SSH 的密钥已经生成，万事俱备，再试试 SSH登陆看，是不是直接无密码进入了呢？
但是这样的防护还不够完美，如果 root 密码被泄露或者被暴力破解，那么系统就会完全置于攻击者的股掌之内。
所以，还是接着往下读。

## 关闭密码SSH登陆和 Root 帐号登陆

登陆远程服务器，并编辑 SSH 配置文件（`/etc/ssh/sshd_config` and `/etc/ssh/ssh_config`）：

选项 PasswordAuthentication 的意思是SSH 登陆是否使用密码验证的方式，改为 no，即关闭密码验证。
`PasswordAuthentication no`

PermitRootLogin：是否运行 root 登陆，同样的改为『no』：
`PermitRootLogin no`

保存修改，并重启 SSH 服务：Debian/Ubuntu Users:
`sudo service ssh restartFedora/CentOS:`
`sudo systemctl restart sshd`

重启后修改才会生效。

[原文地址：https://www.ivpser.com/security-basic-settings/](https://www.ivpser.com/security-basic-settings/)
