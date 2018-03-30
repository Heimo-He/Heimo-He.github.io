---
titile: PHP 似乎没有设置好查询的系统环境变量。 用 getenv(\"PATH\") 测试只返回一个空值
categories: php
tags: 
 - nextcloud
 - php
---

nextcloud安装完成后出现：“PHP 似乎没有设置好查询的系统环境变量。 用 getenv(\"PATH\") 测试只返回一个空值。“

<!-- more -->

## 编辑php-fpm.conf配置文件

**加入**

```bash
    env[HOSTNAME] = $HOSTNAME
    env[PATH] = /usr/local/bin:/usr/bin:/bin
    env[TMP] = /tmp
    env[TMPDIR] = /tmp
    env[TEMP] = /tmp
```

## 重启php-fpm