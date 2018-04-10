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
```




> ***转载使用注明出处。原文链接 ![https://heimo-he.github.io/youknow/2018/03/30/php-deal-gzip-data/](https://heimo-he.github.io/youknow/2018/03/30/php-deal-gzip-data/)***