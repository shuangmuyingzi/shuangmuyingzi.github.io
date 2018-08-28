title: Promise学习
date: 2018-05-28
tag:
 - js
 - promise

photos:
 - /img/2017/5.jpg 

---

Promise对象是一个构造函数，其接受一个函数作为参数，resolve、reject为这个函数的参数，函数内部一般为异步执行的代码，resolve作为异步执行完成之后成功的回调，reject作为异步执行抛错的回调。Promise构造函数可以理解为执行异步的过程，其resolve和reject为执行异步调用结果的回调函数。

<!--more-->

### 初识Promise
Promise对象是一个构造函数，其接受一个函数作为参数，resolve、reject为这个函数的参数，函数内部一般为异步执行的代码，resolve作为异步执行完成之后成功的回调，reject作为异步执行抛错的回调。Promise构造函数可以理解为执行异步的过程，其resolve和reject为执行异步调用结果的回调函数。
```
// 代码1
var p = new Promise((resolve, reject) => {
  // 执行一系列的异步执行
  // some codes...
  if (true) {
    resolve("异步执行成功");
  } else {
    reject("异步执行抛错");
  }
}); // fulfilled: 异步执行成功 ; 非fulfilled: 异步执行抛错
```

### Promise的异步处理

#### then和catch的使用
Promise构造函数返回一个异步执行之后的promise对象，该对象对异步的结果进一步处理。

```
// 代码2
p
  .then((res) => {
    // try catch 手动抛错
    try {
      // console.log("异步返回成功状态");
      throw Error("错误代码");
    } catch(e) {
      console.log("执行catch");
      return Promise.reject(e);
    }
  })
  .catch((res) => {
    console.log(res); // 输出上一个 then 中 catch 的 e
    return "这里由错误信息过去的数据";
  })
  .then((res) => {
    console.log(res); // 若上一个catch执行，输出：这里由错误信息过去的数据
  })
```
以上代码执行结果：

```
# 结果1
执行catch
Error: 错误代码
    at p.then (**/promise.js:77:10)
    at process._tickCallback (internal/process/next_tick.js:109:7)
    at Module.runMain (module.js:607:11)
    at run (bootstrap_node.js:423:7)
    at startup (bootstrap_node.js:147:9)
    at bootstrap_node.js:538:3
这里由错误信息过去的数据
```
### promise对象的链式调用
由代码2可以看出，promise对象状态为resolve的时候，执行then方法，而且在不抛错情况下会持续执行链式调用的then方法，若then方法抛出异常或者抛出返回Promise.reject()方法，会转到执行catch方法，若catch方法返回的不是Promise.reject()方法或者不抛出异常，则所有使用return返回的数据都会作为参数传给下一个then函数参数的参数，即代码2中的最后一个then方法指定函数参数的res是上一个catch所return的数据。

```
var p = new Promise((resolve, reject) => {
  resolve("ok");
});
p.then((msg) => {
  console.log(msg);
  return Promise.reject("then01抛错");
}).catch((errMsg) => {
  console.warn(errMsg);
}).then(() => {
  console.log("then02再执行");
}).then(() => {
  console.log("then03再执行");
  return Promise.reject("then03抛错");
}).catch((errMsg) => {
  console.warn(errMsg);
  return "catch02 return 给下一个then指定方法的值";
}).then((msg) => {
  console.log(msg);
});
```
运行结果如下：

```
ok
then01抛错
then02再执行
then03再执行
then03抛错
catch02 return 给下一个then指定方法的值
```
这是因为then方法和catch方法返回的都是一个promise对象。then方法指定的回调函数抛出错误会被下一个catch方法捕获，多个then方法执行也是如此。catch方法会捕获上一个catch方法（如果有的话）之后抛错的错误。

换言之，无论是then方法还是catch方法,返回的都是一个promise对象，其状态取决于上一个方法指定的函数是否顺利执行或者没有返回Promise.reject（）。

### Promise状态
一旦Promise的状态变为resolved或者rejected，就会永久保持该状态，不会再变。

