title: 重拾那些逐渐退化的CSS能力
date: 2018-03-28 22:01
modified: 2018-03-29 21:01
tag:
 - css

photos:
 - /img/2017/3.jpg 

---

在这个js当道的前端届里，号称要用js写操作系统，js真的牛逼啊，写得了前端，做得了后台，码得了客户端桌面应用，不管是小程序还是新出的快应用统统都离不开js,谁还记得曾经的那个页面仔。与一个像素去较真，写了不知道多少牛逼哄哄的hack，多少个日与夜在做IE兼容。如今你有多久没有关注过html、css的更新，你的css能力退化了吗？

<!--more-->

### 源起
span元素是否可以用padding撑开宽高，”不能啊”！真的吗，确定没有记错吗，来，实验是检验真理的唯一标准。

```html
<!DOCTYPE html>
<html>
<head>
	<title></title>
</head>
<style type="text/css">
	body{margin: 0;padding: 0}
	div{
		color: #000;
		/*margin-bottom: 30px;*/ /*取消注释第二步，运行看效果*/
	}
	span{
		padding:20px;
		background-color: #000;
		color: #fff;
		text-align: center;
		font-size: 20px;
		margin:20px;
	}
	
</style>
<body>
	<!-- <div>神奇啊</div> --> <!-- 取消注释第一步，运行看效果 -->
	<span>我是span</span>	
</div>
</body>
</html>
```
由此我们可以得出：

