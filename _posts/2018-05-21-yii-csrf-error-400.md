---
title: Yii2 post表单csrf验证失败返回400报错 问题记录
categories: php
tags:
 - php
 - yii
 - csrf
---

### 问题特征

异常抛出为csrf验证失败，返回400

我们测试环境无法复现

但是客户的生产和测试环境偶尔出现，并且个别管理员会频繁出现

<!-- more -->

### 解决过程

##### 通过burp抓包发现，同一会话下，两次不同表单post提交中，第一次为200，第二次为400。发现两次提交中cookie中的_csrf值为不同的

##### 追代码发现，个别action行为中因为业务敏感性会存在`Yii::$app->request->getCsrfToken(true);`强制更新csrf_token的操作。查看源码发现，参数为TRUE会执行`$this->generateCsrfToken();`。

##### 进一步查看源码发现`generateCsrfToken()`方法中会更新cookie中的_csrf

```php
$cookie=$this>createCsrfCookie($token);
Yii::$app->getResponse()->getCookies()->add($cookie);
```

##### 确定Yii2中csrf的验证机制

生成token并保存在COOKIE['_csrf']中，那么每一次在访问时，就会以这个token作为一个基准进行数据加密随意生成一个csrfToken，然后返回给表单中。当post数据过来的时候，就得将这个csrfToken传递过来，然后进行解密，再和COOKIE['_csrf']的token进行比较，那么如果相等就说明访问是无攻击性的，是本站的访问。如果访问不通过，说明可能删改了一些信息，是不安全的。

> **到这里可以初步确定，可能存在强制更新csrf操作，导致表单提交时cookie中的_csrf和表单中的_csrf不匹配的情况，导致400**

### 验证

登录用户后，

1.打开一个表单页面A，记录表单中的_crsf值和cookie中的_csrf值

2.打开一个提交后会强制更新csrf的表单B，记录表单中的_crsf值和cookie中的_csrf值

3.先提交表单B，后提交表单A

抓包结果如下：

B表单成功提交，同时cookie中的_csrf值被更新

A表单提交返回400，页面中的cookie中的_csrf值被更新为和B页面被更新的cookie中的_csrf值


**验证通过**

### 新的问题

后台请求前台应用静态资源时，发现存在set-cookie:_csrf

导致cookie中的_csrf被更新，后续的post表单提交都400


### 解决过程

> **初步确定，请求静态资源时，会更新cookie中的_csrf，Nginx肯定将静态资源请求转发给了php，然后Yii初始化了cookie中的_csrf。其次，后台的cookie会被前台更新，表示同源，因为客户前后台使用了相同的域名(不同的端口)。**

### 验证

1.检查Nginx配置文件，如下：

```bash
location / {

...

}
location ~ \.php$ {

...

}
```

**没有针对静态资源的location**，静态资源获取404的时候，Nginx会继续匹配location，将请求发给php，然后Yii初始化了cookie中的_csrf。

2.如果构造一个url在浏览器中请求静态资源，返回的是前端应用渲染出的404页面

**验证通过**

### 解决办法

1.使用本地host去解析到后台应用的ip，避免同源

2.申请一个子域名，用于后台访问，避免同源

3.为Nginx增加静态资源配置





> ***转载使用注明出处。原文链接 [https://heimo-he.github.io/php/2018/05/21/yii-csrf-error-400/](https://heimo-he.github.io/php/2018/05/21/yii-csrf-error-400/)***