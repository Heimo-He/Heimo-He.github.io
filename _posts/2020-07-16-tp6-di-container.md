---
title: TP6的容器和依赖注入实践
categories: php
tags:
 - php
 - tp6
 - 依赖注入
---


> **[TP6官方手册](https://www.kancloud.cn/manual/thinkphp6_0/1037489)**

#### 创建两个类Foo和Bar，Foo中调用Bar中的函数


```php
class Bar
{
    public function index(String $name){

        return 'Bar_'.$name;
    }
}
```

```php

class Foo{

    public function index(String $name){
        $bar = new Bar();

        return 'Foo_'.$bar->index($name);
    }
}
```

#### 依赖注入

##### 构造函数注入

```php
class Bar
{
    public function index(String $name){

        return 'Bar_'.$name;
    }
}
```

```php
class Foo{
    
    protected $bar;

    //构造函数注入
    public function __construct(Bar $bar) {
        $this->bar = $bar;
    }


    public function index(String $name){
    
        return 'Foo_'.$this->bar->index($name);
    }
}
```

##### 使用invoke助手函数

```php
$bar = invoke('namespace\Bar');
```

##### 操作方法注入

```php
//假设Foo为Controller，index为操作方法
public function index(String $name, Bar $bar){
    
    return 'Foo_'.$bar->index($name);
}
```

#### 先使用bind，再使用app助手函数调用容器

##### bind手动绑定类到容器中，支持多种绑定方式

- **在服务类的register方法中进行绑定**

```php
<?php
declare (strict_types = 1);

namespace app;

use think\Service;

/**
 * 应用服务类
 */
class AppService extends Service
{
    public function register()
    {
        // 服务注册
        bind('bar', 'namespace\Bar');
    }

    public function boot()
    {
        // 服务启动
    }
}

```

- **在app目录下面定义provider.php文件中进行批量绑定**

```php
<?php
use app\ExceptionHandle;
use app\Request;

// 容器Provider定义文件
return [
    'think\Request'          => Request::class,
    'think\exception\Handle' => ExceptionHandle::class,
    
    'bar'   => namespace\Bar::class,
];

```

##### app助手函数进行容器中的类解析调用，对于已经绑定的类标识，会自动快速实例化

```php
$bar = app('bar');
```

##### 对象化调用

```php
app()->bar()->index($name);
```




> ***转载使用注明出处。原文链接 [https://heimo-he.github.io/php/2020/07/16/tp6-di-container/](https://heimo-he.github.io/php/2020/07/16/tp6-di-container/)***