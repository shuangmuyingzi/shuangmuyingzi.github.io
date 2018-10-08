title: 前端性能优化
date: 2018-09-28
tag:
 - 性能分析

photos:
 - /img/2018/performance.jpg 

---

对于网站的性能，在行业内有很多既定的指标，但就以前端 er 而言，我们应该更加关注以下指标：白屏时间、首屏时间、整页时间、DNS 时间、CPU 占用率。

<!--more-->

对Chrome控制台有一定的了解的朋友都在知道，Network面板会包括很多网络请求方面的东西，包括Http相关的Request信息，Response信息，以及Cookies等等，都是前端开发需要密切关注的问题。这些信息都是属于功能性的，那么当我们的功能需求满足后，势必需要对于性能进行优化，有什么工具可以帮助我们进行分析呢？答案就是Chrome控制台的Performance面板。

## 浏览器都做了什么
我们希望浏览器打开一个简单的网页

```
<!DOCTYPE html>
<html>
  <head>
    <title>The "Click the button" page</title>
    <meta charset="UTF-8">
    <link rel="stylesheet" href="styles.css" />
  </head>
  
  <body>
    <h1>
      Click the button.
    </h1>
    
    <button type="button">Click me</button>
    
    <script>
      var button = document.querySelector("button");
      button.style.fontWeight = "bold";
      button.addEventListener("click", function () {
        alert("Well done.");
      });
    </script>
  </body>
</html>

```

### 浏览器如何渲染网页

1、使用 HTML 创建文档对象模型（DOM）
2、使用 CSS 创建 CSS 对象模型（CSSOM）
3、基于 DOM 和 CSSOM 执行脚本（Scripts）
4、合并 DOM 和 CSSOM 形成渲染树（Render Tree）
5、使用渲染树布局（Layout）所有元素
6、渲染（Paint）所有元素

