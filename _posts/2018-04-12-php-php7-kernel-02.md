---
title: PHP的构成
categories: php
tags:
 - php
 - php7内核剖析
---

php源码下有几个主要目录：sapi、main、Zend、ext、TSRM

- sapi：应用接口层
- main：php主要的代码
- Zend：php解析器的主要实现，即ZendVM，它是php代码的核心实现，php代码的解释，执行就是由Zend完成的。
- ext：php的扩展目录
- TSRM：线程安全的相关实现

<!-- more -->

php各个组成之间的关系如下图所示：

![](https://ws1.sinaimg.cn/large/005H70QEgy1fq9ovy70g6j307t07s74g.jpg)

### SAPI

php提供了一个SAPI层以适配不同的应用环境，SAPI可以认为是php的宿主环境。它是整个php框架最外层的部分，它主要负责php框架的初始化工作。PHP通过SAPI提供了一组接口，供应用和PHP内核之间进行数据交互。脚本执行的开始都是以SAPI接口实现开始的。只是不同的SAPI接口实现会完成他们特定的工作。

PHP提供很多种形式的接口，包括apache、apache2filter、apache2handler、caudium、cgi 、cgi-fcgi、cli、cli-server、continuity、embed、isapi、litespeed、milter、nsapi、phttpd pi3web、roxen、thttpd、tux和webjames。但是常用的只有5种形式，CLI/CGI（命令行）、Multiprocess（多进程）、Multithreaded（多线程）、FastCGI和Embedded（内嵌）。

如果SAPI是个独立的应用程序，那么main函数也将定义在SAPI中。

### ZendVM

ZendVM是一个虚拟的计算机，它介于PHP应用和实际计算机中间。它是php语言的核心实现，主要由两部分组成：

- 编译器：负责将php代码解释为执行器可执行的指令
- 执行器：负责执行编译器解释出的指令

ZendVM的角色等价于JVM，它们都是抽象出来的虚拟计算机，与C/C++这类编译型语言不同，虚拟机上运行的指令并不是机器指令。虚拟机的优点是跨平台，主需要按照不通的平台编译出对应的解析器就可以实现代码的跨平台执行。

如果想深入理解php，那么ZendVM就是最主要的目标。

### Extension

扩展是php内核提供的一套用于扩充php功能的一种方式。通过扩展，我们可以使用C/C++实现更强大的功能和更高的性能，这样使得php和C/C++非常相近，甚至可以再C/C++中应用中把php嵌入座位第三方库使用。

扩展分为php扩展、Zend扩展。php扩展比较常见，而Zend扩展主要应用于ZendVM，它可以做的东西更多，比如Opcache就是Zend扩展。



> 本文整理于《PHP7内核剖析》
> ***转载使用注明出处。原文链接 [https://heimo-he.github.io/php/2018/04/02/php-php7-kernel-02/](https://heimo-he.github.io/php/2018/04/02/php-php7-kernel-02/)***