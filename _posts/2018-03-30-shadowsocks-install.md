---
title: vps中部署shadowsocks实现科学上网
categories: youknow
tags: youknow
---

> 考虑性能，这里只提供Python版本安装方法
> 操作系统为Debian，centos等请使用yum安装

<!-- mroe -->

## 安装python环境

```bash
apt-get install python-pip
apt-get install python-m2crypto
```

## 然后查看python版本，确认安装成功

```bash
python --version
```

## 安装shadowsocks

```bash
pip install shadowsocks
```

## 配置config文件

```bash
mkdir /etc/shadowsocks
vim /etc/shadowsocks/config.json
```

复制下面代码
```json
{
"server":"你的vps的ip",
"server_port":服务端启用的端口,
"local_port":客户端启用的端口,
"password":"密码",
"timeout”:600,
"method":"aes-256-cfb"
}
```

相应数据按自己实际情况修改

## 运行
```bash
ssserver -c /etc/shadowsocks/config.json -d start
ssserver -c /etc/shadowsocks/config.json -d stop
```