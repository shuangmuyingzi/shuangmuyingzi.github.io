title: 作用域闭包
date: 2019-01-28
tag:
 - js
 - 闭包


<!--more-->

最近在看你不知道的javascript上卷，做下总结。

### 闭包产生
当函数可以记住并访问所在的词法作用域时，就产生了闭包，即使函数是在当前词法作用域之外执行。

```
function foo() { var a = 2;
function bar() { console.log( a );
}
return bar; }
var baz = foo();
baz(); // 2 —— 朋友，这就是闭包的效果。
```

函数 bar() 的词法作用域能够访问 foo() 的内部作用域。然后我们将 bar() 函数本身当作 一个值类型进行传递。在这个例子中，我们将 bar 所引用的函数对象本身当作返回值。
在 foo() 执行后，其返回值(也就是内部的 bar() 函数)赋值给变量 baz 并调用 baz()，实 际上只是通过不同的标识符引用调用了内部的函数 bar()。
bar() 显然可以被正常执行。但是在这个例子中，它在自己定义的词法作用域以外的地方 执行。
在 foo() 执行后，通常会期待 foo() 的整个内部作用域都被销毁，因为我们知道引擎有垃 圾回收器用来释放不再使用的内存空间。由于看上去 foo() 的内容不会再被使用，所以很 自然地会考虑对其进行回收。
而闭包的“神奇”之处正是可以阻止这件事情的发生。事实上内部作用域依然存在，因此 没有被回收。谁在使用这个内部作用域?原来是 bar() 本身在使用。
拜 bar() 所声明的位置所赐，它拥有涵盖 foo() 内部作用域的闭包，使得该作用域能够一 直存活，以供 bar() 在之后任何时间进行引用。
bar() 依然持有对该作用域的引用，而这个引用就叫作闭包。

```
function foo() { var a = 2;
function baz() { console.log( a ); // 2
}
bar( baz ); }
function bar(fn) {
fn(); // 妈妈快看呀，这就是闭包!
}
```
把内部函数 baz 传递给 bar，当调用这个内部函数时(现在叫作 fn)，它涵盖的 foo() 内部
作用域的闭包就可以观察到了，因为它能够访问 a。 传递函数当然也可以是间接的。

```
var fn;
function foo() {
var a = 2;
function baz() { console.log( a );
}
fn = baz; // 将 baz 分配给全局变量 }
function bar() {
    fn(); // 妈妈快看呀，这就是闭包!
}
     foo();
     bar(); // 2
```
无论通过何种手段将内部函数传递到所在的词法作用域以外，它都会持有对原始定义作用 域的引用，无论在何处执行这个函数都会使用闭包。

### 闭包日常
```
function wait(message) {
         setTimeout( function timer() {
             console.log( message );
}, 1000 ); }
wait( "Hello, closure!" );
```
将一个内部函数(名为 timer)传递给 setTimeout(..)。timer 具有涵盖 wait(..) 作用域
的闭包，因此还保有对变量 message 的引用。
wait(..) 执行 1000 毫秒后，它的内部作用域并不会消失，timer 函数依然保有 wait(..)
作用域的闭包。
深入到引擎的内部原理中，内置的工具函数 setTimeout(..) 持有对一个参数的引用，这个 参数也许叫作 fn 或者 func，或者其他类似的名字。引擎会调用这个函数，在例子中就是 内部的 timer 函数，而词法作用域在这个过程中保持完整。
这就是闭包。
或者，如果你很熟悉 jQuery(或者其他能说明这个问题的 JavaScript 框架)，可以思考下面 的代码:

```
function setupBot(name, selector) {
$( selector ).click( function activator() {
             console.log( "Activating: " + name );
         } );
}
     setupBot( "Closure Bot 1", "#bot_1" );
     setupBot( "Closure Bot 2", "#bot_2" );
```
### 闭包经典循环问题

```
for (var i=1; i<=5; i++) { (function() {
setTimeout( function timer() { console.log( i );
             }, i*1000 );
         })();
}
```
这样子能打出1、2、3、4、5吗
这样不行。但是为什么呢?我们现在显然拥有更多的词法作用域了。的确 每个延迟函数都会将 IIFE 在每次迭代中创建的作用域封闭起来。
如果作用域是空的，那么仅仅将它们进行封闭是不够的。仔细看一下，我们的 IIFE 只是一 个什么都没有的空作用域。它需要包含一点实质内容才能为我们所用。
它需要有自己的变量，用来在每个迭代中储存 i 的值:

```
for (var i=1; i<=5; i++) { (function() {
var j = i;
setTimeout( function timer() {
                 console.log( j );
             }, j*1000 );
})(); }
```
行了!它能正常工作了!。
可以对这段代码进行一些改进:

```
for (var i=1; i<=5; i++) { (function(j) {
setTimeout( function timer() { console.log( j );
             }, j*1000 );
         })( i );
}
```
### 块作用域
我们使用 IIFE 在每次迭代时都创建一个新的作用 域。换句话说，每次迭代我们都需要一个块作用域。
本质上这是将一个块转换成一个可以被关闭的作用域。

```
for (var i=1; i<=5; i++) {
let j = i; // 是的，闭包的块作用域! setTimeout( function timer() {
             console.log( j );
         }, j*1000 );
}
```
优化：

```
for (let i=1; i<=5; i++) { setTimeout( function timer() {
             console.log( i );
         }, i*1000 );
}
```
### 模块