```
span设置内外边距问题
1、margin：
span标签设置margin-left/right均好使，margin-top/bottom不好使；
2、padding： 
span标签设置padding好使，但span标签上面无元素时，padding-top 不好使；span标签上面有元素时，padding-top会把上面的元素盖住.
```
天啊，太神奇了！太神奇了！为什么，为什么啊？？？？
等等，我还有疑问，那span可以设置line-height撑开里面的高度吗？
### html元素的标签分类
曾经我的字典里只有行内元素和块级元素，inline & block，事实上：
![GitHub set up](https://shuangmuyingzi.github.io/img/cssTu.png)
HTML5中，元素主要分为7类：
Metadata
Flow
Sectioning
Heading
Phrasing
Embedded
Interactive
这些分类集合互相之间也存在一定的交集(一个元素可以同时属于多个分类)
#### Metadata（元数据元素）
顾名思义，Metadata元素意指那些定义文档元数据信息的元素 — 其作用包括：影响文档中其它节点的展现与行为、定义文档与其它外部资源之间的关系等。

```
base, link, meta, noscript, script, style, template, title
```
#### Flow（流式元素）
所有可以放在body标签内，构成文档内容的元素均属于Flow元素。因此，除了base, link, meta, style, title等只能放在head标签内的元素外，剩下的所有元素均属于Flow元素。

```
a， abbr， address， area（如果它是map元素的后裔）， article， aside， audio， b， bdi， bdo， blockquote， br， button， canvas， cite， code， command， datalist， del， details， dfn， div， dl，em， embed， fieldset， figure， footer， form， h1， h2， h3， h4， h5， h6， header， hgroup， hr， i， iframe， img， input， ins， kbd， keygen， label， map， mark， math， menu， meter，nav， noscript， object， ol， output， p， pre， progress， q， ruby， s， samp， script， section， select， small， span， strong， style（如果该元素设置了scoped属性）， sub， sup， svg， table，textarea， time， u， ul， var， video， wbr， text
```
#### Sectioning（章节元素）
Sectioning意指定义页面结构的元素

```
article, aside, nav, section
```
#### Heading（标题元素）
所有标题元素属于Heading

```
h1, h2, h3, h4, h5, h6
```

#### Phrasing（段落元素）
所有可以放在p标签内，构成段落内容的元素均属于Phrasing元素。因此，所有Phrasing元素均属于Flow元素。在HTML5标准文档中，关于Phrasing元素的原始定义为：

```
Phrasing content is the text of the document, as well as elements that mark up that text at the intra-paragraph level. Runs of phrasing content form paragraphs.
```
对于这一定义，个人认为不应当使用“text”这一容易引起误解的词，事实上，一个元素即使不是文本，只要能包含在p标签中成为段落内容的一部分，就可以称之为Phrasing元素。

```
a（如果其只包含段落式元素）， abbr， area（如果它是map元素的后裔）， audio， b， bdi， bdo， br， button， canvas， cite， code， command， datalist， del（如果其只包含段落式元素）， dfn， em， embed， i，iframe， img， input， ins（如果其只包含段落式元素）， kbd， keygen， label， map（如果其只包含段落式元素）， mark， math， meter， noscript， object， output， progress， q， ruby， s， samp， script，select， small， span， strong， sub， sup， svg， textarea， time， u， var， video， wbr， text
```
#### Embedded（嵌入元素）
所有用于在网页中嵌入外部资源的元素均属于Embedded元素

```
audio, video, img, canvas, svg, iframe, embed, object, math
```
#### Interactive（交互元素）
所有与用户交互有关的元素均属于Interactive元素。

```
a， audio（如果设置了controls属性）， button， details， embed， iframe， img（如果设置了usemap属性）， input（如果type属性不为hidden状态）， keygen， label， menu（如果type属性为toolbar状态），object（如果设置了usemap属性）， select， textarea， video（如果设置了controls属性）
```
#### Palpable
所有应当拥有子元素的元素称之为Palpable元素。比如，br元素因不需要子元素，因此也就不属于Palpable。

Script-supporting
自身不做任何页面展现，但与页面脚本相关的元素

```
script, template
```
其实这是非规范的，所以不是强求的，新的分类定义了元素的内容模型(Content Model),关键还是要清楚这些元素的特性以及各大浏览器是如何渲染解析的，包括有哪些默认的样式,从而正确嵌套标签以及设置reset.css。
https://html.spec.whatwg.org/multipage/indices.html#element-content-categories
### W3C 标准盒模型 & IE 怪异盒模型
页面上显示的每个元素（包括内联元素）都可以看作一个盒子，即盒模型(box model) 盒模型由 4 部分组成，从内到外分别是：`content` `padding` `border` `margin`

![GitHub set up](https://shuangmuyingzi.github.io/img/box-sizing.png)
`W3C标准盒模型`一个元素的宽度（高度以此类推）应该这样计算：

```
一个元素的宽度 =  content
盒子总宽度 = margin-left + border-left + padding-left + width + padding-right + border-right + margin-right
```
`而IE 怪异盒模型`一个元素的宽度（高度以此类推）却是这样计算的：

```
一个元素的宽度 =  content + padding + border
盒子总宽度 = margin-left + width + margin-right
```
#### 解决方案 box-sizing
```
// W3C 标准盒模型（浏览器默认）
box-sizing: content-box;
// IE 怪异盒模型
box-sizing: border-box;
```
当我们设置 box-sizing: border-box; 时，border 和 padding 就被包含在了宽高之内,和IE在非标准的模式下是一致的。
然而好像看到很多网站为了避免同一份 css 在不同浏览器下表现不同，基本会加上：

```
*, *:before, *:after {
  -moz-box-sizing: border-box;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
}
```
#### 文档类型（Doctype）
亲测了下，发现其实当头部设置了html5的头部声明<!DOCTYPE html>，在IE下也全是标准的盒模型了，估计除了历史遗留下的网站，新的全是html5的声明，HTML5 不基于 SGML，所以不需要引用 DTD。可能也就只有一些老家伙才经历过图下的历史：
![GitHub set up](https://shuangmuyingzi.github.io/img/doctype.png)
如果有兴趣推荐看这篇关于声明的具体元素意思，毕竟其实很多医院系统，学校系统，政府系统之类可能还是会有旧的，了解下也好。
https://www.jianshu.com/p/c3dcdad42e6d

#### 理解CSS布局和BFC
CSS布局中有一些概念，一旦理解了这些概念，就能真正的提高你的CSS能力。这篇文章主要介绍的是关于块格式化上下文（Block Formatting Context），也就是大家俗称的BFC。

它是W3C CSS2.1规范中的一个概念。它是页面中的一块渲染区域，并且有一套渲染规则，它决定了其子元素将如何定位，以及和其他元素的关系和相互作用。也就是说我们平时在布局的时候，它默默地提供了一个环境，使得HTML元素在这个环境中按照一定规则进行布局。

最常见的formatting context有Block Formatting Context(BFC)和Inline Formatting Context(IFC)，CSS3中还增加了GridLayout Formatting Context(GFC)和Flex Formatting Context(FFC)。

BFC的布局规则与触发规则
刚才我们说到BFC中的元素有一套规定的布局规则：

```
内部的元素会在垂直方向一个接一个地放置
元素垂直方向的距离由margin决定，属于同一个BFC的两个相邻元素的margin会发生重叠
每个元素的左外边距与包含块的左边界相接触(对于从左往右，否则相反)，即使存在浮动也是如此
BFC的区域不会与float元素重叠
计算BFC的高度时，浮动元素也参与计算
BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素，反之也如此
```
刚才我们又提到BFC是一块渲染区域，那这块渲染区域到底在哪，它又是有多大，这些由生成BFC的元素来决定，CSS2.1中规定满足下列CSS声明的元素便会生成BFC（触发规则）：

```
根元素
float的值不为none
overflow的值不为visible
position的值为absolute或fixed
display的值为inline-block, table-cell, table-caption, flex, inline-flex, grid
注：display: table也认为可以生成BFC，主要原因是table会默认生成一个匿名的table-cell，正是这个匿名的table-cell生成了BFC
```
上面这些CSS声明的元素生成了BFC，而它们本身并不是BFC，这一点需要区分。
#### 防止垂直margin重叠
有点布局经验的朋友都知道margin collapse，也就是相邻的垂直元素同时设置了margin后，实际margin值会塌陷到其中较大的那个值。其根本原理就是它们处于同一个BFC，符合“属于同一个BFC的两个相邻元素的margin会发生重叠”的规则。

我们可以在其中一个元素外面包裹一层容器，并触发该容器生成一个BFC。那么两个元素便属于不同的BFC，就不会发生margin重叠了：

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>防止垂直margin重叠</title>
</head>
<style>
    /*.wrap{
        overflow: hidden;
    }
    p{
        width: 200px;
        line-height: 100px;
        margin: 100px;
        background: #000;
        color: #fff;
        text-align: center;
    }*/
    .outer { background-color: #ccc; margin: 0 0 40px 0; /*overflow: auto;*/} 
    p { padding: 0; margin: 20px 0 20px 0; background-color: rgb(233,78,119); color: #fff; }
</style>
<body>
    <!-- <p>我属于一个BFC</p>
    <div class="wrap">
        <p>我属于另一个BFC</p>
    </div> -->
    <div class="outer">
        <p>我是P</p>
        <p>我是另一个P</p>
    </div>
</body>
</html>

```
防止浮动子元素高度塌陷

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>防止浮动子元素高度塌陷</title>
</head>
<style>
    .outer { border: 5px dotted rgb(214,129,137); border-radius: 5px; width: 450px; padding: 10px; margin-bottom: 40px;/*overflow: hidden;*/ } 
    .float { padding: 10px; border: 5px solid rgba(214,129,137,.4); border-radius: 5px; background-color: rgba(233,78,119,.4); color: #fff; float: left; width: 200px; margin: 0 20px 0 0; }
</style>
<body>
    <div class="outer"> 
    	<div class="float">我是浮动</div> 
    	我会包裹着浮动
    </div>
</body>
</html>
```
如果我们将.outer元素的overflow: hidden去掉，那么.outer元素就获取不到浮动元素的高度了。但是加上overflow属性后触发了BFC，计算BFC的高度时，浮动元素也参与了计算。

防止文字（或其他元素）环绕

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>防止文字（或其他元素）环绕</title>
</head>
<style>
    .parent{
        width: 300px;
        border: 3px solid #000;
    }
    .child{
        float: left;
        width: 100px;
        height: 100px;
        border: 3px solid #f00;
        color: #f00;
    }
    .text{
        /*overflow: hidden;*/
    }
</style>
<body>
    <div class="parent">
        <div class="child">float: left</div>
        <div class="text">我只是文字但我不会环绕我只是文字但我不会环绕我只是文字但我不会环绕我只是文字但我不会环绕我只是文字但我不会环绕我只是文字但我不会环绕我只是文字但我不会环绕我只是文字但我不会环绕我只是文字但我不会环绕我只是文字但我不会环绕</div>
    </div>
</body>
</html>
```
正常情况下，如果一个块级元素设置成了float，那么他的兄弟元素会环绕其布局。这里我们给.text加上overflow，文字所在的区域就产生了BFC，元素的左边总是触碰到容器的左边，即使存在浮动也是如此。

以上的都是BFC常见的应用场景以及它的触发规则，
接下来我们来看看BFC新的触发条件。

#### 创建BFC的新方式
使用overflow或其他的方法创建BFC时会有两个问题。首先，这些方法本身是有自身的设计目的，所以在使用它们创建BFC时可能会产生副作用。例如，使用overflow创建BFC后在某些情况下可能会看到出现一个滚动条或者元素内容被裁切。这是由于overflow属性的设计是用来让你告诉浏览器如何定义元素的溢出状态的。

最安全的做法应该是创建一个BFC时并不会带来任何副作用，它内部的元素都安全的呆在这个迷你布局中，这种方法不会引起任何意想不到的问题，也可以理解开发者的意图。CSS工作组也十分认同这种想法，所以他们定制了一个新的属性值：display:flow-root。

![GitHub set up](https://shuangmuyingzi.github.io/img/flow-root.png)

Caniuse上display:flow-root各浏览器兼容情况。

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>flow-root</title>
</head>
<style>
    .parent{
        width: 300px;
        border: 3px solid #000;
    }
    .child{
        float: left;
        width: 100px;
        height: 100px;
        border: 3px solid #f00;
        color: #f00;
    }
    .text{
        display: flow-root;
    }
</style>
<body>
    <div class="parent">
        <div class="child">float: left</div>
        <div class="text">我只是文字但我不会环绕我只是文字但我不会环绕我只是文字但我不会环绕我只是文字但我不会环绕我只是文字但我不会环绕我只是文字但我不会环绕我只是文字但我不会环绕我只是文字但我不会环绕我只是文字但我不会环绕我只是文字但我不会环绕</div>
    </div>
</body>
</html>
```
vertical-center(水平垂直居中)
仅居中元素定宽高适用：

[absolute + 负margin](http://yanhaijing.com/vertical-center/absolute1.html)
[absolute + margin auto](http://yanhaijing.com/vertical-center/absolute2.html)
[absolute + calc](http://yanhaijing.com/vertical-center/absolute3.html)
居中元素不定宽高适用：

[absolute + transform](http://yanhaijing.com/vertical-center/absolute4.html)
[writing-mode](http://yanhaijing.com/vertical-center/writing-mode.html)
[lineheight](http://yanhaijing.com/vertical-center/lineheight.html)
[table](http://yanhaijing.com/vertical-center/table.html)
[css-table](http://yanhaijing.com/vertical-center/css-table.html)
[flex](http://yanhaijing.com/vertical-center/flex.html)
[grid](http://yanhaijing.com/vertical-center/grid.html)
参考链接：
https://www.w3cplus.com/css/understanding-css-layout-block-formatting-context.html
https://segmentfault.com/a/1190000011211625

