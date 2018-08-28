title: js基础
date: 2018-06-28
tag:
 - js

photos:
 - /img/2017/6.jpg

---

js基础备忘

<!--more-->

### valueOf()
valueOf方法是一个所有对象都拥有的方法，表示对该对象求值。不同对象的valueOf方法不尽一致，数组的valueOf方法返回数组本身。

```
var arr = [1, 2, 3];
arr.valueOf() // [1, 2, 3]
```
日期对象
valueOf方法返回实例对象距离时间零点（1970年1月1日00:00:00 UTC）对应的毫秒数，该方法等同于getTime方法

```
new Date()
Thu Jun 21 2018 16:43:00 GMT+0800 (中国标准时间)
new Date().valueOf()
1529570590072
```
### toString()
toString方法也是对象的通用方法，数组的toString方法返回数组的字符串形式。

```
var arr = [1, 2, 3];
arr.toString() // "1,2,3"

var arr = [1, 2, 3, [4, 5, 6]];
arr.toString() // "1,2,3,4,5,6"
```
```
new Date()
Thu Jun 21 2018 16:43:00 GMT+0800 (中国标准时间)
new Date().valueOf()
1529570590072
var a = 1;
undefined
a.toString();
"1"
new Date().toString()
"Thu Jun 21 2018 17:15:52 GMT+0800 (中国标准时间)"
```
### slice()
slice方法用于提取目标数组的一部分，返回一个新数组，原数组不变。

```
var a = ['a', 'b', 'c'];
a.slice(0) // ["a", "b", "c"]
a.slice(1) // ["b", "c"]
```
`String.prototype.slice()`
slice方法用于从原字符串取出子字符串并返回，不改变原字符串。它的第一个参数是子字符串的开始位置，第二个参数是子字符串的结束位置（不含该位置）。

```
'JavaScript'.slice(0, 4) // "Java"
```
控制文字长度

```
{{item.user_name.slice(0,4)}}
```
### indexOf()
indexOf方法返回给定元素在数组中第一次出现的位置，如果没有出现则返回-1。

```
var a = ['a', 'b', 'c'];
a.indexOf('b') // 1
a.indexOf('y') // -1
```
`String.prototype.indexOf()`

indexOf方法用于确定一个字符串在另一个字符串中第一次出现的位置，返回结果是匹配开始的位置。如果返回-1，就表示不匹配。

```
'hello world'.indexOf('o') // 4
'JavaScript'.indexOf('script') // -1
```
截取url

```
var index = location.href.indexOf("order_id");
var re_url = location.href.slice(0,index);
```
根据不同环境设置token

```
let host = location.host,token = '';
if( host.indexOf('localhost') !== -1 || host.indexOf('127.0.0.1') !== -1 ){
    token = ''
}
```
### splice()
splice方法用于删除原数组的一部分成员，并可以在删除的位置添加新的数组成员，返回值是被删除的元素。注意，该方法会改变原数组。
splice的第一个参数是删除的起始位置（从0开始），第二个参数是被删除的元素个数。如果后面还有更多的参数，则表示这些就是要被插入数组的新元素。

```
var a = ['a', 'b', 'c', 'd', 'e', 'f'];
a.splice(4, 2) // ["e", "f"]
a // ["a", "b", "c", "d"]
```
### push()、pop()，shift()、unshift()，concat()
### join()
join方法以指定参数作为分隔符，将所有数组成员连接为一个字符串返回。如果不提供参数，默认用逗号分隔。

```
var a = [1, 2, 3, 4];
a.join(' ') // '1 2 3 4'
a.join(' | ') // "1 | 2 | 3 | 4"
a.join() // "1,2,3,4"
```
### map()
map方法将数组的所有成员依次传入参数函数，然后把每一次的执行结果组成一个新数组返回。

```
var numbers = [1, 2, 3];

numbers.map(function (n) {
  return n + 1;
});
// [2, 3, 4]

numbers
// [1, 2, 3]
```
map方法接受一个函数作为参数。该函数调用时，map方法向它传入三个参数：当前成员、当前位置和数组本身。

```
[1, 2, 3].map(function(elem, index, arr) {
  return elem * index;
});
// [0, 2, 6]
```
map方法还可以接受第二个参数，用来绑定回调函数内部的this变量

