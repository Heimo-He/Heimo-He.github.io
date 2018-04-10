---
title: 阿里云申请免费https证书及开启http2
categories: http
tags: http
---

## 访问阿里云

[传送门](https://common-buy.aliyun.com/?spm=5176.2020520163.cas.4.dadd4e672GmJz&commodityCode=cas#/buy)

![Alt text](https://ws1.sinaimg.cn/large/005H70QEgy1fpupctwpeoj30zl0hrgns.jpg)

购买成功选择**文件形式**，**自动生成csr**

<!-- more -->

## 支付成功后

![Alt text](https://ws1.sinaimg.cn/large/005H70QEgy1fpupe4p1l3j30b10903zp.jpg)

## 按下图要求下载`fileauth.txt`文件，通过`ftp`或者`scp`命令上传至服务器web项目`.well-known/pki-validation`目录中。
**阿里云业务更新，如果是使用阿里云云解析的域名，会自动添加一条解析，省去了操作`fileauth.txt`文件的步骤**

![Alt text](https://ws1.sinaimg.cn/large/005H70QEgy1fpupes6yc2j319f0hsk7a.jpg)

## 点击**检查配置**，左侧显示配置正确，几分钟后，会收到短信等，配置成功

## 找到相应订单，点击**下载**

![Alt text](https://ws1.sinaimg.cn/large/005H70QEgy1fpupf7hb4tj31fb0be41j.jpg)

## 选择下载对应web服务器证书文件（这里为Nginx），上传证书文件到服务器，配置好Nginx，顺便开启http2
```
server {
    listen 443 http2 default_server;#开启http2
    server_name localhost;
    ssl on;
    root html;
    index index.html index.htm;
    ssl_certificate   cert/214297829420678.pem;
    ssl_certificate_key  cert/214297829420678.key;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    location / {
        root html;
        index index.html index.htm;
    }
}
```

![Alt text](https://ws1.sinaimg.cn/large/005H70QEgy1fpupg2viikj319m0hg420.jpg)

![Alt text](https://ws1.sinaimg.cn/large/005H70QEgy1fpuphaqq8jj317r0dn411.jpg)

## enjoy it;



> ***转载使用注明出处。原文链接 ![https://heimo-he.github.io/http/2018/03/30/aliyun-free-openssl/](https://heimo-he.github.io/http/2018/03/30/aliyun-free-openssl/)***