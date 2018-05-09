---
title: 修改composer依赖包源代码操作方式
categories: php
tags:
 - php
 - composer
---

> 如果你需要修改依赖包的源码，你可以使用这样的操作方式，将不会影响composer的正常使用。

### 操作方式
#### 当你需要修改库的时候，克隆源代码就比下载包方便了。你可以使用--prefer-source来强制选择克隆源代码。

```bash
$ composer update windhoney/yii2-rest-rbac --prefer-source
```

<!-- more -->

#### 接下来你可以进行源代码修改操作

使用status -v进行源代码检查，会显示修改的内容

```bash
$ composer status -v

You have version variations in the following dependencies:
/Users/heimo/work/ga/vendor/windhoney/yii2-rest-rbac:
    From 1.0.3 to dev-Heimo/account_manage
```


#### 当你试图更新一个修改过的库的时候，Composer会提醒你，询问是否放弃修改：

```bash
$ composer update
Loading composer repositories with package information
Updating dependencies
  - Updating symfony/symfony v2.2.0 (v2.2.0- => v2.2.0)
    The package has modified files:
    M Dumper.php
    Discard changes [y,n,v,s,?]?
```

### composer相关命令：

1.更新 `update`

`--prefer-source`: 当有可用的包时，从 source 安装。

2.依赖包状态检测 `status`

如果你经常修改依赖包里的代码，并且它们是从 source（自定义源）进行安装的，那么 status 命令允许你进行检查，如果你有任何本地的更改它将会给予提示。

```bash
php composer.phar status
```

你可以使用 `--verbose` 系列参数（-v/vv/vvv）来获取更详细的详细：

```bash
php composer.phar status -v
  
You have changes in the following dependencies:
vendor/seld/jsonlint:
    M README.mdown
```
 

### 相关参考资料：

[PHP 开发者该知道的 5 个 Composer 小技巧](https://www.phpcomposer.com/5-features-to-know-about-composer-php/)
[命令行 | Composer 中文文档 | Composer 中文网](http://docs.phpcomposer.com/03-cli.html#status)
 



> ***转载使用注明出处。原文链接 [https://heimo-he.github.io/php/2018/05/09/composer-modify-code/](https://heimo-he.github.io/php/2018/05/09/composer-modify-code/)***