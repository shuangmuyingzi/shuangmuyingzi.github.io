---
title: 移动端的彩蛋
date: 2018-07-28
photo: https://shuangmuyingzi.github.io/img/npm.jpg
---

本文会持续更新做移动端开发的时候遇到的坑或者是需要注意的点。

<!--more-->
# 关于图片
## 阻止移动端浏览器点击图片会放大的几种方法
下面介绍几种方法：
1.在img元素上添加 onclick="return false"

```
<img src="a.png" onclick="return false" />
```
2.图片用背景图的方式插入
3.使用js事件阻止默认行为的方法！

```
var img = document.getElementById('banner');
img.addEventListener('click',function(e){
　　e.preventDefault();
});
```
## 拼接图片时的缝隙问题
一般如果图切得好是不会出现缝隙的问题，但是有时候出现的话也有办法处理。
之前是说可以给img标签的父级元素设置font-size:0.或者去掉<img>标签之间的空格，但是现在用着发现不起作用了，然后就寻找另一些的方式。
1、将img显示设置成  display：block;

```
img{display：block;}
```
2、可以在img上加这个样式，让图片按照底部对齐，就可消除缝隙

```
vertical-align: bottom;
```
但是如果是背景图拼接之间的缝隙呢
## js number长度问题引起的bug
js number类型最长为17位，所有的语音都是有位数限制的，应该用字符串的形式
## 微信文章阅读原文的链接跳转自带参数问题
以下链接是从微信阅读原文进去的：

```
https://www.weiixn.com/dist/abc.html?from=singlemessage&isappinstalled=0#/?bianhao=1234567
```
这样子的话那个bianhao是拿不到的

```
(function(){
    if( location.href.split("?").length >= 3 ){
        let path = location.href.split("?")[1].split('#')[1];
        location.href = location.href.split("?")[0] + '#' + path + '?' + location.href.split("?")[2];
    }else if( location.href.split("?").length == 2 ){
        let path = location.href.split("?")[1].split('#')[1];
        if( path ){
            location.href = location.href.split("?")[0] + '#' + path;
        }
    }
    // 保存链接中的渠道号到本地
    let bianhao = commonFn.getParams()['bianhao'];
    if( bianhao ){
        localStorage.setItem('bianhao',bianhao);
    }
})();
```
### iPhoneX的兼容，小刘海
参考链接：https://aotu.io/notes/2017/11/27/iphonex/

```

一、<meta name="viewport" content="width=device-width, viewport-fit=cover">

body {
  padding-bottom: constant(safe-area-inset-bottom);
  padding-bottom: env(safe-area-inset-bottom);
}

1padding-bottom: constant(safe-area-inset-bottom); /* 兼容 iOS < 11.2 */
2padding-bottom: env(safe-area-inset-bottom); /* 兼容 iOS >= 11.2 */
```
类型二：fixed 非完全吸底元素（bottom ≠ 0），比如 “返回顶部”、“侧边广告” 等
像这种只是位置需要对应向上调整，可以仅通过外边距 margin 来处理：

```
{
  margin-bottom: constant(safe-area-inset-bottom);
  margin-bottom: env(safe-area-inset-bottom);
}

```