```
var arr = ['a', 'b', 'c'];
[1, 2].map(function (e) {
  return this[e];
}, arr)
// ['b', 'c']
```
上面代码通过map方法的第二个参数，将回调函数内部的this对象，指向arr数组。
### Object.keys() & forEach()
Object.keys()用于获得由对象属性名组成的数组，可与数组遍历相结合使用，用起来效果杠杠滴。

```
//创建对象
var person = {
    ChineseName: "wjh",
    EnglishName: "Bruce",
    age: 10,
    school:"HQ University"
}
//用forEach()进行遍历
var keys = Object.keys(person).forEach(function (tz) {
    console.log("person ", tz, ": ", person[tz]);
});
```
把url参数的类对象模式转化为对象

```
let keys = Object.keys( to.query );
if ( keys.length ){
		query = '?';
		keys.forEach( key => {
		if( keys.length == 1 ){
				query += `${key}=${to.query[key]}`
		}else{
			   query += `${key}=${to.query[key]}&`
		}
	})
}			
```
### filter()
filter方法用于过滤数组成员，满足条件的成员组成一个新数组返回。

它的参数是一个函数，所有数组成员依次执行该函数，返回结果为true的成员组成一个新数组返回。该方法不会改变原数组。

```
[1, 2, 3, 4, 5].filter(function (elem) {
  return (elem > 3);
})
// [4, 5]
```
### some()，every()
这两个方法类似“断言”（assert），返回一个布尔值，表示判断数组成员是否符合某种条件。

它们接受一个函数作为参数，所有数组成员依次执行该函数。该函数接受三个参数：当前成员、当前位置和整个数组，然后返回一个布尔值。

some方法是只要一个成员的返回值是true，则整个some方法的返回值就是true，否则返回false。
every方法是所有成员的返回值都是true，整个every方法才返回true，否则返回false。
是否是苹果机型的判断

```
isIphone:function(){
			let ua = navigator.userAgent,
				Agents = ["iPhone", "iPad", "iPod"];
			return Agents.some(function(v){
				return new RegExp(v,'i').test(ua);
			})
		}
```
### reduce()
reduce方法和reduceRight方法的第一个参数都是一个函数。该函数接受以下四个参数。
我们在reduce的第二个参数里面初始了回调函数第一个参数的类型和值
获取url任意参数值的方法

```
getParams:function(){
            var search = location.href.split("?")[1];
            if(!search){
                return {};
            }
            var params = search.split("&");
            return params.reduce(function(res,items){
                var parts = items.split("=");
                res[parts[0]] = parts[1] + "";
                return res;
            },{});
        },
```
{}初始化res为空对象
## Date对象
### Date()
Date对象可以作为普通函数直接调用，返回一个代表当前时间的字符串。
`·Tue Jun 26 2018 10:19:28 GMT+0800 (中国标准时间)·`
这个对象里面可以加各种参数，返回的都是如上字符串显示
Date还可以当作构造函数使用。对它使用new命令，会返回一个Date对象的实例。如果不加参数，实例代表的就是当前时间。
`new Date();`
使用

```
dateFormat:function(time){
                time = time * 1000 ;
                time = new Date(time);
                var year = time.getFullYear(),
                    month = time.getMonth() + 1,
                    date = time.getDate(),
                    hour = time.getHours(),
                    minute = time.getMinutes(),
                    second = time.getSeconds(),
                    m = month >= 10 ? month : "0" + month,
                    d = date >= 10 ? date : "0" + date,
                    h = hour >= 10 ? hour : "0" + hour,
                    mt = minute >= 10 ? minute : "0" + minute,
                    s = second >= 10 ? second : "0" + second;
                return year + "-" + m + "-" + date + " " + h + ":" + mt + ":" + s;
            }
```
### Date.now()
Date.now方法返回当前时间距离时间零点（1970年1月1日 00:00:00 UTC）的毫秒数，相当于 Unix 时间戳乘以1000。
### Date.parse()
Date.parse方法用来解析日期字符串，返回该时间距离时间零点（1970年1月1日 00:00:00）的毫秒数。

```
Date.parse('2018/06/26 23:13:40')
1530026020000
```
### Date.UTC()
Date.UTC方法接受年、月、日等变量作为参数，返回该时间距离时间零点（1970年1月1日 00:00:00 UTC）的毫秒数。