![GitHub set up](http://jinlong.github.io/image/browser-fe-optimizing/p1.png)

## 步骤一 — HTML
浏览器从上到下读取标签，把他们分解成节点，从而创建 DOM 。

![GitHub set up](http://jinlong.github.io/image/browser-fe-optimizing/p2.png)

### HTML 加载优化策略
1、样式在顶部，脚本在底部
总体思路是尽可能早的加载样式，尽可能晚的加载脚本。原因是脚本执行之前，需要 HTML 和 CSS 解析完成，因此，样式尽可能的往顶部放，当底部脚本开始执行之前，样式有足够的时间完成计算。


2、最小化和压缩
方法可用于所有内容，包括 HTML，CSS，JavaScript，图片和其它资源。

最小化是移除所有多余的字符，包括空格，注释，多余的分号，等等。

压缩比如 GZip，大大压缩下载文件的大小
## 步骤二 — CSS
当浏览器发现任何与节点相关的样式时，比如：外部，内部，或行内样式，立即停止渲染 DOM ，并利用这些节点创建 CSSOM。这就是 CSS “渲染阻塞“ 的由来。

```
//外部样式
<link rel="stylesheet" href="styles.css">
// 内部样式
<style>
  h1 {
    font-size: 18px;
  }
</style>
// 行内样式
<button style="background-color: blue;">Click me</button>

```
CSSOM 节点创建与 DOM 节点创建类似，随后，两者合并如下：
![GitHub set up](http://jinlong.github.io/image/browser-fe-optimizing/p3.png)
CSSOM 的构建会阻塞页面的渲染，因此我们想尽早加载样式
### CSS 加载优化策略
```
ws.onerror = function (error) {
        console.log('连接出错');
    };
```
onerror是在连接出现问题时触发，连接出错考虑是否需要做相应的提示或者重连。
### CSS 加载优化策略
1、延迟加载 CSS
有些样式，比如：首屏以下的，或者不那么重要的，可以等待首屏最有价值的内容渲染完成再加载，可以使用脚本等待页面加载，然后再插入样式。

2、只加载需要的样式
尽量移除不需要的样式。

## 步骤三 — JavaScript
浏览器不断构建 DOM / CSSOM 节点，直到发现外部或者行内的脚本。

由于脚本可能需要访问或操作之前的 HTML 或样式，我们必须等待它们构建完成。

因此浏览器必须停止解析节点，完成构建 CSSOM，执行脚本，然后再继续。这就是 JavaScript 被称作“解析器阻塞”的原因。

脚本只能等到先前的 CSS 节点构建完成。
![GitHub set up](http://jinlong.github.io/image/browser-fe-optimizing/p4.png)

### JavaScript 加载优化策略
1、异步加载脚本
脚本添加 async 属性，可以通知浏览器不要阻塞其余页面的加载，下载脚本处于较低的优先级。一旦下载完成，就可以执行。
async 适用于不影响 DOM 或 CSSOM 的脚本，对一些跟我们的代码无关的，不影响用户体验的外部脚本尤其适用，比如：分析统计脚本。
2、延迟加载脚本
defer 跟 async 非常相似，不会阻塞页面加载，但会等到 HTML 完成解析后再执行。
不幸的是 async 和 defer 对于行内的脚本不起作用，浏览器默认会编译执行它们。
3、操作之前克隆节点
多次操作 DOM 时可以尝试，首先克隆整个 DOM 节点更加高效，操作克隆后的节点，然后替换先前的节点，避免了多次重绘，降低了 CPU 和内存消耗，同时也避免了不必要的页面闪烁。
## 步骤四 — 渲染树（Render Tree）
一旦所有节点已被解析，DOM 和 CSSOM 准备合并，浏览器便会构建渲染树。如果我们把节点想象成单词，那么对象模型就是句子，渲染树便是整个页面。
![GitHub set up](http://jinlong.github.io/image/browser-fe-optimizing/p7.png)
## 步骤五 — 布局（Layout）
布局阶段需要确定页面上所有元素的大小和位置。

![GitHub set up](http://jinlong.github.io/image/browser-fe-optimizing/p8.png)
## 步骤六 — 渲染（Paint）
最终的渲染阶段，会真正地光栅化屏幕上的像素，把页面呈现给用户。

![GitHub set up](http://jinlong.github.io/image/browser-fe-optimizing/p9.png)
整个过程耗时1秒或十分之一秒，我们的任务是让它更快。

如果 JavaScript 事件改变了页面的某部分，便会引起渲染树的重绘，并且迫使布局（Layout）和渲染（Paint）过程再次进行。

## 浏览器如何发起网络请求
当浏览器请求一个 URL，服务端会响应一些 HTML。

我们需要认识一个新术语，关键渲染路径（Critical Rendering Path (CRP)），就是浏览器渲染页面的步骤数，如下图。

![GitHub set up](http://jinlong.github.io/image/browser-fe-optimizing/p10.png)
### 关键路径长度
关键渲染路径的度量标准是路径长度。最理想的关键路径长度是1。

如果页面包含一些内部样式和 JavaScript ，关键路径发生以下改变
![GitHub set up](http://jinlong.github.io/image/browser-fe-optimizing/p11.png)
新增两步，构建 CSSOM和执行脚本，因为我们的 HTML 有内部样式和脚本需要计算。由于没有外部请求，我们的关键路径长度没变。

但是注意，我们的 HTML 大小增加到了 2kb，某些地方还是受了影响。

### 关键字节数
三个度量标准之二出现了，关键字节数，它用来衡量渲染页面需要传送多少字节数。

如果你认为不需要外部资源，就大错特错了，外部资源可以被缓存。

我们使用一个外部 CSS 文件，一个外部 JavaScript 文件，和一个外部带 async 属性的 JavaScript 文件。关键路径图如下：
![GitHub set up](http://jinlong.github.io/image/browser-fe-optimizing/p12.png)
浏览器请求页面，构建 DOM，发现外部资源后开始下载，CSS 和 JavaScript 有较高的优先级，其它资源次之。

styles.css 和 app.js 通过另一个关键路径获取。暂时不获取 analytics.js ，因为加了 async 属性，浏览器将用另一个线程下载它，它处于较低优先级，不会阻塞页面渲染，也不影响关键路径。
### 关键文件
最后一个度量标准是关键文件，浏览器渲染页面需要下载的文件总量。以上例子，HTML 文件，CSS 和 JavaScript 文件算关键文件，async 的脚本不算。当然是文件越少越好。
### 回到关键路径长度
以上例子就是最长的渲染路径吗？我认为渲染页面时，我们仅需要下载 HTML，CSS 和 JavaScript 文件，仅通过两次服务器往返就做到了。
### HTTP1 文件限制
我们浏览器的 HTTP1 协议，在同一个域名，同一次，允许下载的文件数有最大限制，范围从 2（老旧的浏览器）到 6（Edge，Chrome）。
### HTTP2
如果网站使用了 HTTP2，并且用户的浏览器也兼容，则可以完全避开这个下载限制。
### TCP 往返限制
每一次服务器往返可以传送的最大数据量是 14kb，包括所有 HTML，CSS 和脚本的网络请求。

如果我们的 HTML，或者积累的资源请求超过 14kb时，需要多做一次服务器往返。
### 大魔法师
我们整个 HTML 页面可以很好的压缩， GZip 可以压缩到 2kb，远低于 14kb 的限制，因此，一次服务器往返就可以搞定。
![GitHub set up](http://jinlong.github.io/image/browser-fe-optimizing/p13.png)

 `关键路径度量: 长度 1，文件数 1，字节数 2kb`

浏览器发现外部资源（CSS 和 JavaScript）时，发起请求开始下载它们。首要下载的 CSS 文件是 14kb，达到了往返传输的最大限制，因此增加了一条关键路径。
![GitHub set up](http://jinlong.github.io/image/browser-fe-optimizing/p14.png)
`关键路径度量: 长度 2，文件数 2，字节数 16kb`

余下的资源低于 14kb，但是总共有 7 个资源，由于网站未启用 HTTP2，我们的 Chrome，每一次往返仅可以下载 6 个文件。
![GitHub set up](http://jinlong.github.io/image/browser-fe-optimizing/p15.png)
`关键路径度量: 长度 3，文件数 8，字节数 28kb`

下载完最终文件，并开始渲染 DOM。
![GitHub set up](http://jinlong.github.io/image/browser-fe-optimizing/p16.png)

来源：http://jinlong.github.io/2017/05/08/optimising-the-front-end-for-the-browser/


