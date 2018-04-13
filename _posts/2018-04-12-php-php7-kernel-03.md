---
title: PHP生命周期
categories: php
tags:
 - php
 - php7内核剖析
---

php的整个生命周期被划分为以下几个阶段：

1. 模块初始化阶段（modules startup）
2. 请求初始化阶段（request startup）
3. 执行脚本阶段（execute script）
4. 请求关闭阶段（request shutdown）
5. 模块关闭阶段（modules shutdown）

<!-- more -->

根据不同SAPIde实现，各阶段的执行情况会有一些差异。比如：

- cli模式下，每次执行一个脚本都会完整的经历这些阶段。
- FastCgi模式下，启动时执行一次模块初始化阶段，然后各个请求只经历请求初始化阶段、执行脚本阶段、请求关闭阶段几个阶段，在SAPI关闭时经历模块关闭阶段。各阶段执行顺序与对应的处理函数如下：

![](https://ws1.sinaimg.cn/large/005H70QEgy1fq9wk56rrej309r064750.jpg)

### 模块初始化阶段（modules startup）

这个阶段主要进行PHP框架、Zend引擎的初始化操作，入口函数为php_module_startup()。这个阶段一般只在SAPI启动时执行一次，对于Fpm而言，就是在Fpm的master进程启动时执行的。

![](https://ws1.sinaimg.cn/large/005H70QEgy1fq9wobt2goj30gf0gsgny.jpg)

- 激活 SAPI：sapi_activate()，初始化请求信息 SG(request_info)、设置读取 POST 请求的
- handler 等，在 module startup 阶段处理完成后将调用 sapi_deactivate()。
- 启动 PHP 输出：php_output_startup()。
- 初始化垃圾回收器：gc_globals_ctor()，分配 zend_gc_globals 内存。
- 启动 Zend 引擎：zend_startup()，主要操作包括：
	- 启动内存池 start_memory_manager()；
	- 设置一些 util 函数句柄（如 zend_error_cb、zend_printf、zend_write 等）；
	- 设置 Zend 虚拟机编译、执行器的函数句柄 zend_compile_file、zend_execute_ex，以及垃圾回收的函数句柄gc_collect_cycles；
	- 分配函数符号表（CG(function_table)）、类符号表（CG(class_table)）、常量符号表（EG(zend_constants)）等，如果是多线程的话，还会分配编译器、执行器的全局变量；
	- 注册 Zend 核心扩展：zend_startup_builtin_functions()，这个扩展是内核提供的，该过程将注册Zend核心扩展提供的函数，比如strlen、define、func_get_args、class_exists 等；
	- 注册 Zend 定义的标准常量：zend_register_standard_constants()，比如：E_ERROR、E_WARNING、E_ALL、TRUE、FALSE 等；
	- 注册$GLOBALS 超全局变量的获取 handler；
	- 分配 php.ini 配置的存储符号表：EG(ini_directives)。
- 注册 PHP 定义的常量：PHP_VERSION、PHP_ZTS、PHP_SAPI，等等。
- 解析 php.ini：解析完成后所有的 php.ini 配置保存在 configuration_hash 哈希表中。
- 映射 PHP、Zend 核心的 php.ini 配置：根据解析出的 php.ini，获取对应的配置值，将最终的配置插入 EG(ini_directives)哈希表。
- 注册用于获取$_GET、$_POST、$_COOKIE、$_SERVER、$_ENV、$_REQUEST、$_FILES变量的 handler。
- 注册静态编译的扩展：php_register_internal_extensions_func()。
- 注册动态加载的扩展：php_ini_register_extensions()，将 php.ini 中配置的扩展加载到 PHP中。
- 回调各扩展定义的 module starup 钩子函数，即通过 PHP_MINIT_FUNCTION()定义的函数。
- 注册 php.ini 中禁用的函数、类：disable_functions、disable_classes。

### 请求初始化阶段（request startup）

这个阶段是每个请求处理前都会经历的一个阶段，对于Fpm而言，是在worker进程accept一个请求且读取、解析完请求数据后的一个阶段。这个阶段的处理函数为php_request_startup()。

![](https://ws1.sinaimg.cn/large/005H70QEgy1fq9xef919bj30dm08vmyj.jpg)

主要的处理有以下几个。
- 激活输出：php_output_activate()。
- 激活 Zend 引擎：zend_activate()，主要操作如下所述。
	- 重置垃圾回收器：gc_reset()；
	- 初始化编译器：init_compiler()；
	- 初始化执行器：init_executor()，将 EG(function_table)、EG(class_table)分别指向CG(function_table)、CG(class_table)，所以在 PHP 的编译、执行期间，EG(function_table)与 CG(function_table)、EG(class_table)与 CG(class_table)是同一个值；另外还会初始化全局变量符号表 EG(symbol_table)、include 过的文件符号表EG(included_files)；
	- 初始化词法扫描器：startup_scanner()。
- 激活 SAPI：sapi_activate()。
- 回调各扩展定义的 request startup 钩子函数：zend_activate_modules()。

### 执行脚本阶段（execute script）

这个阶段包括php代码的编译，执行两个核心阶段，这也是Zend引擎最重要的功能。在编译阶段，php脚本将经历

php源码->抽象语法树->opline指令

的转化过程，最终生成的opline指令就是Zend引擎可以识别的执行指令，这些指令接着再被执行器执行，这就是php代码解释执行的过程。这个阶段的入口的函数为php_execute_script()。

![](https://ws1.sinaimg.cn/large/005H70QEgy1fq9xk1mqg8j30dw08tmy9.jpg)

### 请求关闭阶段（request shutdown）

这个阶段将flush输出内容、发送HTTP应答header头、清理全局变量、关闭编译器、关闭执行器等。另外，在该阶段将回调各个扩展的 request shutdown 钩子函数。这个阶段是请求初始化阶段的相反操作，与请求初始化时的处理一一对应。

![](https://ws1.sinaimg.cn/large/005H70QEgy1fq9xn5e9x8j30gf0hp41n.jpg)

### 模块关闭阶段（modules shutdown）

这个阶段在SAPI关闭时执行，与模块初始化阶段相对应。这个阶段主要是进行资源的清理、php各个模块的关闭操作，同时，将回调各个扩展的 module shutdown 钩子函数php_module_shutdown()。

![](https://ws1.sinaimg.cn/large/005H70QEgy1fq9xniffj1j30d408mq52.jpg)



> 本文整理于《PHP7内核剖析》

> ***转载使用注明出处。原文链接 [https://heimo-he.github.io/php/2018/04/02/php-php7-kernel-03/](https://heimo-he.github.io/php/2018/04/02/php-php7-kernel-03/)***