```
 var p = new Promise((resolve, reject) => {
  resolve("ok");
  throw new Error("wrong");
});
p.then((msg) => {
  console.log(msg);
}).catch((errMsg) => {
  console.warn(errMsg);
});

// ok
```
在Promise的参数函数中，由于先断定了resolved状态，所以在之后只会执行then函数，后面抛出的错误会等于没抛出来。

另外，“事件循环”会对抛出的结果有影响。

```
var p = new Promise((resolve, reject) => {
  resolve("ok");
  setTimeout(() => {
    throw new Error("wrong");
  }, 0);
});
p.then((msg) => {
  console.log(msg);
}).catch((errMsg) => {
  console.warn(errMsg);
});

// ok

// 浏览器抛出的错误
// index.js:4 Uncaught Error: wrong
//    at setTimeout (index.js:4)
// setTimeout    @    index.js:4
```
在本轮“事件循环”中，promise对象p先执行，所以构造函数Promise的指定函数先输出‘ok’；在进入到下一次的“事件循环”的时候，由于Promise函数体已经执行完毕，故后面抛出的错误是在Promise函数体外抛出的，Promise函数体无法捕获到这个错误。

#### Promise.resolve()
Promise.resolve()接受一个参数，其返回一个promise对象的状态会因为传入的参数的不同而不同。

参数分别以下几种情况：
空

返回一个状态为resolved的promise对象，也就是下一步会执行then方法。

```
var p = Promise.resolve();

p.then((res) => {
  console.log("then");
}).catch((res) => {
  console.log("catch");
});

// then
```
thenable对象

```
var thenable = {
  then: function (resolve, reject) {
    console.log("立即执行thenable的then的方法" + Date.now());
    resolve("断定之后的信息");
  }
}

var p = Promise.resolve(thenable);

p.then((res) => {
  console.log(res);
});

// 立即执行thenable的then的方法1494485393447
// 断定之后的信息

// 相当于

var p = new Promise(function (resolve, reject) {
  console.log("立即执行thenable的then的方法" + Date.now());
  resolve("断定之后的信息");
});

p.then((res) => {
  console.log(res);
});

// 立即执行thenable的then的方法1494485454503
// 断定之后的信息
```
thenable对象作为参数，在执行Promise.resolve(thenable)方法的时候，会立即执行thenable对象中的then方法，并且其返回的Promise对象的状态取决于thenable对象的then方法执行的是resolve()还是reject()。这种情况下，就相当于Promise构造函数以thenable对象的then方法作为参数，实例化一个Promise实例。

一个非Promise对象，且不含有then方法的对象------非thenable对象

```
var p = Promise.resolve({
  a: 1
});
p.then((res) => {
  console.log(res);
});

// { a: 1 }

var p01 = Promise.resolve('Hello Promise!');
p01.then((res) => {
  console.log(res);
});

// Hello Promise!
```
这种情况下，Promise.resolve()的状态为resolved，其接收的参数会作为then方法指定函数的参数。

Promise对象

```
var p01 = new Promise((resolve, reject) => {
  reject('Throw some error! Come on! You bite me.');
});

var p = Promise.resolve(p01);

p.then((res) => {
  console.log("这是then方法");
}).catch((errMsg) => {
  console.log(errMsg);
});

// Throw some error! Come on! You bite me.
```
传入的是一个Promise对象，Promise.resolve()返回的对象的状态就是传入的Promise对象的状态。


#### Promise.reject()
Promise.reject(reason)方法同样返回一个状态为rejected的Promise对象实例。值得注意的是，参数reason(Promise状态rejected的原因)不论是什么值，都会传给返回的Promise对象的catch方法指定的函数作为参数。

```
var p = Promise.reject('Throw some error! Come on! You bite me.');

p.then((res) => {
  console.log("这是then方法");
}).catch((errMsg) => {
  console.log(errMsg);
});

// Throw some error! Come on! You bite me
```
#### promise对象的使用场景——图片加载

```
var imgPromise = function (url) {
  return new Promise((resolve, reject) => {
    var img = new Image();
    img.src = url;
    img.onload = resolve;
    img.onerror = reject;
  });
}
imgPromise("http://imgurl")
  .then((res) => {
    console.log("图片加载完成");
  })
  .catch((res) => {
    console.log("图片加载失败");
  })：
```






