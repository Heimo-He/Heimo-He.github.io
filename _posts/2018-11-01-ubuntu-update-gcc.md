---
title: Ubuntu 更新gcc
categories: linux
tags:
 - linux
 - gcc
---

- **添加软件源**

`sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
 
##### 如果报错`add-apt-repository command not found`

`sudo apt-get install python-software-properties`
`sudo apt-get install software-properties-common`
`sudo apt-get update`

<!-- more -->

##### 如果是缺失依赖，则需要安装必要的依赖。

`sudo apt-get install software-properties-common`

- **更新**

`sudo apt-get update`
`sudo apt-get upgrade`

- **安装gcc**

#####  gcc4.8

`sudo apt-get install gcc-4.8 g++-4.8`

##### gcc4.9（推荐安装这个版本，不是最新，稳定。但能支持绝大多数的软件编译）

`sudo apt-get install gcc-4.9 g++-4.9`

##### gcc5

`sudo apt-get install gcc-5 g++-5`

- **查看版本号**

`gcc -v`

- **更新链接**

#####  gcc4.8

`update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.8 48 \--slave /usr/bin/g++ g++ /usr/bin/g++-4.8 \--slave /usr/bin/gcc-ar gcc-ar /usr/bin/gcc-ar-4.8 \--slave /usr/bin/gcc-nm gcc-nm /usr/bin/gcc-nm-4.8 \--slave /usr/bin/gcc-ranlib gcc-ranlib /usr/bin/gcc-ranlib-4.8`

#####  gcc4.9

`update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.9 49 \--slave /usr/bin/g++ g++ /usr/bin/g++-4.9 \--slave /usr/bin/gcc-ar gcc-ar /usr/bin/gcc-ar-4.9 \--slave /usr/bin/gcc-nm gcc-nm /usr/bin/gcc-nm-4.9 \--slave /usr/bin/gcc-ranlib gcc-ranlib /usr/bin/gcc-ranlib-4.9`

#####  gcc5

`update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-5 53 \--slave /usr/bin/g++ g++ /usr/bin/g++-5 \--slave /usr/bin/gcc-ar gcc-ar /usr/bin/gcc-ar-5 \--slave /usr/bin/gcc-nm gcc-nm /usr/bin/gcc-nm-5 \--slave /usr/bin/gcc-ranlib gcc-ranlib /usr/bin/gcc-ranlib-5`



> ***转载使用注明出处。原文链接 [https://heimo-he.github.io/macos/2018/11/01/ubuntu-update-gcc/](https://heimo-he.github.io/macos/2018/11/01/ubuntu-update-gcc/)***