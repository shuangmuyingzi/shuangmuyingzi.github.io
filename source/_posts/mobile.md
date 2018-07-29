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






