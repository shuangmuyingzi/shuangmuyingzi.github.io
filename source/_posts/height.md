title: 元素的高度
date: 2018-11-28
tag:
 - js

photos:
 - /img/2017/2.jpg 

---

HTML5之webSocket的客户端实现，建立webSocket连接、交换信息及最终关闭webSocket连接过程。

<!--more-->

为了使用Web Socket,需要在Web服务器上运行一个Web Socket服务器。这个程序负责协调各方通信，而且启动后就会不间断的运行下去。本厂后端开发语言为php(毕竟是世界上最好的语言)，有兴趣的可以在github上找相关服务端代码阅读。本文只介绍Web Socket客户端的实现。


### offsetHeight

```
let endHeight = document.getElementById('chat_inner_box').offsetHeight;
console.log( 'endHeight' + endHeight);
document.getElementById('chat_body').scrollTop =  scrollHeight;
```
Element.offsetHeight属性返回一个整数，表示元素的 CSS 垂直高度（单位像素），包括元素本身的高度、padding 和 border，以及水平滚动条的高度（如果存在滚动条）。只读属性



### scrollTop
Element.scrollTop属性表示当前元素的垂直滚动条向下滚动的像素数量。对于那些没有滚动条的网页元素，这两个属性总是等于0。

如果要查看整张网页的水平的和垂直的滚动距离，要从document.documentElement元素上读取。

```
document.documentElement.scrollLeft
document.documentElement.scrollTop
```
这两个属性都可读写，设置该属性的值，会导致浏览器将当前元素自动滚动到相应的位置。

```
this.$store.commit('setScScrollTop', document.querySelector('.content-container').scrollTop);
document.querySelector('.content-container').scrollTop = vm.$store.state.scIndexScrollTop;
```
### scrollHeight
Element.scrollHeight属性返回一个整数值（小数会四舍五入），表示当前元素的总高度（单位像素），包括溢出容器、当前不可见的部分。它包括padding，但是不包括border、margin以及水平滚动条的高度（如果有水平滚动条的话），还包括伪元素（::before或::after）的高度。

```
HTML 代码如下
// <div id="myDiv" style="height: 200px; overflow: hidden;">...<div>
document.getElementById('myDiv').scrollHeight // 356
```
上面代码中，即使myDiv元素的 CSS 高度只有200像素，且溢出部分不可见，但是scrollHeight仍然会返回该元素的原始高度。
### offsetTop
Element.offsetTop返回垂直位移，单位为像素。通常，这个值是指相对于父节点的位移。

下面的代码可以让页面滚动到相对应位置。

```
document.querySelector('.content-container').scrollTop = document.querySelector('.main1').offsetTop;
```

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



