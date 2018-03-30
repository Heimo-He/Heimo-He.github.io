---
title: Swoole实现基于websocket的webIM聊天室
catagories: php
tags:
 - swoole
 - php
 - websocket
---

> 这是一个简单的基于php Swoole扩展实现的聊天室demo。

<!-- more -->

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
$ws = new swoole_websocket_server("0.0.0.0", 9501);

//监听WebSocket连接打开事件
$ws->on('open', function ($ws, $request) {
    //var_dump($ws);
    //var_dump($request);
    echo "client-{$request->fd} is connected\n";
});

//监听WebSocket消息事件
$ws->on('message', function ($ws, $frame) {
   foreach($ws->connections as $fd)
    {
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
    var wsServer = 'ws://127.0.0.1:9501';
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

```bash
php websocket_server.php
```

## 访问页面

浏览器打开chat.html