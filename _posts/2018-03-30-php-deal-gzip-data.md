---
title: PHP处理gzip压缩后的数据
categories: php
tags:
 - php
 - gzip
---

## 取消gzip

把 `"Accept-Encoding: gzip,deflate\r\n"; `去掉

## php内置方法解压

```php
$content = substr($content, 10);
$content = gzinflate($content));
```

<!-- more -->

## curl自带方法**

```php
curl_setopt( self::$ch, CURLOPT_ENCODING, 'gzip' );
```