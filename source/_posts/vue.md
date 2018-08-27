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
const AnsIndex = () => import(/* webpackChunkName: "part4 */ '../p/user/index.vue'); 
const AnsList = () => import(/* webpackChunkName: "part5" */ '../p/user/ans.vue'); 
```
未做懒加载的路由文件

```
import Index from './p/index.vue';
import Pay from './p/pay.vue';
```
配置 chunkFilename

使用 import 函数对 Components、Router、Vuex modules 进行延迟加载后，在 webpack.config.js 中配置 chunkFilename！
https://juejin.im/entry/5a9e5a0d5188255584538023
路由使用
https://juejin.im/entry/597ab13d5188253e0a62efcb

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
            async getData(){
                this.showLoading();
               await  myAjax.get(apiPath.getAskAgain,{
                        id:this.id
                    }).then( res => {
                        this.askData = res;
                } ).catch( e => {});
				this.hideLoading();
            },
```
```
created: function() {
				this.getData().then( () => {
				   ...
					this.share();
				});
		},
```

```
//同样等于
async created: function() {
				await this.getAskAgainData();
			      ...
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
{{ Status == 1 ? '按住 说话' : Status == 2 ? '松手 结束' : '松开手指,取消发送'}}
```

```
res.Id && that.upload(res.Id);
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
 <ul class="c-nav">
    <li class="nav-item active" @click="toggle($event,1)">未使用({{ c_total}})</li>
    <li class="nav-item" @click="toggle($event,2)">已过期({{ e_total }})</li>
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
                this.api = ( type == 1 ? apiPath.cUseful : apiPath.cExpired );
                var that = this;
                this.$nextTick(function(){
                    that.$refs["infiniteLoading"].$emit("$InfiniteLoading:reset");
                });
            },
```
### 在组件里监听路由变化注意
```
activated(){
			this.$store.commit('changeCurrent',1);
			this.share();
		},
beforeRouteEnter (to, from, next) {
			// 在渲染该组件的对应路由被 confirm 前调用
			// 不！能！获取组件实例 `this`
			// 因为当守卫执行前，组件实例还没被创建
			next( vm => {
				// 通过 `vm` 访问组件实例
				document.querySelector('.content-container').scrollTop = vm.$store.state.indexScrollTop;
			});
		},
beforeRouteLeave (to, from, next) {
			// 导航离开该组件的对应路由时调用
			// 可以访问组件实例 `this`
			this.$store.commit('setIndexScrollTop',document.querySelector('.content-container').scrollTop);
			next();
		}
```
### 切换tab从新载入下拉刷新

```
tabChange:function(event,type){
                this.type = type;
                var target = event.currentTarget;
                var lis = document.querySelectorAll(".group li");
                [].forEach.call(lis,function(val){
                    val.classList.remove("active");
                });
                target.classList.add("active");
                var that = this;
                this.$nextTick(function(){
                    that.$refs["infiniteLoading"].$emit('$InfiniteLoading:reset');
                });
            },
```
### better-scroll

```
_innerScroll(){
                this.liHeight = this.$refs.li.offsetHeight;
                this.timeScroll = new BScroll(this.$refs.wrap, {
                    scrollY:true,
                    bounce:true,
                    starY:0,
                    snap: {
                        loop: false,
                        threshold: 0.1,
                        stepY: this.liHeight
                    },
                })
            },
```
### vue input @input 双向绑定
```
 @input="changeHandle($event)"
 changeHandle:function(event){
                let currentELe = event.currentTarget;
                let money = currentELe.value;
                this.reMoney = money;
            },
```
### 多tab数据切换接口处理
```
async requestData(){
                let data = {
                    ask_type:this.ask_type,
					page:this.page
                };
                await myAjax.get(apiPath.asks,data)
                    .then(res => {
                        if( res.list && res.list.length ){
                            this.page++;
                            // 暂不格式化时间
                            this.$refs["infiniteLoading"].$emit('$InfiniteLoading:loaded');
                        }else{
                            this.$nextTick(function(){
                                this.$refs["infiniteLoading"].$emit("$InfiniteLoading:complete");
                            });
                        }
                        })
                    .catch( e => {  } );
            },
            
//点击tab触发事件

			goto(type){
				this.askList = [];
				this.ask_type = type;
				this.page = 1;
				this.$nextTick( () => {
					this.$refs["infiniteLoading"].$emit('$InfiniteLoading:reset');
				})
			},
			
			
//另一种
// 切换导航tab
            navChange:function( event , type ){
                this.type = type || 1;
                var targetLi = event.currentTarget;
                var activeLi = document.querySelector(".nav-tab li.active");
                if( targetLi != activeLi ){
                    this.$nextTick(() => {
						this.$refs["infiniteLoading"].$emit('$InfiniteLoading:reset');
					})
                }
            },
```
### better-scroll 普通滚动
```
<div class="Mark" ref="wrap">
.Mark{
        width: 100%;
        height: 100%;
        position: fixed;
        top: 0;
        left: 0;
        background: #fff;
        z-index: 10000;
    }
    _innerScroll(){
                this.countryScroll = new BScroll(this.$refs.wrap, {
                    scrollY:true,
                    click: true,
                    starY:0
                })
            },
    mounted(){
			this._innerScroll();
	}
```
### ref触发点击事件
```
this.$refs["photos"].click();
```



