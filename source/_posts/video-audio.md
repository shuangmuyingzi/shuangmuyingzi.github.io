title: H5 API audio&video
date: 2018-10-28
tag:
 - js
 - h5

photos:
 - /img/2017/2.jpg 

---

随着网页元素的多元化，多形态的发展，音频跟视频自然是用得越来越多了，本文记录下H5的audio以及video两者的属性跟方法的使用。

<!--more-->

`video`标签专门用来播放网络上的视频或电影，`audio`标签则专门用来播放网络上的音频数据。使用这两个标签，就不再需要使用其他的任何插件了，只要使用支持 HTML5 的浏览器就可以了。

# video属性、方法规范

### video的控件样式控制
这里所说的修改video标签中自带按钮的默认样式，指的是用css就可以控制视频播放按钮的大小等

如何查看 video 的内部构造：
chrome 下，开发者工具  setting  Preferences  Elements  勾选 "Show user agent shadow DOM"

![GitHub set up](https://shuangmuyingzi.github.io/img/video1.png)

### video的属性

```
<video
  id="video" 
  src="video.mp4" 
  controls = "true"
  poster="images.jpg" /*视频封面*/
  preload="auto" 
  webkit-playsinline="true" /*这个属性是ios 10中设置可以让视频在小窗内播放，也就是不是全屏播放*/  
  playsinline="true"  /*IOS微信浏览器支持小窗内播放*/ 
  x-webkit-airplay="allow" 
  x5-video-player-type="h5"  /*启用H5播放器,是wechat安卓版特性*/
  x5-video-player-fullscreen="true" /*全屏设置，设置为 true 是防止横屏*/
  x5-video-orientation="portraint" //播放器支付的方向， landscape横屏，portraint竖屏，默认值为竖屏
  style="object-fit:fill">
</video>
```
* src: 视频的地址

* controls: 加上这个属性，Gecko 会提供用户控制，允许用户控制视频的播放，包括音量，跨帧，暂停/恢复播放。

* poster: 属性规定视频下载时显示的图像，或者在用户点击播放按钮前显示的图像。如果未设置该属性，则使用视频的第一帧来代替。

* preload: 属性规定在页面加载后载入视频。

* webkit-playsinline和playsinline: 视频播放时局域播放，不脱离文档流 。但是这个属性比较特别， 需要嵌入网页的APP比如WeChat中UIwebview 的allowsInlineMediaPlayback = YES webview.allowsInlineMediaPlayback = YES，才能生效。换句话说，如果APP不设置，你页面中加了这标签也无效，这也就是为什么安卓手机WeChat 播放视频总是全屏，因为APP不支持playsinline，而ISO的WeChat却支持。
这里就要补充下，如果是想做全屏直播或者全屏H5体验的用户，IOS需要设置删除 webkit-playsinline 标签，因为你设置 false 是不支持的 ，安卓则不需要，因为默认全屏。但这时候全屏是有播放控件的，无论你有没有设置control。 做直播的可能用得着播放控件，但是全屏H5是不需要的，那么去除全屏播放时候的控件，需要以下设置：同层播放

* x-webkit-airplay="allow" : 这个属性应该是使此视频支持ios的AirPlay功能。使用AirPlay可以直接从使用iOS的设备上的不同位置播放视频、音乐还有照片文件，也就是说通过AirPlay功能可以实现影音文件的无线播放，当然前提是播放的终端设备也要支持相应的功能

* x5-video-player-type: 启用同层H5播放器，就是在视频全屏的时候，div可以呈现在视频层上，也是WeChat安卓版特有的属性。同层播放别名也叫做沉浸式播放，播放的时候看似全屏，但是已经除去了control和微信的导航栏，只留下"X"和"<"两键。目前的同层播放器只在Android（包括微信）上生效，暂时不支持iOS。至于为什么同层播放只对安卓开放，是因为安卓不能像ISO一样局域播放，默认的全屏会使得一些界面操作被阻拦，如果是全屏H5还好，但是做直播的话，诸如弹幕那样的功能就无法实现了，所以这时候同层播放的概念就解决了这个问题。不过在测试的过程中发现，不同版本的IOS和安卓效果略有不同

* x5-video-orientation: 声明播放器支持的方向，可选值landscape 横屏, portraint竖屏。默认值portraint。无论是直播还是全屏H5一般都是竖屏播放，但是这个属性需要x5-video-player-type开启H5模式

* x5­-video­-player­-fullscreen:全屏设置。它又两个属性值，ture和false，true支持全屏播放，false不支持全屏播放。其实，IOS 微信浏览器是Chrome的内核，相关的属性都支持，也是为什么X5同层播放不支持的原因。安卓微信浏览器是X5内核，一些属性标签比如playsinline就不支持，所以始终全屏。

### video 的事件
video 支持的事件很多，但在有些事件在不同的系统上跟预想的表现不一致，在尝试比较之后，使用 timeupdate 和 ended 这两个事件基本可以满足需求

```
video.addEventListener('timeupdate', function (e) {
  console.log(video.currentTime) // 当前播放的进度
})
video.addEventListener('ended', function (e) {
  // 播放结束时触发
})
```

### 应用
知道了video的一些属性方法之后，我们来看看具体的一些使用场景。
1、统一为固定在某一个区域播放，非全屏

```
<video class="view-cover"
		controls
	   id="my-video"
		x5-video-player-type="h5"
		webkit-playsinline
		playsinline
		@ended="videoPlayEnd"
		@touchmove.stop.prevent=""
		:poster="posterImage"
>
</video>
```
但是IOS在QQ浏览器会吊起整个播放器，凌驾于任何弹框之上，还可以引用一个库iphone-inline-video（具体用法很简单看它github，这里不介绍了，只需加js一句话，css加点），github地址https://github.com/bfred-it/iphone-inline-video 加上playsinline webkit-playsinline这两个属性和这个库基本可以保证ios端没有问题了（不过亲测，只加webkit-playsinline playsinline这两个属性不引入库好像也是ok的，至今没有在ios端微信没有出现问题，如果你要兼容uc或者qq的浏览器建议带上这个库），具体还没试过。
2、点击某个区域全屏播放

```
<video
		width="100%"
		ref="video"
		controls
		x5-video-player-type="h5"
		x5-video-player-fullscreen='true'
		:src="answer.video_url"
		@play="videoPlay"
		@pause="videoPaused"
		preload="auto">
</video>
```


