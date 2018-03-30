---
title: 开启 TCP BBR
categories: you know
tags: 
 - linux
 - you know
---

## 检查内核

```bash
uname -r
```

输出如下

```bash
4.9.0-4-amd64
```

检查内核版本大于4.9即可

<!-- more -->

## 修改系统变量

```bash
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
sysctl -p
```

输出如下

```bash
fs.file-max = 65535
net.ipv4.tcp_congestion_control = bbr
```
    
## 执行修改

```bash
sysctl net.ipv4.tcp_available_congestion_control
```

输出如下

```bash
net.ipv4.tcp_available_congestion_control = bbr cubic reno
```

## 检查是否开启

```
lsmod | grep bbr
```

输出如下

```
tcp_bbr                20480  0
```