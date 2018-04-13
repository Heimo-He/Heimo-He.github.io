---
title: PHP7的变化
categories: php
tags:
 - php
 - php7内核剖析
---

> php7相对php5变化非常大，尤其是Zend引擎方面。运行速度相比php5.0~5.6快了近5倍，同时还降低了php对系统资源的占用。下面简单介绍php7比较大的几个变化。

<!-- more -->

### 抽象语法树

- 之前的版本中，php代码在语法解析阶段直接生成了ZendVM指令，也就是zend_language_parser.y中生成opline指令，使得编译器与执行器耦合在一起。如果修改语法解析规则，则相应的指令生成规则也要修改。
- php7中增加了抽象语法树。首先将PHP代码解析成抽象语法树，然后将抽象与法树编译成ZendVM指令。抽象语法树的加入使PHP的编译器与执行器很好的隔离开。编译器不需要关心指令生成规则，执行器不需要关心语法解析规则。

### Native TLS

- 之前的版本中，php中线程使用全局资源需要先获取本线程的资源池，这个过程比较占用时间。因此，php5.x通过参数传递的方式将本线程的资源池传递给其它函数，避免重复查找。这样会使几乎所有的函数都要加上接收资源池的参数，不仅容易遗漏，而且这种方式极不优雅。
-  php7中使用了Native TLS（线程局部存储）来保存线程的资源池。简单来说就是通过__thread 标识一个全局变量，这样这个全局变量就是线程独享的了，不同线程的修改不会相互影响。

### 指定函数参数、返回值类型

- php7中可以指定函数参数及返回值的类型，如下。如果实际参数、返回值的类型和指定的不同，会报error错误。

```php
function test(string $str): array{
	return [];
}
```

### zval结构的变化

zval是php中非常重要的一个结构，他是php变量的内部结构，也是php内核中应用最普遍的一个结构。

- 在php5.x中，zval的结构如下。php5.x中变量的引用计数存放在refcount__gc中，这样一来，导致变量复制时需要复制两个结构。

```php
struct _zval_struct {
	/* Variable inFormation*/
	zvalue_value value;    /* value */
	zend_uint refcount__gc;
	zend_uchar type;    /* active yupe */
	zend_uchar is_ref__gc;
};
```

- php7中将引用计数转移到了具体的value中，这样更合理。因为zval只是变量的载体，可以简单的理解为变量名，而value才是真正的值，这个改变使得php变量复制，传递更加简洁，易懂。并且zval结构的大小也从24byte减少到16byte，这是php7能够降低系统资源占用的一个优化点所在。

### 异常处理

```php
try {
	test();
}catch(Throwable $e){
	echo $e->getMessage();
}
```
- php5.x中很多操作直接会抛出error错误。比如调用一个不存在的函数，php5.x中报"PHP Fatal error: Call to undefined function test()"。
- php7中将多数错误改为了异常抛出，这样可以使用try catch捕获，使得错误处理更加可控。如上代码中可以通过Throwable异常类型进行捕获。

### HashTable的变化

HashTable是PHP中强大的array()类型的内部实现结构，也是内核中使用非常频繁的一个结构。

- php7中HashTable结构的大小从72byte减小到56byte，同时数组元素Bucket结构也从72byte减小到32byte。

### 执行器

执行器的调度函数为execute_ex()，这个函数负责执行php代码编译生成的ZendVM指令。在执行期间会频繁调用execute_data、opline两个变量。

- php5.x中这两个变量是由execute_ex()通过参数传递给各指令handler的。
- php7中使用寄存器来存储这两个变量，避免了传参导致的频繁出入栈操作，同时寄存器相比内存的访问速度更快。这个优化使得php的性能提升了5%左右。

### 新的参数解析方式

- php5.x通过zend_parse_parameters()解析 函数的参数。
- php7中保留了原来的方式，提供了一种新的参数解析方式，新的解析方式速度更快。具体请查看《PHP7内核剖析》第10章。



> 本文整理于《PHP7内核剖析》

> ***转载使用注明出处。原文链接 [https://heimo-he.github.io/2018/04/02/php-php7-kernel-01/](https://heimo-he.github.io/2018/04/02/php-php7-kernel-01/)***