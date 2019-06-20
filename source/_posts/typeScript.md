title: Typescript学习
date: 2018-11-28
tag:
 - js
 - typescript

photos:
 - /img/2017/2.jpg 

---



<!--more-->

### Typescript 介绍

1. TypeScript 是由微软开发的一款开源的编程语言。
2. TypeScript 是 Javascript 的超集，遵循最新的 ES6、Es5 规范。TypeScript 扩展了 JavaScript 的语法。
3. TypeScript 可编译成可读的、标准的 JavaScript 
4. TypeScript 更像后端 java、C#这样的面向对象语言可以让 js 开发大型的企业项目。

![GitHub set up](https://shuangmuyingzi.github.io/img/typescript1.png)

### Typescript 安装 编译
有两种主要的方式来获取TypeScript工具：
1. 通过npm（Node.js包管理器）
2. 安装Visual Studio的TypeScript插件，Visual Studio 2017和Visual Studio 2015 Update 3默认包含了TypeScript。

```
npm install -g typescript
tsc hello.ts
```
Typescript 开发工具 Vscode 自动 编译.ts 文件

1. 执行 tsc --init 生成配置文件 tsconfig.json ,可修改编译文件的输出目录，outDir

    ![GitHub set up](https://shuangmuyingzi.github.io/img/typescript2.png)
    
2. 点击菜单 任务-运行任务 点击 tsc:监视-tsconfig.json 然后就可以自动生成代码 了

    ![GitHub set up](https://shuangmuyingzi.github.io/img/typescript3.jpg)

### Typescript中的数据类型
typescript中为了使编写的代码更规范，更有利于维护，增加了类型校验，在typescript中主要给我们提供了以下数据类型

布尔类型（boolean）
数字类型（number）
字符串类型(string)
数组类型（array）
元组类型（tuple）
枚举类型（enum）
任意类型（any）
null 和 undefined
void类型
never类型

```
typescript中为了使编写的代码更规范，更有利于维护，增加了类型校验

写ts代码必须指定类型

    var flag:boolean=true;

    // flag=123;  //错误

    flag=false;  //正确

    console.log(flag);

```

未完待续：关于typescript更多的语法的使用可参考：
https://www.tslang.cn/docs/handbook/basic-types.html

