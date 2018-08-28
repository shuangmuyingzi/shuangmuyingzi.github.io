title: js简单的防抖跟节流
date: 2018-01-26
tag:
 - js

photos:
 - /img/2017/4.jpg 

---

前端的优化这个老生常谈的话题，这次我来记录下js的防抖跟节流对于页面优化的实现方式。

<!--more-->

为了使用Web Socket,需要在Web服务器上运行一个Web Socket服务器。这个程序负责协调各方通信，而且启动后就会不间断的运行下去。本厂后端开发语言为php(毕竟是世界上最好的语言)，有兴趣的可以在github上找相关服务端代码阅读。本文只介绍Web Socket客户端的实现。

## 前言

在前端开发中我们会遇到一些频繁的事件触发，比如：window 的 resize、scroll，mousedown、mousemove，keyup、keydown …… 或者根据数据的改变实时得进行数据接口的请求，比如搜索。

下面，我们举个示例代码来了解事件如何频繁的触发：

`index.html` 文件：

```html
<!DOCTYPE html>
<html>
<head>
    <title>debounce</title>
    <style>
        #container{
            width: 100%; height: 200px; line-height: 200px; text-align: center; color: #fff; background-color: #333; font-size: 30px;
        }
    </style>
</head>
<body>
    <div id="container"></div>
    <script src="debounce.js"></script>
</body>
</html>
```
`debounce.js` 文件：

```JS
var count = 1;
var container = document.getElementById('container');
function getMouseMove() {
    container.innerHTML = count++;
};
container.onmousemove = getMouseMove;
```
当我们从浏览器这样滑过去时会调用很多次的getMouseMove方法，这只是一个很简单的效果，浏览器完全反应得过来，试想一下，如果是复杂的回调或者是ajax请求那性能会大受打击，会有明显的卡顿。

为了解决这个问题，一般有两种解决方案：
    1、debounce 防抖
    2、throttle 节流
### 防抖
防抖的原理就是：你尽管触发事件，但是我一定在事件停止触发 n 秒后才执行。

这意味着如果你在一个事件触发的 n 秒内又触发了这个事件，那我就以新的事件触发的时间为准，在此时间 n 秒后才执行。

总之，就是要等你触发完事件 n 秒内不再触发事件，我才执行!

#### 防抖函数

```JS
function debounce(func, wait) {
    var timeout;
    return function () {
        var context = this;
        var args = arguments;
        clearTimeout(timeout)
        timeout = setTimeout(function(){
            func.apply(context, args)
        }, wait);
    }
}
```
使用：

```JS
container.onmousemove = debounce(getMouseMove, 1000);
```
### 节流

节流就是：如果你持续触发事件，每隔一段时间，只执行一次事件。

关于节流的实现，有两种主流的实现方式，一种是使用时间戳，一种是设置定时器。
#### 使用时间戳
让我们来看第一种方法：使用时间戳，当触发事件的时候，我们取出当前的时间戳，然后减去之前的时间戳(最一开始值设为 0 )，如果大于设置的时间周期，就执行函数，然后更新时间戳为当前的时间戳，如果小于，就不执行。

```JS
function throttle(func, wait) {
    var context, args;
    var previous = 0;
    return function() {
        var now = +new Date();
        context = this;
        args = arguments;
        if (now - previous > wait) {
            func.apply(context, args);
            previous = now;
        }
    }
}
```
例子依然是用讲 debounce 中的例子，如果你要使用：

```JS
container.onmousemove = throttle(getMouseMove, 1000);
```
#### 使用定时器
接下来，我们讲讲第二种实现方式，使用定时器。

当触发事件的时候，我们设置一个定时器，再触发事件的时候，如果定时器存在，就不执行，直到定时器执行，然后执行函数，清空定时器，这样就可以设置下个定时器。

```JS
function throttle(func, wait) {
    var timeout;
    return function() {
        context = this;
        args = arguments;
        if (!timeout) {
            timeout = setTimeout(function(){
                timeout = null;
                func.apply(context, args)
            }, wait)
        }
    }
}
```
参考链接如下：
https://segmentfault.com/a/1190000009638648
https://segmentfault.com/a/1190000009831691


