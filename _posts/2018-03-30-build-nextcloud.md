---
title: vps中lnmp 搭建nextcloud私人云服务
categories: Linux
tags:
 - nextcloud
 - linux
---

> 搭建私有云同步服务。
> nextCloud由ownCloud团队开发，相比其他云服务，使用php编写，支持服务端加密存贮，支持多客户端，ui美观，功能完善，安装使用起来也很方便。
> 可以docker搭建，也可以手动lnmp下搭建。这里介绍lnmp下搭建步骤。

<!-- more -->

## 安装lnmp

参考[lnmp.org](lnmp.org)

环境细节配置不多赘述

## 下载源码

```bash
wget https://download.nextcloud.com/server/releases/nextcloud-12.0.5.zip
```

## 解压并授权，不授权会报错

```bash
unzip nextcloud-12.0.5.zip
chmod -R 777 nextcloud
```

## 配置nginx

```bash
server{
        listen 80;
        #这里用自己的域名，没有域名用就写localhost或者127.0.0.1
        server_name cloud.nextcloud.com;
        root /home/wwwroot/nextcloud;
        index index.php index.html index.htm;
        #不使用include enable-php.conf，因为会有伪静态问题，安装后页面404
        include enable-php-pathinfo.conf;
    }
```

## 访问安装界面
浏览器输入域名即可访问，没有域名用ip访问

![enter image description here](https://ws1.sinaimg.cn/large/005H70QEgy1fo6vfvt1dcj30kn0bxab4.jpg)