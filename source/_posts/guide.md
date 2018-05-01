---
title: JavaScript guide
date: 2017-12-28
photo: https://shuangmuyingzi.github.io/img/npm.jpg
---

本文主要记录一些javascript优秀的写法，自己又老是不习惯使用的，方便日后养成习惯。

<!--more-->

## JavaScript guide es5

### 数组
1、当你需要拷贝数组时，使用 Array#slice。

```
var len = items.length;
var itemsCopy = [];
var i;
// bad
for (i = 0; i < len; i++) {
  itemsCopy[i] = items[i];
}
// good
itemsCopy = items.slice();
```
2、使用 Array#slice 将类数组对象转换成数组。

```
function trigger() {
  var args = Array.prototype.slice.call(arguments);
  ...
}
```
### 字符串
1、使用单引号 `''` 包裹字符串。

```
// bad
var name = "Bob Parr";
// good
var name = 'Bob Parr';
// bad
var fullName = "Bob " + this.lastName;
// good
var fullName = 'Bob ' + this.lastName;
```
2、超过 100 个字符的字符串应该使用连接符写成多行。
3、注：若过度使用，通过连接符连接的长字符串可能会影响性能。

```
// bad
var errorMessage = 'This is a super long error that was thrown because of Batman. When you stop to think about how Batman had anything to do with this, you would get nowhere fast.';
// bad
var errorMessage = 'This is a super long error that was thrown because \
of Batman. When you stop to think about how Batman had anything to do \
with this, you would get nowhere \
fast.';
// good
var errorMessage = 'This is a super long error that was thrown because ' +
  'of Batman. When you stop to think about how Batman had anything to do ' +
  'with this, you would get nowhere fast.';
```
4、程序化生成的字符串使用 Array#join 连接而不是使用连接符。尤其是 IE 下：

```
var items;
var messages;
var length;
var i;
messages = [{
  state: 'success',
  message: 'This one worked.'
}, {
  state: 'success',
  message: 'This one worked as well.'
}, {
  state: 'error',
  message: 'This one did not work.'
}];
length = messages.length;
// bad
function inbox(messages) {
  items = '<ul>';
  for (i = 0; i < length; i++) {
    items += '<li>' + messages[i].message + '</li>';
  }
  return items + '</ul>';
}
// good
function inbox(messages) {
  items = [];
  for (i = 0; i < length; i++) {
    // use direct assignment in this case because we're micro-optimizing.
    items[i] = '<li>' + messages[i].message + '</li>';
  }
  return '<ul>' + items.join('') + '</ul>';
}
```
### 函数
1、函数表达式：

```
// 匿名函数表达式
var anonymous = function() {
  return true;
};
// 命名函数表达式
var named = function named() {
  return true;
};
// 立即调用的函数表达式（IIFE）
(function () {
  console.log('Welcome to the Internet. Please follow me.');
}());
```
2、永远不要在一个非函数代码块（if、while 等）中声明一个函数，浏览器允许你这么做，但它们的解析表现不一致，正确的做法是：在块外定义一个变量，然后将函数赋值给它。

3、注： ECMA-262 把 块 定义为一组语句。函数声明不是语句。

```
// bad
if (currentUser) {
  function test() {
    console.log('Nope.');
  }
}
// good
var test;
if (currentUser) {
  test = function test() {
    console.log('Yup.');
  };
}
```
4、永远不要把参数命名为 arguments。这将取代函数作用域内的 arguments 对象。

```
// bad
function nope(name, options, arguments) {
  // ...stuff...
}
// good
function yup(name, options, args) {
  // ...stuff...
}
```
### 变量
1、使用 var 声明每一个变量。
这样做的好处是增加新变量将变的更加容易，而且你永远不用再担心调换错 ; 跟 ,。

```
// bad
var items = getItems(),
    goSportsTeam = true,
    dragonball = 'z';
// bad
// （跟上面的代码比较一下，看看哪里错了）
var items = getItems(),
    goSportsTeam = true;
    dragonball = 'z';
// good
var items = getItems();
var goSportsTeam = true;
var dragonball = 'z';
```
2、最后再声明未赋值的变量。当你需要引用前面的变量赋值时这将变的很有用。

```
// bad
var i, len, dragonball,
    items = getItems(),
    goSportsTeam = true;
// bad
var i;
var items = getItems();
var dragonball;
var goSportsTeam = true;
var len;
// good
var items = getItems();
var goSportsTeam = true;
var dragonball;
var length;
var i;
```
3、在作用域顶部声明变量。这将帮你避免变量声明提升相关的问题。

```
// bad
function () {
  test();
  console.log('doing stuff..');
  //..other stuff..
  var name = getName();
  if (name === 'test') {
    return false;
  }
  return name;
}
// good
function () {
  var name = getName();
  test();
  console.log('doing stuff..');
  //..other stuff..
  if (name === 'test') {
    return false;
  }
  return name;
}
// bad - 不必要的函数调用
function () {
  var name = getName();
  if (!arguments.length) {
    return false;
  }
  this.setFirstName(name);
  return true;
}
// good
function () {
  var name;
  if (!arguments.length) {
    return false;
  }
  name = getName();
  this.setFirstName(name);
  return true;
}
```

