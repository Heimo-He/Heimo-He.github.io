---
title: 使用锐速破解版 为ss加速
categories: youknow
tags: youknow
---

> 锐速不支持Openvz。

> 如果内核版本在4.9以上可以[ >> 开启bbr](https://heimo-he.github.io/youknow/2018/03/30/open-tcp-bbr/)，不使用锐速。

<!-- more -->

## 安装锐速破解版

```bash
wget -N --no-check-certificate https://github.com/91yun/serverspeeder/raw/master/serverspeeder.sh && bash serverspeeder.sh
```

## 卸载锐速破解版

```bash
chattr -i /serverspeeder/etc/apx* && /serverspeeder/bin/serverSpeeder.sh uninstall -f
```

## 锐速Debian下重启命令

```bash
/serverspeeder/bin/serverSpeeder.sh restart
```



> ***转载使用注明出处。原文链接 [https://heimo-he.github.io/youknow/2018/03/30/shadowsocks-serverSpeeder/](https://heimo-he.github.io/youknow/2018/03/30/shadowsocks-serverSpeeder/)***