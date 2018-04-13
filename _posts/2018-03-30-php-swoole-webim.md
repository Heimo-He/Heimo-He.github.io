---
title: Swoole实现基于websocket的webIM聊天室
catagories: php
tags:
 - swoole
 - php
 - websocket
---

> 这是一个简单的基于php Swoole扩展实现的聊天室demo。

[本文源码github地址](https://github.com/Heimo-He/webIM)

<!-- more -->

## 前言-浅析websocket

### 什么是websocket

> WebSocket协议是基于TCP的一种新的网络协议。它实现了浏览器与服务器全双工(full-duplex)通信——允许服务器主动发送信息给客户端。

### 为什么会有websocket

长久以来, 创建实现客户端和用户端之间双工通讯的web app都会造成HTTP轮询的滥用: 客户端向主机不断发送不同的HTTP呼叫来进行询问。

这会导致一系列的问题：

1.服务器被迫为每个客户端使用许多不同的底层TCP连接：一个用于向客户端发送信息，其它用于接收每个传入消息。

2.有线协议有很高的开销，每一个客户端和服务器之间都有HTTP头。

3.客户端脚本被迫维护从传出连接到传入连接的映射来追踪回复。

一个更简单的解决方案是使用单个TCP连接双向通信。 这就是WebSocket协议所提供的功能。 结合WebSocket API ，WebSocket协议提供了一个用来替代HTTP轮询实现网页到远程主机的双向通信的方法。

WebSocket协议被设计来取代用HTTP作为传输层的双向通讯技术,这些技术只能牺牲效率和可依赖性其中一方来提高另一方，因为HTTP最初的目的不是为了双向通讯。

### 名称的由来

08年6月18日，一群WHATWG的工程师在讨论一些技术问题，一个工程师提到说「我们之前讨论的那个东西，不要叫TCPConnection 了，还是起个别的名字吧 」，接着几个名字被提及，DuplexConnection，TCPSocket，SocketConnection 。

Socket一直以来都被人用来表示网络中一个连接的两端，考虑到怎么让工程师更容易接受，后来Hixie提出了WebSocket ，大家都没有异议，紧接着mcarter在Comet Daily中发表了文章Independence Day: HTML5 WebSocket Liberates Comet From Hacks，后来随着各大浏览器对WebSocket的支持，它变成了实际的标准，IETF也沿用了这个名字。

### 接口定义

WHATWG文档中对WebSocket接口的定义 

![](https://ws1.sinaimg.cn/large/005H70QEgy1fq58gh3koqj30qw0fs0wg.jpg)

### 协议基础

WebSocket的目的是取代HTTP在双向通信场景下的使用，而且它的实现方式有些也是基于HTTP的（WS的默认端口是80，WSS是443）。现有的网络环境（客户端、服务器、网络中间人、代理等）对HTTP都有很好的支持，所以这样做可以充分利用现有的HTTP的基础设施，有点向下兼容的意味。 

### 和http的关系（握手）

浏览器请求
GET /webfin/websocket/ HTTP/1.1

Host: localhost
Upgrade: websocket

Connection: Upgrade
Sec-WebSocket-Key: xqBt3ImNzJbYqRINxEFlkg==
Origin: http://服务器地址
Sec-WebSocket-Version: 13

服务器回应

HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: K7DJLdLooIwIG/MOpvWFB3y3FE8=

WebSocket借用http请求进行握手，相比正常的http请求，多了一些内容。其中，

Upgrade: websocket
Connection: Upgrade

表示希望将http协议升级到Websocket协议。

Sec-WebSocket-Key是浏览器随机生成的base64 encode的值，用来询问服务器是否是支持WebSocket。

服务器返回

Upgrade: websocket
Connection: Upgrade

告诉浏览器即将升级的是Websocket协议

Sec-WebSocket-Accept是将请求包“Sec-WebSocket-Key”的值，与”258EAFA5-E914-47DA-95CA-C5AB0DC85B11″这个字符串进行拼接，然后对拼接后的字符串进行sha-1运算，再进行base64编码得到的。用来说明自己是WebSocket助理服务器。

Sec-WebSocket-Version是WebSocket协议版本号。RFC6455要求使用的版本是13，之前草案的版本均应当被弃用。
更多握手规范详见RFC6455。

### 和socket的关系

就像Java和JavaScript，并没有什么太大的关系，但又不能说完全没关系。可以这么说：

1.命名方面，Socket是一个深入人心的概念，WebSocket借用了这一概念；
2.使用方面，完全两个东西。

![](https://ws1.sinaimg.cn/large/005H70QEgy1fq58ows4yaj30ee0dgwj6.jpg)

## 环境准备

### 安装swoole

```bash
pecl install swoole
```

### 准备好jQuery.js

## websocket_server.php

{% highlight php linenos %}
<?php
/**
 * Created by PhpStorm.
 * User: heimo
 * Date: 2017/6/8
 * Time: 下午10:52
 */

/*
创建wss

$ws = new swoole_websocket_server("0.0.0.0", 9502, SWOOLE_PROCESS, SWOOLE_SOCK_TCP | SWOOLE_SSL);
$ws->set(array(
    'ssl_cert_file' => '/ssl/xxxx.crt',
    'ssl_key_file' => '/ssl/xxxx.key',
));
*/


//创建websocket服务器对象，监听0.0.0.0:9502端口
$ws = new swoole_websocket_server("0.0.0.0", 9502);

//监听WebSocket连接打开事件
$ws->on('open', function ($ws, $request) {
    //var_dump($ws);
    //var_dump($request);
    echo "client-{$request->fd} is connected\n";
});

//监听WebSocket消息事件
$ws->on('message', function ($ws, $frame) {
    echo "client-{$frame->fd} send message：".$frame->data."\n";
    foreach($ws->connections as $fd)
    {
        echo "push message to client-{$fd} \n";
        $ws->push($fd, $frame->data);
    }
});

//监听WebSocket连接关闭事件
$ws->on('close', function ($ws, $fd) {
    echo "client-{$fd} is closed\n";
});

$ws->start();

{% endhighlight %}

## chat.html

{% highlight php linenos %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>websocket</title>
</head>
<style>
    .sendBox { margin: 15px; }
    .sendBox .input { width: 200px;height: 25px; }
    .sendBox .button{ width: 60px;height: 25px; }
</style>
<body>
    <div class="sendBox">
        <input class="input" id="msg" />
        <button class="button" type="button" onclick="send()">send</button>
    </div>
    <hr>
    <div>
        <ul id="chatMsg">

        </ul>
    </div>
</body>
<script type="text/javascript" src="jquery.js"></script>
<script>
    var wsServer = 'ws://websocket_server_ip:9502';
    var websocket = new WebSocket(wsServer);

    //打开事件
    websocket.onopen = function (evt) {
        $("#chatMsg").append("<li style='color: green'>Connected to WebSocket server!</li>");
    };

    //关闭事件
    websocket.onclose = function (evt) {
        $("#chatMsg").append("<li style='color: red'>Disconnected!</li>");
    };

    //消息事件
    websocket.onmessage = function (evt) {
        $("#chatMsg").prepend("<li style='color: blue'>new message:"+escapeHtml(evt.data)+"</li>");
    };

    //异常事件
    websocket.onerror = function (evt, e) {
        $("#chatMsg").append("<li style='color: red'>Error occured:"+evt.data+"</li>");
    };

    //发送消息
    function send() {
        var msg = $('#msg').val();
	if (msg.length/1024 > 1 || msg.length == 0){
            alert('消息内容不符合规范');        
	}else {
            websocket.send(msg);
	    $('#msg').val('');
        }
    }

    document.onkeydown = function(e){
        var ev = document.all ? window.event : e;
        if(ev.keyCode==13) {
            send();
            $('#msg').val('');
        }
    }

    //过滤
    var entityMap = {
        "&": "&amp;",
        "<": "&lt;",
        ">": "&gt;",
        '"': '&quot;',
        "'": '&#39;',
        "/": '&#x2F;'
    };

    function escapeHtml(string) {
        return String(string).replace(/[&<>"'\/]/g, function (s) {
            return entityMap[s];
        });
    }
</script>
</html>

{% endhighlight %}

## 启动服务

1. 修改chat.html中wsServer的配置为自己的配置

```javascript
    var wsServer = 'ws://websocket_server_ip:9502';
```

2. 配置Nginx使用户能够访问到chat.html

3. 启动websocket_server

`sudo php websocket_server.php`

![image](https://github.com/Heimo-He/webIM/raw/master/screenshots/01.gif)


## 访问页面

浏览器打开chat.html

## 构建整个IM需要考虑的问题

### 安全问题

1.身份认证：token

2.防止越权：代码逻辑

3.XSS：过滤

4.https和wss

### 性能问题

1.并发

2.异步

### 稳定性

1.心跳：服务端

2.断线重连：客户端



> ***转载使用注明出处。原文链接 [https://heimo-he.github.io/php/2018/03/30/php-swoole-webim/](https://heimo-he.github.io/php/2018/03/30/php-swoole-webim/)***