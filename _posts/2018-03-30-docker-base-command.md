---
title: Docker 常用命令
categories: Linux
tags:
 - docker
 - linux
---

## 安装docker
```bash
curl -sSL https://get.docker.com/ | bash -x
```

## 卸载docker
```bash
sudo apt-get purge docker-ce
sudo apt-get autoremove --purge docker-ce
rm -rf /var/lib/docker
```
<!-- more -->

## 创建container
```bash
docker run --name nc -p 80:80 -d images
```

## 启动container
```bash
docker start <container id>
```

## 查看所有container
```bash
docker ps -a
```

## 停止所有的container
```bash
docker stop $(docker ps -a -q)
```

## 停止container
```bash
docker stop <container id>
```

## 删除所有container
```bash
docker rm $(docker ps -a -q)
```

## 查看当前所有images
```bash
docker images
```

## 要删除全部image
```bash
docker rmi $(docker images -q)
```

## 删除指定container
```bash
docker rmi <image id>
```

## 删除指定images
```bash
docker rmi <image id>
```

## 删除untagged images，也就是那些id为<None>的image
```
docker rmi $(docker images | grep "^<none>" | awk "{print $3}")
```



> ***转载使用注明出处。原文链接 ![https://heimo-he.github.io/linux/2018/03/30/docker-base-command/](https://heimo-he.github.io/linux/2018/03/30/docker-base-command/)***