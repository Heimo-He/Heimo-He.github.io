---
title: 执行composer 抛出Killed
categories: php
tags:
 - php
 - linux
---

- **报错如下**

```bash
vagrant@precise64:/work/usrc$ composer require "yiisoft/yii2":"~2.0.12.1"
./composer.json has been updated
The "extra.asset-installer-paths" option is deprecated, use the "config.fxp-asset.installer-paths" option
Loading composer repositories with package information
Updating dependencies (including require-dev)
Killed
```
<!-- more -->

- **解决办法**

内存过小，加大虚拟机内存



> ***转载使用注明出处。原文链接 ![https://heimo-he.github.io/php/2018/04/02/php-composer-killed/](https://heimo-he.github.io/php/2018/04/02/php-composer-killed/)***