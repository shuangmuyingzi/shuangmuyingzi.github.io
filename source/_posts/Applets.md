title: wepy微信小程序问题记录
date: 2019-3-28
tag:
 - js
 - wepy

photos:
 - /img/2017/2.jpg 

---


<!--more-->

为了使用Web Socket,需要在Web服务器上运行一个Web Socket服务器。这个程序负责协调各方通信，而且启动后就会不间断的运行下去。本厂后端开发语言为php(毕竟是世界上最好的语言)，有兴趣的可以在github上找相关服务端代码阅读。本文只介绍Web Socket客户端的实现。

## 一个聊天室的Web Socket简单客户端

### 父组件调用子组件的方法

```
父：this.$broadcast('destroyAudio');

子：events = {

destroyAudio() {

}

      };
```

### 小程序的下拉加载
```
onReachBottom（）{}
```
### 表单的双向绑定
```
<input bindinput="handleInput" />

handleInput (e) {

        console.log(1)

        this.obj[e.target.dataset.key] = e.detail.value;

 };


```
handleInput需要写在methods里面才可以,要不然可能watch不到改变有点奇怪。
### 使用 Redux进行状态管理
全局数据管理问题：比如在 A 页面用到了全局数据 globalData，由 A 页面跳转到 B 页面，B 页面也用到了 globalData；在 B 页面改变 globalData，返回到 A 页面，发现 globalData 没实时更新到。

通过使用 wepy-redux ，来管理和分发全局数据。

```
import { setStore, getStore } from 'wepy-redux';

import configStore from './store';

import { asyncIsOn, asyncIsIos, asyncUserObj } from './store/actions/counter'



const store = configStore();

setStore(store);

wepy.$store = store;



the same as



wepy.$store.dispatch(asyncUserObj());

```

参考：https://juejin.im/post/5b067f6ff265da0de02f3887

https://www.redux.org.cn/
### fancy-mini无限层级路由解决方案
路由问题，目前小程序最多支持打开10个page。



npm install --save fancy-mini
新建空白页面 pages/curtain/curtain
app.wpy
import './appPlugin'

appPlugin.js
import {registerToThis, registerPageHook, pageRestoreHandler, NavRefine} from 'fancy-mini/lib/wepyKit';

import Navigator from 'fancy-mini/lib/navigate/Navigator';

import {customWxPromisify} from 'fancy-mini/lib/wxPromise';



//无限层级路由方案

Navigator.config({

  enableCurtain: true, //是否开启空白中转策略

  curtainPage: '/pages/curtain/curtain',  //空白中转页



  enableTaintedRestore: true, //是否开启实例覆盖自动恢复策略



  /**

  * 自定义页面数据恢复函数，用于

  * 1. wepy实例覆盖问题，存在两级同路由页面时，前者数据会被后者覆盖，返回时需予以恢复

  * 2. 层级过深时，新开页面会替换前一页面，导致前一页面数据丢失，返回时需予以恢复

  */

  pageRestoreHandler: pageRestoreHandler,



  MAX_LEVEL: 10, //最多同时打开的页面层数



  oriNavOverrides: NavRefine, //自定义覆盖部分/全部底层跳转api，如：此处底层使用wepy定义的路由模块，便于使用wepy提供的prefetch等功能

});

registerPageHook('onUnload', Navigator.onPageUnload);



//wx接口Promise化

let {wxPromise, wxResolve} = (function () {

  let overrides = {  //覆盖wx的部分接口

    navigateTo: Navigator.navigateTo,

    redirectTo: Navigator.redirectTo,

    navigateBack: Navigator.navigateBack,

    reLaunch: Navigator.reLaunch,

    switchTab: Navigator.switchTab,

  };



  return {

    wxPromise: customWxPromisify({overrides, dealFail: false}),

    wxResolve: customWxPromisify({overrides, dealFail: true}),

  }

}());

registerToThis("$wxPromise", wxPromise);

registerToThis("$wxResolve", wxResolve);



export { //导出部分api，方便lib文件使用

  wxPromise,

  wxResolve,

}

github地址：https://github.com/zhuanzhuanfe/fancy-mini/blob/master/docs/%E6%97%A0%E9%99%90%E5%B1%82%E7%BA%A7%E8%B7%AF%E7%94%B1%E6%96%B9%E6%A1%88.md

使用
所有页面不直接调用wx.navigateTo、wx.redirectTo、wx.navigateBack、wx.switchTab、wx.reLaunch，或wepy对应接口，统一改用this.$wxPromise.navigateTo等对应接口
wx.navigateTo({ url: '/pages/index/index'});//错误，无法确保路由过程全部由自定义模块接管

wepy.navigateTo({ url: '/pages/index/index'});//错误，无法确保路由过程全部由自定义模块接管

this.$wxPromise.navigateTo({ url: '/pages/index/index'}); //正确

所有页面跳转由this.$wxPromise相应接口触发，而不使用<navigator>元素
所有页面若有自定义onUnload函数，需在函数中执行父类相应钩子： super.onUnload && super.onUnload()
export default class extends wepy.page {

 //正确，页面中无onUnload函数，会直接执行父类上的统一钩子

}



export default class extends wepy.page {

 onUnload(){//错误，页面中自定义onUnload函数覆盖了统一钩子，影响路由模块监听返回行为

 

 }

}



export default class extends wepy.page {

 onUnload(){//正确，页面自定义onUnload函数中调用了统一钩子

 	super.onUnload && super.onUnload()

 }

}

this.$wxPromise.navigateTo({

url:``

})

github地址：https://github.com/zhuanzhuanfe/fancy-mini/blob/master/docs/%E6%97%A0%E9%99%90%E5%B1%82%E7%BA%A7%E8%B7%AF%E7%94%B1%E6%96%B9%E6%A1%88.md

### 组件传值坑
props传递过去的值，html代码界面中显示正确，但在 onLoad 生命周期中打印 undefined，方法中使用也是 undefined。

在父组件利用 $broadcast，在获取到数据后对数据进行广播，然后在子组件中获取到数据。

```
// 向子组件以广播形式传递answer_uid

this.$broadcast('transferAnswerId', this.answer_uid)



events = {

async transferAnswerId(answerId) {

this.tagsList = await api.teacherCommentTags({

uid: answerId,

});

this.$apply();

}

};



<AudioControl wx:if="{{item.media.media_src}}"

:item.sync="item"

:isLeft.sync="true"

:chatType.sync="2"

></AudioControl>
```
不能是item.下面的属性值传，要整个对象一起传过去

文档中的话：

WePY 1.x 版本中，组件使用的是静态编译组件，即组件是在编译阶段编译进页面的，每个组件都是唯一的一个实例，目前只提供简单的 repeat 支持。不支持在 repeat 的组件中去使用 props, computed, watch 等等特性。

将需要变量渲染的一整组数据直接通过 props 传给子组件，在子组件中使用 repeat 循环渲染。改变数据用子组件通过$emit改变数据源，更新数据。

### methods里面的方法调用的方法要跟methods同级