```
// 用法
Date.UTC(2011, 0, 1, 2, 3, 4, 567)
// 1293847384567
```
该方法的参数用法与Date构造函数完全一致，比如月从0开始计算，日期从1开始计算。区别在于Date.UTC方法的参数，会被解释为 UTC 时间（世界标准时间），Date构造函数的参数会被解释为当前时区的时间。

http://javascript.ruanyifeng.com/stdlib/date.html
## RegExp对象
如果正则表示式包含圆括号（即含有“组匹配”），则返回的数组会包括多个成员。第一个成员是整个匹配成功的结果，后面的成员就是圆括号对应的匹配成功的组。也就是说，第二个成员对应第一个括号，第三个成员对应第二个括号，以此类推。整个数组的length属性等于组匹配的数量再加1。
例子：

```
var type = function (o){
  var s = Object.prototype.toString.call(o);
  return s.match(/\[object (.*?)\]/)[1].toLowerCase();
};

type({}); // "object"
type([]); // "array"
```
\s 匹配空格 （包括换行符、制表符、空格符等），相等于[ \t\r\n\v\f]
（1）点字符（.)
点字符（.）匹配除回车（\r）、换行(\n) 、行分隔符（\u2028）和段分隔符（\u2029）以外的所有字符。
（2）位置字符
位置字符用来提示字符所处的位置，主要有两个字符。
^ 表示字符串的开始位置
$ 表示字符串的结束位置
（3）选择符（|）
竖线符号（|）在正则表达式中表示“或关系”（OR），即cat|dog表示匹配cat或dog。
正则表达式中，需要反斜杠转义的，一共有12个字符：^、.、[、$、(、)、|、*、+、?、{和\\。

字符类
字符类（class）表示有一系列字符可供选择，只要匹配其中一个就可以了。所有可供选择的字符都放在方括号内，比如[xyz] 表示x、y、z之中任选一个匹配。
量词符
量词符用来设定某个模式出现的次数。

? 问号表示某个模式出现0次或1次，等同于{0, 1}。
* 星号表示某个模式出现0次或多次，等同于{0,}。
+ 加号表示某个模式出现1次或多次，等同于{1,}。

预定义模式指的是某些常见模式的简写方式。
\d 匹配0-9之间的任一数字，相当于[0-9]。
\D 匹配所有0-9以外的字符，相当于[^0-9]。
\w 匹配任意的字母、数字和下划线，相当于[A-Za-z0-9_]。
\W 除所有字母、数字和下划线以外的字符，相当于[^A-Za-z0-9_]。
\s 匹配空格（包括换行符、制表符、空格符等），相等于[ \t\r\n\v\f]。
\S 匹配非空格的字符，相当于[^ \t\r\n\v\f]。
\b 匹配词的边界。
\B 匹配非词边界，即在词的内部。
重复类
模式的精确匹配次数，使用大括号（{}）表示。{n}表示恰好重复n次，{n,}表示至少重复n次，{n,m}表示重复不少于n次，不多于m次。
量词符
量词符用来设定某个模式出现的次数。

? 问号表示某个模式出现0次或1次，等同于{0, 1}。
* 星号表示某个模式出现0次或多次，等同于{0,}。
+ 加号表示某个模式出现1次或多次，等同于{1,}。

上一小节的三个量词符，默认情况下都是最大可能匹配，即匹配直到下一个字符不满足匹配规则为止。这被称为贪婪模式。
var s = 'aaa';
s.match(/a+/) // ["aaa"]
上面代码中，模式是/a+/，表示匹配1个a或多个a，那么到底会匹配几个a呢？因为默认是贪婪模式，会一直匹配到字符a不出现为止，所以匹配结果是3个a。

如果想将贪婪模式改为非贪婪模式，可以在量词符后面加一个问号。

var s = 'aaa';
s.match(/a+?/) // ["a"]
*?：表示某个模式出现0次或多次，匹配时采用非贪婪模式。
+?：表示某个模式出现1次或多次，匹配时采用非贪婪模式。
## 包装对象
使用双重的否运算符（!）也可以将任意值转为对应的布尔值。

```
!!undefined // false
!!null // false
!!0 // false
!!'' // false
!!NaN // false
!!1 // true
!!'false' // true
!![] // true
!!{} // true
!!function(){} // true
!!/foo/ // true
```
Number、String和Boolean如果不作为构造函数调用（即调用时不加new），常常用于将任意类型的值转为数值、字符串和布尔值。

```
Number(123) // 123
String('abc') // "abc"
Boolean(true) // true
```
总结一下，这三个对象作为构造函数使用（带有new）时，可以将原始类型的值转为对象；作为普通函数使用时（不带有new），可以将任意类型的值，转为原始类型的值。
### Boolean 函数的类型转换作用
Boolean对象除了可以作为构造函数，还可以单独使用，将任意值转为布尔值。这时Boolean就是一个单纯的工具方法。
对于一些特殊值，Boolean对象前面加不加new，会得到完全相反的结果，必须小心

```
if (Boolean(false)) {
  console.log('true');
} // 无输出

if (new Boolean(false)) {
  console.log('true');
} // true

if (Boolean(null)) {
  console.log('true');
} // 无输出

if (new Boolean(null)) {
  console.log('true');
} // true
```
## JSON对象
### Number.prototype.toFixed()
toFixed方法先将一个数转为指定位数的小数，然后返回这个小数对应的字符串。

```
(10).toFixed(2) // "10.00"
10.005.toFixed(2) // "10.01"
```
使用：转化时间

```
getTime(time){
    return (time/3600).toFixed(2) + '小时';
}
```
## Math对象
Math是 JavaScript 的原生对象，提供各种数学功能。该对象不是构造函数，不能生成实例，所有的属性和方法都必须在Math对象上调用。
Math对象提供以下一些静态方法。

```
Math.abs()：绝对值
Math.ceil()：向上取整
Math.floor()：向下取整
Math.max()：最大值
Math.min()：最小值
Math.pow()：指数运算
Math.sqrt()：平方根
Math.log()：自然对数
Math.exp()：e的指数
Math.round()：四舍五入
Math.random()：随机数
```
### Math.floor()
方法返回小于参数值的最大整数（地板值）。

```
Math.floor(3.2) // 3
Math.floor(-3.2) // -4
```
时间倒数取整

```
h = Math.floor(leftTime/1000/60/60%48),
m = Math.floor(leftTime/1000/60%60),
s = Math.floor(leftTime/1000%60);
```
### Math.ceil()
方法返回大于参数值的最小整数（天花板值）。

```
Math.ceil(3.2) // 4
Math.ceil(-3.2) // -3
```
### Math.round()
Math.round方法用于四舍五入。
注意，它对负数的处理（主要是对0.5的处理）。

```
Math.round(-1.1) // -1
Math.round(-1.5) // -1
Math.round(-1.6) // -2
```
### Object.prototype.toLocaleString()
这个方法的主要作用是留出一个接口，让各种不同的对象实现自己版本的toLocaleString，用来返回针对某些地域的特定的值。目前，主要有三个对象自定义了toLocaleString方法。
返回当地时间的写法模式

```
dateFormat:function(time){
            var unixTimestamp = new Date( time*1000 ) ;
            var commonTime;
            commonTime = unixTimestamp.toLocaleString("zh-CN");
            return commonTime;
        }
```

```
var type = function (o){
  var s = Object.prototype.toString.call(o);
  return s.match(/\[object (.*?)\]/)[1].toLowerCase();
};

type({}); // "object"
type([]); // "array"
type(5); // "number"
type(null); // "null"
type(); // "undefined"
type(/abcd/); // "regex"
type(new Date()); // "date"
```
#### location
`let curPath = location.pathname;`
如：

```
http://localhost:8081/courseIndex?channel=123&lin=linziying
location.pathname
"/courseIndex"
location.search
"?channel=1524653365&lin=linziying"

http://localhost:8081/home/index/index
location.pathname
"/home/index/index"
location.origin
"http://localhost:8081"
location.host
"localhost:8081"
```
### replace
是字符串写在前面，字符串的实例方法
String.prototype.replace()：按照给定的正则表达式进行替换，返回替换后的字符串
字符串对象的replace方法可以替换匹配的值。它接受两个参数，第一个是正则表达式，表示搜索模式，第二个是替换的内容。
下面再来说location.replace
location.replace 是替换当前页面，如果a页面 跳转到 b页面  b页面 写location.replace（c） 当点击返回按钮时，就会回到a页面，换句话说，location.replace 是清除了本页面的访问记录，直接回到上一个页面
如果是 b页面href到c页面的话，按c页面的回退按钮，会回到b页面
### history 对象
history.replaceState

```
if( window.history && history.pushState ){
	history.replaceState({state:1},'','/usercateList');
}
```
## DOM 模型概述

## 错误处理机制
### try…catch 结构
一旦发生错误，程序就中止执行了。JavaScript 提供了try...catch结构，允许对错误进行处理，选择是否往下执行。

如果你不确定某些代码是否会报错，就可以把它们放在try...catch代码块之中，便于进一步对错误进行处理。因为一旦报错js会阻塞，然后就卡在那里不执行。
例如json.parse方法，ocalStorage.setItem在iphone的混合模式下会报错，以及下面这个

```
dateFormat:function(time){
    var unixTimestamp = new Date( time*1000 ) ;
    var commonTime;
    try{
        commonTime = unixTimestamp.toLocaleString("zh-CN");
    }catch(err){
        console.log(err);
        commonTime = unixTimestamp.toLocaleString();
    }
    return commonTime;
},
```
### document.referrer
此返回上一页相关HTML代码如下：

```
<a href="javascript:history.go(-1)" class="header-back jsBack">返回</a>
```
在大部分场景下，上面办法可以满足我们的交互需求，但是，在有些时候，上面的代码就有些心有力而气不足，因为当前页面的referrer并不总是存在的。

比方说用户是通过微信分享进来的，直接进入了内页，此时是没有上一页的，返回按钮再怎么点击都没有任何反应，就会让用户很奇怪，十有八九会认为是实现了bug，则会让用户对产品的品质抱有疑虑，那问题可就大了。

怎么办呢！后来我想了一招，那就是如果发现浏览器没有上一页来源信息，我们就把返回按钮的链接改成首页的链接地址，这样无论什么时候，用户点击返回按钮一定是会有反应的，并且返回首页从逻辑上讲也是合情合理的。而这里判断是否有没有来源信息就是使用这里的document.referrer，当浏览器得不到上一页的来源信息的时候，document.referrer的返回值就是空字符串''，于是乎，就有类似下面的代码：

```
if (document.referrer === '') {
    // 没有来源页面信息的时候，改成首页URL地址
    $('.jsBack').attr('href', '/');
}
```
### ajax与HTML5 history pushState/replaceState实例

```

JS代码：
var eleMenus = $("#choMenu a").bind("click", function(event) {
    var query = this.href.split("?")[1];
    if (history.pushState && query && !$(this).hasClass(clMenuOn)) {
        /*
            ajax载入~~
        */
        
        // history处理
        var title = "上海3月开盘项目汇总-" + $(this).text().replace(/\d+$/, "");
        document.title = title;
        if (event && /\d/.test(event.button)) {            
            history.pushState({ title: title }, title, location.href.split("?")[0] + "?" + query);
        }
    }
    return false;
});

var fnHashTrigger = function(target) {
    var query = location.href.split("?")[1], eleTarget = target || null;
    if (typeof query == "undefined") {
        if (eleTarget = eleMenus.get(0)) {
            // 如果没有查询字符，则使用第一个导航元素的查询字符内容
            history.replaceState(null, document.title, location.href.split("#")[0] + "?" + eleTarget.href.split("?")[1]) + location.hash;    
            fnHashTrigger(eleTarget);
        }
    } else {
        eleMenus.each(function() {
            if (eleTarget === null && this.href.split("?")[1] === query) {
                eleTarget = this;
            }
        });
        
        if (!eleTarget) {
            // 如果查询序列没有对应的导航菜单，去除查询然后执行回调
            history.replaceState(null, document.title, location.href.split("?")[0]);    
            fnHashTrigger();
        } else {
            $(eleTarget).trigger("click");
        }        
    }    
};
if (history.pushState) {
    window.addEventListener("popstate", function() {
        fnHashTrigger();                             
    });
    
    // 默认载入
    fnHashTrigger();
}
```
```
history.pushState({page: 4}, "title 1", "");
				if ( window.history && window.history.pushState ) {
					window.addEventListener("popstate", function () {
						history.replaceState({page: 4}, "title 1", location.href);
						location.href = "/";
					});
				}
```
pushState会促发popstate事件监听，pushState会在历史里面添加记录，返回时是进入到这个地址，而replaceState虽然能改变浏览器url地址但是并不会添加进历史，回退不会进入相应的地址。
### 类的使用

