---
title: vue全家桶
date: 2018-06-28
photo: https://shuangmuyingzi.github.io/img/npm.jpg
---

vue全家桶收集

<!--more-->

### this.$router.beforeEach((to, from, next)=>{})
```
fullPath:"/answerIndex?answer_uid=11767"
hash:""
matched:[]
meta:{}
name:undefined
params:{}
path:"/answerIndex"
query:{answer_uid: "11767"}
```

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
### export default {}
直接输出对象

```
export default{
    "myname":"linziying",
    "old":18
}
```
### 对于历史原因的做强制跳转

main.js

```
let curPath = location.pathname;
let search = location.search;
if( historyRouterMap[curPath] ){
	location.href = `${location.origin}${historyRouterMap[curPath]}${search}`;
}
```
### 设置http授权头部

```
// 配置http头部授权头部
Vue.http.headers.common['Authorization'] = token;
Vue.http.options.emulateJSON = true;
```
### main.js app.vue
都是刷新才执行
### 对制定路由做缓存
```
<keep-alive include="Index,ScIndex,meIndex,Agreement,AnswerList,courseIndex">
		<router-view></router-view>
</keep-alive>
```
### 使用Webpack的代码拆分在Vue中进行延迟加载
应用懒加载的路由文件

```
const AnsIndex = () => import(/* webpackChunkName: "part4 */ '../pages/user/index.vue'); 		// 老师首页
const AnsList = () => import(/* webpackChunkName: "part5" */ '../pages/user/ans.vue'); 	// 老师首页回答了问题列表
```
未做懒加载的路由文件

```
import Index from './pages/index.vue';
import Pay from './pages/pay.vue';
```
配置 chunkFilename

使用 import 函数对 Components、Router、Vuex modules 进行延迟加载后，在 webpack.config.js 中配置 chunkFilename！
https://juejin.im/entry/5a9e5a0d5188255584538023
路由使用
https://juejin.im/entry/597ab13d5188253e0a62efcb
### 设置http头部授权
```
Vue.http.headers.common['Authorization'] = token;
Vue.http.options.emulateJSON = true;
```
### $nextTick
延迟执行回调到下一次 DOM 更新循环。在修改数据后调用它，等待 DOM 更新

```
 this.$nextTick(
            function() {
                setTimeout(() => {
                    this.$emit('close-tip')
                }, this.time)
            }
        )
```
### vue2里面的vuex
为什么vuex的actions传的第二个参数是undefined？
只能传一个参数，可用对象去代替

```
// 显示出提示框
		showLayer(state,text){
			state.layer.isShow = true;
			state.layer.text = text;
		},
```
### 微信支付
https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=7_7
### 执行的先后顺序

```
// 获得问题的内容
            async getAskAgainData(){
                this.showLoading();
               await  myAjax.get(apiPath.getAskAgain,{
                        ask_id:this.ask_id
                    }).then( res => {
                        this.askAgainData = res;
                } ).catch( e => {});
				this.hideLoading();
            },
```
```
created: function() {
				this.getAskAgainData().then( () => {
					this.getEvaluate();
					this.getRecommend();
					this.getUserAskReward();
					this.share();
				});
		},
```

```
//同样等于
async created: function() {
				await this.getAskAgainData();
				
					this.getEvaluate();
					this.getRecommend();
					this.getUserAskReward();
					this.share();
		},
```
### 行内样式
```
 :style="avatarWidth"
 avatarWidth:{
                type:Object,
                default: function(){
                    return {
                        width:'80px',
                        height:'80px'
                    }
                }
            },
```
### 条件判断的三级联动
```
{{ recordStatus == 1 ? '按住 说话' : recordStatus == 2 ? '松手 结束' : '松开手指,取消发送'}}
```

```
res.serverId && that.uploadMedia(res.serverId);
```
### 对于InfiniteLoading使用
```
this.$nextTick(function(){
    that.$refs["infiniteLoading"].$emit("$InfiniteLoading:reset");
});

if( res.list.length ){
		this.list = this.list.concat(res.list);
		this.page++;
		this.$refs["infiniteLoading"].$emit('$InfiniteLoading:loaded');
		}else{
		this.$refs["infiniteLoading"].$emit("$InfiniteLoading:complete");
}
```
### tab切换DOM获取
```
 <ul class="coupon-nav">
    <li class="nav-item active" @click="toggle($event,1)">未使用({{ coupons_total}})</li>
    <li class="nav-item" @click="toggle($event,2)">已过期({{ expired_total }})</li>
</ul>

toggle:function(event,type){
                var targetEle = event.target;
                [].forEach.call(targetEle.parentElement.children,
                    function(v,idx){
                        v.classList.remove("active")
                    }
                );
                targetEle.classList.add("active");
                this.isExpired = ( type == 1 ?  false : true  );
                // 分页切换
                this.list = [];
                this.page = 1;
                this.api = ( type == 1 ? apiPath.couponUseful : apiPath.couponExpired );
                var that = this;
                this.$nextTick(function(){
                    that.$refs["infiniteLoading"].$emit("$InfiniteLoading:reset");
                });
            },
```


