---
title: 简单的 Web Socket 客户端
date: 2017-10-31
photo: https://shuangmuyingzi.github.io/img/npm.jpg
---

HTML5之webSocket的客户端实现，建立webSocket连接、交换信息及最终关闭webSocket连接过程。

<!--more-->

为了使用Web Socket,需要在Web服务器上运行一个Web Socket服务器。这个程序负责协调各方通信，而且启动后就会不间断的运行下去。本厂后端开发语言为php(毕竟是世界上最好的语言)，有兴趣的可以在github上找相关服务端代码阅读。本文只介绍Web Socket客户端的实现。

## 一个聊天室的Web Socket简单客户端

### 创建Web Socket对象

```
var ws = new WebSocket('ws://127.0.0.1:8000'); //填写具体服务端地址
```
ws对象值如下：

```
binaryType:"blob",
bufferedAmount:0,
extensions:"",
onclose:null,
onerror:null,
onmessage:null,
onopen:null,
protocol:"",
readyState:1,
url:"",
__proto__:
```
创建WebSocket对象后，页面就会尝试连接服务器。接下来，就是使用WebSocket对象的四个事件一个方法以及一个属性：onOpen、onError、onClose和onMessage还有send以及readyState。利用这些事件、方法、属性就可以实现与服务端的简单通信。
### ws.onopen
```
ws.onopen = function () {
        var bindData = {
                "type": "bind",
                "from_uid": '',
                "to_uid": '',
                "room_id":'',
                "time": ''
        }
        ws.send( JSON.stringify( bindData ) );
    };
```
onOpen是在建立连接后触发,代表连接成功。send()方法发送连接的相关信息给服务器端。
### ws.onmessage
```
ws.onmessage = function (e) {
        var result = JSON.parse( e.data );
        switch(result.type){
            case 'speak':
                handleSpeak(result.data);
                break;
            case 'sendMsg':
                break;     
            default:
                break;
        }        
 };
```
onmessage是在页面从服务器接收到消息时触发。type区分服务器端返回消息的类型，可跟后端约定好，根据type的不同去执行不同的业务。
### ws.onclose
```
ws.onclose = function (error) {
        console.log('关闭连接');
    };
```
onclose是在连接关闭时触发,连接关闭考虑是否需要做相应的提示或者重连。
### ws.onerror
```
ws.onerror = function (error) {
        console.log('连接出错');
    };
```
onerror是在连接出现问题时触发，连接出错考虑是否需要做相应的提示或者重连。
### ws.send()
```
<input type="button" id="btn" value="发送" onclick="sendMsg()" >
```

```
//发送信息到服务端
    function sendMsg(){
        var inputText = document.querySelector('#inputText');
        var data = {
                "type": "sendMsg",
                "from_uid": '',
                "to_uid": '',
                "room_id":'',
                "time": ''
                "data": {
                    'text': inputText.value
                },
            }
        ws.send( JSON.stringify( data ) );
    }
```
send方法用于向服务器发送数据。假设type为’sendMsg’,在onmessage事件里面监听该type对应值的服务端返回，进而做相应的处理。

### ws.readyState
readyState返回对象的当前状态，一共有四种。可在需要做处理的地方判断状态值进而做相应的提示，一般判断状态值是否为’3’。

```
* CONNECTING：值为0，表示正在连接。
* OPEN：值为1，表示连接成功，可以通信了。
* CLOSING：值为2，表示连接正在关闭。
* CLOSED：值为3，表示连接已经关闭，或者打开连接失败。
```