### 比较运算符 & 等号
1、优先使用 === 和 !== 而不是 == 和 !=.
2、条件表达式例如 if 语句通过抽象方法 ToBoolean 强制计算它们的表达式并且总是遵守下面的规则：
对象 被计算为 true
Undefined 被计算为 false
Null 被计算为 false
布尔值 被计算为 布尔的值
数字 如果是 +0、-0 或 NaN 被计算为 false，否则为 true
字符串 如果是空字符串 '' 被计算为 false，否则为 true

```
if ([0]) {
  // true
  // 一个数组就是一个对象，对象被计算为 true
}
```
3、使用快捷方式。

```
// bad
if (name !== '') {
  // ...stuff...
}
// good
if (name) {
  // ...stuff...
}
// bad
if (collection.length > 0) {
  // ...stuff...
}
// good
if (collection.length) {
  // ...stuff...
}
```
### 块
1、使用大括号包裹所有的多行代码块。

```
// bad
if (test)
  return false;
// good
if (test) return false;
// good
if (test) {
  return false;
}
// bad
function () { return false; }
// good
function () {
  return false;
}
```
### 注释
1、使用 /** ... */ 作为多行注释。包含描述、指定所有参数和返回值的类型和值。

```
// bad
// make() returns a new element
// based on the passed in tag name
//
// @param {String} tag
// @return {Element} element
function make(tag) {
  // ...stuff...
  return element;
}
// good
/**
 * make() returns a new element
 * based on the passed in tag name
 *
 * @param {String} tag
 * @return {Element} element
 */
function make(tag) {
  // ...stuff...
  return element;
}
```
2、使用 // 作为单行注释。在评论对象上面另起一行使用单行注释。在注释前插入空行。

```
// bad
var active = true;  // is current tab
// good
// is current tab
var active = true;
// bad
function getType() {
  console.log('fetching type...');
  // set the default type to 'no type'
  var type = this.type || 'no type';
  return type;
}
// good
function getType() {
  console.log('fetching type...');
  // set the default type to 'no type'
  var type = this.type || 'no type';
  return type;
}
```
3、给注释增加 FIXME 或 TODO 的前缀可以帮助其他开发者快速了解这是一个需要复查的问题，或是给需要实现的功能提供一个解决方式。这将有别于常见的注释，因为它们是可操作的。使用 FIXME -- need to figure this out 或者 TODO -- need to implement。

4、使用 // FIXME: 标注问题。

```
function Calculator() {
  // FIXME: shouldn't use a global here
  total = 0;
  return this;
}

```
5、使用 // TODO: 标注问题的解决方式。

```
function Calculator() {
  // TODO: total should be configurable by an options param
  this.total = 0;
  return this;
}
```
### 空白
1、使用 2 个空格作为缩进。

```
// bad
function () {
∙∙∙∙var name;
}
// bad
function () {
∙var name;
}
// good
function () {
∙∙var name;
}
```
2、在大括号前放一个空格。

```
// bad
function test(){
  console.log('test');
}
// good
function test() {
  console.log('test');
}
// bad
dog.set('attr',{
  age: '1 year',
  breed: 'Bernese Mountain Dog'
});
// good
dog.set('attr', {
  age: '1 year',
  breed: 'Bernese Mountain Dog'
});
```
3、在控制语句（if、while 等）的小括号前放一个空格。在函数调用及声明中，不在函数的参数列表前加空格。

```
// bad
if(isJedi) {
  fight ();
}
// good
if (isJedi) {
  fight();
}
// bad
function fight () {
  console.log ('Swooosh!');
}
// good
function fight() {
  console.log('Swooosh!');
}
```
4、使用空格把运算符隔开。

```
// bad
var x=y+5;
// good
var x = y + 5;
```
5、在文件末尾插入一个空行。

```
// bad
(function (global) {
  // ...stuff...
})(this);
```
```
// bad
(function (global) {
  // ...stuff...
})(this);↵
↵
```
```
// good
(function (global) {
  // ...stuff...
})(this);↵
```
6、在使用长方法链时进行缩进。使用前面的点 . 强调这是方法调用而不是新语句。

```
// bad
$('#items').find('.selected').highlight().end().find('.open').updateCount();
// bad
$('#items').
  find('.selected').
    highlight().
    end().
  find('.open').
    updateCount();
// good
$('#items')
  .find('.selected')
    .highlight()
    .end()
  .find('.open')
    .updateCount();
// bad
var leds = stage.selectAll('.led').data(data).enter().append('svg:svg').classed('led', true)
    .attr('width', (radius + margin) * 2).append('svg:g')
    .attr('transform', 'translate(' + (radius + margin) + ',' + (radius + margin) + ')')
    .call(tron.led);
// good
var leds = stage.selectAll('.led')
    .data(data)
  .enter().append('svg:svg')
    .classed('led', true)
    .attr('width', (radius + margin) * 2)
  .append('svg:g')
    .attr('transform', 'translate(' + (radius + margin) + ',' + (radius + margin) + ')')
    .call(tron.led);
```
7、在块末和新语句前插入空行。

