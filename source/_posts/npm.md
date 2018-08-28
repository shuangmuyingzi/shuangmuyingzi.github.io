title: NPM — JavaScript 的包管理器
date: 2017-11-23
tag:
 - js
 - 构建

photos:
 - /img/2017/1.jpg 

---

本文主要介绍npm的常用命令，以及如何发布一些常用的js模块化代码到npm上面方便日后的使用，和举例如何把一个vue组件打包发布到npm到最后下载到本地使用的过程。

<!--more-->

npm(Node Package Manager)是node的默认模块管理器，一个命令行下的软件，用来安装和管理node模块，同时也可以管理其他开放式的js模块代码。npm有一个好处是除了下载需要的模块外还会帮我们解决依赖关系，同时下载所依赖的模块。

## NPM ——  JavaScript 的包管理器

### npm help

``` 
    access, adduser, bin, bugs, c, cache, completion, config,
    ddp, dedupe, deprecate, dist-tag, docs, doctor, edit,
    explore, get, help, help-search, i, init, install,
    install-test, it, link, list, ln, login, logout, ls,
    outdated, owner, pack, ping, prefix, prune, publish, rb,
    rebuild, repo, restart, root, run, run-script, s, se,
    search, set, shrinkwrap, star, stars, start, stop, t, team,
    test, tst, un, uninstall, unpublish, unstar, up, update, v,
    version, view, whoami
```

npm help 可以查看所有可使用的命令。
### npm常用指令

``` 
npm install 名字      //该命令用于安装模块
npm uninstall 名字    //该命令用于卸载模块
npm run 名字          //该命令用于执行脚本
```

### 如何用npm发布自己的模块

我们所有下载以及发布的包是存放在这个地址：https://www.npmjs.com/
我们发布一个东西，得要有自己的标识吧，所以得先注册账号。
#### 1.注册用户
```
npm adduser
运行该命令后会需要你依次输入
Username:
Password:
Email:
```
#### 2.检查
接下来是需要检查一下有没有注册成功

```
npm whoami
```
#### 3.建立package

```
npm init
//一路回车
name: (dateFormat) datechange
version: (1.0.0) 
description: change date format
entry point: (index.js) 
test command: 
git repository: https://github.com/shuangmuyingzi/dateFormat.git
keywords: dateformat date datechange
author: lingzi
license: (ISC) 
About to write to /Users/linziying/Downloads/npm/dateFormat/package.json:

{
  "name": "datechange",
  "version": "1.0.0",
  "description": "change date format",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/shuangmuyingzi/dateFormat.git"
  },
  "keywords": [
    "dateformat",
    "date",
    "datechange"
  ],
  "author": "lingzi",
  "license": "ISC",
  "bugs": {
    "url": "https://github.com/shuangmuyingzi/dateFormat/issues"
  },
  "homepage": "https://github.com/shuangmuyingzi/dateFormat#readme"
}


Is this ok? (yes) 

```
然后会在该目录下多了个package.json文件

#### 添加简单的日期转换格式插件
每次后台数据接口返回的基本是时间戳，往往需要转换成常用的日期格式。那我就以一个简单的日期转换格式小插件为例。把下面代码放到package.json文件同级目录里。

``` 
date.js

(function(global) {
    var datechange = (function() {
      
      return function (date) {
  
        date = date || new Date;
  
        if(!(date instanceof Date)) {
          date = new Date(date);
        }
  
        if (isNaN(date)) {
          throw TypeError('Invalid date');
        }
        let re = date.getFullYear() + '.' + (date.getMonth()+1) + '.' + date.getDate();
        return re
  
      }
  })()

  if (typeof define === 'function' && define.amd) {
    define(function () {
      return datechange;
    });
  } else if (typeof exports === 'object') {
    module.exports = datechange;
  } else {
    global.datechange = datechange;
  }

})(this);
```


#### 4.发布
npm publish
记得在推之前先登录npm要不然会报错。如果是再次推送同一个项目记得修改版本号。 

### 使用

``` 
npm install --save-dev datechange
```
```
var datechange = require('datechange');
var now = new Date();
var timeStamp = datechange(now);
```
OR

```javascript
<script type="text/javascript" src='date.js'></script>
	<script type="text/javascript">
		var now = new Date();
		var timeStamp = datechange(1511350834583);
		alert(timeStamp)
	</script>
```
先安装后使用，包的名称为package.json里的name属性。
具体代码看github:
https://github.com/shuangmuyingzi/dateFormat

### Vue组件如何上传到NPM

#### 方式一

用webpack把项目打包成JS文件，然后在package.json的 main导出该JS文件。

##### 创建                   
* vue-cli创建vue简单项目 

    `vue init webpack-simple load-ling-zi`
* 修改package.json

    1. 修改`"private": false`
    
        npm默认创建的项目是私有的，如果要发布至npm必须将其公开
    2. 添加`"main": "dist/build.js"`
    
        通过`import loading from 'load-ling-zi'`引用该组件时，项目会自动找到`node_modules/load-ling-zi/dist/build.js`
*  在src加入组件代码App.vue， 并创建我们的导出文件index.js。
   在index.js中添加：
 
    ```
    import load from './App.vue'
    export default load
    //global 情况下 自动安装
    if (typeof window !== 'undefined' && window.Vue) {
      window.Vue.component('load', load);
    }
    ```
       
*  因为最后我们是打包成一个js文件，所以需要修改一下配置文件`webpack.config.js`

    因为不是所有使用你组件的人都是通过npm按住和impor的很多人是通过`<script>`直接引入的,我们要将libraryTarget改为umd,以及修改入口文件与设置导出文件目录以及名称。   
    
            entry: './src/index.js',
            output: {
            path: path.resolve(__dirname, './dist'),
            publicPath: '/dist/',
            filename: 'build.js',
            library: 'load',
            libraryTarget: 'umd',
            umdNamedDefine: true
            },
       
*  最后需要把`.gitignore`文件里面的`/dist`删除掉要不然上传时会忽略掉dist打包的文件。

    具体代码已放到github：
    https://github.com/shuangmuyingzi/loadingModule/tree/master/load/load-ling-zi
    
##### 发布
`npm publish`，具体参考上面步骤。

##### 应用

1. Installation

``` bash
# install dependencies
npm install load-ling-zi -D
```

2. Usage

```
<loading v-if="loading.isShow">
    <span>{{ loading.text }}</span>
</loading>

<script>
    import loading from 'load-ling-zi'
    export default {
        components: {
            loading:loading
        },
        data () {
            return {
                loading:{
                    isShow:true,
                    text:"加载中"
                },   
            }
        }
    }
</script>
```

#### 方式二
在main里直接导出Vue组件（.vue文件）
具体代码看这里：
https://github.com/shuangmuyingzi/loadingModule/tree/master/loading

### 写在最后
关于vue组件、插件的实现方式估计还有很多的办法,本文权当抛砖引玉，水平有限，举的例子也是比较简单，一个好的组件也要考虑更多的可配置性以及通用性，数据的可配置，结构的可配置，样式的可配置等等，es里面模块化的写法也是很多，还有一些直接在`<script>`引入，所以要考虑如何导出代码能够满足更多场景的使用。

