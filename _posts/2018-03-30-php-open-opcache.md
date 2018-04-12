---
title: PHP打开OPcache
categories: php
tags: php
---

> OPcache 通过将 PHP 脚本预编译的字节码存储到共享内存中来提升 PHP 的性能， 存储预编译字节码的好处就是 省去了每次加载和解析 PHP 脚本的开销。

> PHP 5.5.0 及后续版本中已经绑定了 OPcache 扩展。 对于 PHP 5.2，5.3 和 5.4 版本可以使用 [» PECL](http://pecl.php.net/package/ZendOpcache) 扩展中的 OPcache 库。

<!-- more -->

## 在php.ini中加入

```
; 开关打开
opcache.enable=1
opcache.enable_cli=1
opcache.interned_strings_buffer=8
opcache.max_accelerated_files=10000
opcache.memory_consumption=128
opcache.save_comments=1
opcache.revalidate_freq=1
zend_extension="opcache.so"
```

## 重启php-fpm




> ***转载使用注明出处。原文链接 [https://heimo-he.github.io/php/2018/03/30/php-open-opcache/](https://heimo-he.github.io/php/2018/03/30/php-open-opcache/)***