```
// bad
if (foo) {
  return bar;
}
return baz;
// good
if (foo) {
  return bar;
}
return baz;
// bad
var obj = {
  foo: function () {
  },
  bar: function () {
  }
};
return obj;
// good
var obj = {
  foo: function () {
  },
  bar: function () {
  }
};
return obj;
```
### 分号
1、使用分号。

```
// bad
(function () {
  var name = 'Skywalker'
  return name
})()
// good
(function () {
  var name = 'Skywalker';
  return name;
})();
// good (防止函数在两个 IIFE 合并时被当成一个参数
;(function () {
  var name = 'Skywalker';
  return name;
})();
```
### 类型转换
1、在语句开始时执行类型转换。
2、字符串：

```
//  => this.reviewScore = 9;
// bad
var totalScore = this.reviewScore + '';
// good
var totalScore = '' + this.reviewScore;
// bad
var totalScore = '' + this.reviewScore + ' total score';
// good
var totalScore = this.reviewScore + ' total score';
```
3、使用 parseInt 转换数字时总是带上类型转换的基数。

```
var inputValue = '4';
// bad
var val = new Number(inputValue);
// bad
var val = +inputValue;
// bad
var val = inputValue >> 0;
// bad
var val = parseInt(inputValue);
// good
var val = Number(inputValue);
// good
var val = parseInt(inputValue, 10);
```
4、如果因为某些原因 parseInt 成为你所做的事的瓶颈而需要使用位操作解决性能问题时，留个注释说清楚原因和你的目的。

```
// good
/**
 * parseInt was the reason my code was slow.
 * Bitshifting the String to coerce it to a
 * Number made it a lot faster.
 */
var val = inputValue >> 0;
```
5、布尔:

```
var age = 0;
// bad
var hasAge = new Boolean(age);
// good
var hasAge = Boolean(age);
// good
var hasAge = !!age;
```
### 命名规则
1、避免单字母命名。命名应具备描述性。

```
// bad
function q() {
  // ...stuff...
}
// good
function query() {
  // ..stuff..
```
2、使用驼峰式命名对象、函数和实例。

```
// bad
var OBJEcttsssss = {};
var this_is_my_object = {};
var o = {};
function c() {}
// good
var thisIsMyObject = {};
function thisIsMyFunction() {}
```
3、使用帕斯卡式命名构造函数或类。

```
// bad
function user(options) {
  this.name = options.name;
}
var bad = new user({
  name: 'nope'
});
// good
function User(options) {
  this.name = options.name;
}
var good = new User({
  name: 'yup'
});
```
4、不要使用下划线前/后缀。
为什么？JavaScript 并没有私有属性或私有方法的概念。虽然使用下划线是表示「私有」的一种共识，但实际上这些属性是完全公开的，它本身就是你公共接口的一部分。这种习惯或许会导致开发者错误的认为改动它不会造成破坏或者不需要去测试。长话短说：如果你想要某处为「私有」，它必须不能是显式提出的。

```
// bad
this.__firstName__ = 'Panda';
this.firstName_ = 'Panda';
this._firstName = 'Panda';
// good
this.firstName = 'Panda';
```
5、不要保存 this 的引用。使用 Function#bind。

```
// bad
function () {
  var self = this;
  return function () {
    console.log(self);
  };
}
// bad
function () {
  var that = this;
  return function () {
    console.log(that);
  };
}
// bad
function () {
  var _this = this;
  return function () {
    console.log(_this);
  };
}
// good
function () {
  return function () {
    console.log(this);
  }.bind(this);
}
```
6、如果你的文件导出一个类，你的文件名应该与类名完全相同。

```
// file contents
class CheckBox {
  // ...
}
module.exports = CheckBox;
// in some other file
// bad
var CheckBox = require('./checkBox');
// bad
var CheckBox = require('./check_box');
// good
var CheckBox = require('./CheckBox');
```
### 模块
1、模块应该以 ! 开始。这样确保了当一个不好的模块忘记包含最后的分号时，在合并代码到生产环境后不会产生错误
2、文件应该以驼峰式命名，并放在同名的文件夹里，且与导出的名字一致。
3、增加一个名为 noConflict() 的方法来设置导出的模块为前一个版本并返回它。
4、永远在模块顶部声明 'use strict';。

```
// fancyInput/fancyInput.js
!function (global) {
  'use strict';
  var previousFancyInput = global.FancyInput;
  function FancyInput(options) {
    this.options = options || {};
  }
  FancyInput.noConflict = function noConflict() {
    global.FancyInput = previousFancyInput;
    return FancyInput;
  };
  global.FancyInput = FancyInput;
}(this);
```
参考链接：https://github.com/sivan/javascript-style-guide/blob/master/es5/README.md


