title: 基于puppeteer的内容爬虫
date: 2019-07-29
tag:
 - js
 - puppeteer

photos:
 - /img/2017/2.jpg 

---



### Puppeteer 介绍
Puppeteer  翻译是操纵木偶的人，利用这个工具，我们能做一个操纵页面的人。Puppeteer是一个Nodejs的库，支持调用Chrome的API来操纵Web，相比较Selenium或是PhantomJs,它最大的特点就是它的操作Dom可以完全在内存中进行模拟既在V8引擎中处理而不打开浏览器，而且关键是这个是Chrome团队在维护，会拥有更好的兼容性和前景。

SPA（单页面web应用）在页面渲染上面用的都是js异步渲染，与传统服务端渲染直接吐出完整的html内容不同。所以一些旧的爬虫工具已经不太适合使用在SPA项目上面了。
而puppeteer因为是一个chromium内核的浏览器工具，固然能抓取到所有能在页面正常渲染的内容，无论是html还是基于js的异步加载。
puppeteer本身支持单个浏览器对象的多开页面，所以在共享登录态这类场景下毫无压力。视硬件配置而定，甚至能多开浏览器实例，做并发爬虫。
通俗点说的就是，我们可以用Puppeteer这个nodejs库来操作一个浏览器（chrome）。

### Puppeteer 用处

1、利用网页生成PDF、图片
2、爬取SPA应用，并生成预渲染内容（即“SSR” 服务端渲染）
3、可以从网站抓取内容
4、自动化表单提交、UI测试、键盘输入等
5、帮你创建一个最新的自动化测试环境（chrome），可以直接在此运行测试用例
6、捕获站点的时间线，以便追踪你的网站，帮助分析网站性能问题
### Puppeteer 使用
安装以及初始化项目

```
mkdir thal
cd thal
npm init
cnpm i --save puppeteer   //用淘宝镜像源好点，我用npm经常安全不成功
```

### 爬取什么值得买12小时热门白菜价商品
爬取白菜价商品数据

```
const fs = require('fs');
const puppeteer = require('puppeteer');
const Sequelize = require('sequelize');
const config = require('./config');

var sequelize = new Sequelize(config.database, config.username, config.password, {
  host: config.host,
  dialect: 'mysql',
  pool: {
      max: 5,
      min: 0,
      idle: 30000
  }
});

var Baicai = sequelize.define('baicai', {
  id: {
    type: Sequelize.STRING(50),
    primaryKey: true
  },
  title: Sequelize.STRING(100),
  price: Sequelize.STRING(100),
  milemeter: Sequelize.STRING(100)
}, {
      timestamps: false
  });

(async () => {
  const browser = await (puppeteer.launch({ headless: true }));
  let page = await browser.newPage();

  // 进入页面
  await page.goto('https://faxian.smzdm.com/9kuai9/');

  // 获取页面标题
  let title = await page.title();
  console.log(title);
  // const CAR_LIST = '.subcate-tab-list';
  // const clickTag = await page.evaluate((sel,page) => {
  //   const catBoxs = Array.from($(sel).find('li'));
  //   const ctn = catBoxs.map(v => {
  //     const title = $(v).find('a').text();
  //     if(title == '12h最热'){
  //       page.click($(v));
  //     }
  //   });
    
  // }, CAR_LIST);   //获取不到12h最热的点击
  
  // 获取商品分类
  const BRANDS_INFO_SELECTOR = '.filter-items.J_filter_items';
  const brands = await page.evaluate(sel => {
    const ulList = Array.from($(sel).find('div a'));
    const ctn = ulList.map(v => {
      return v.innerText.replace(/\s/g, '');
    });
    return ctn;
  }, BRANDS_INFO_SELECTOR);
  console.log('分类: ', JSON.stringify(brands));
  let writerStream = fs.createWriteStream('fenlei.json');
  writerStream.write(JSON.stringify(brands, undefined, 2), 'UTF8');
  writerStream.end();
  // await bodyHandle.dispose();
  // 获取商品列表
  const CAR_LIST_SELECTOR = '.feed-list-col.z-clearfix';
  const carList = await page.evaluate((sel) => {
    const catBoxs = Array.from($(sel).find('li'));
    const ctn = catBoxs.map(v => {
      const title = $(v).find('.feed-ver-title a').text();
      const price = $(v).find('a.tag-bottom-right').text();
      const subTitle = $(v).find('.z-highlight.z-ellipsis').text();
      return {
        title: title,
        price: price,
        milemeter: subTitle
      };
    });
    return ctn;
  }, CAR_LIST_SELECTOR);

  console.log(`总共${carList.length}条商品数据: `, JSON.stringify(carList, undefined, 2));
  // (async () => {
  //   for(let i = 0; i < carList.length; i ++){
  //     //const item = carList[i];
  //     let tmpObj = {
  //       id: 'd-' + Date.now(),
  //       title: carList[i].title,
  //       price: carList[i].price,
  //       milemeter: carList[i].milemeter
  //     }
      
  //         var baicai = await Baicai.create(tmpObj);
  //         console.log('created: ' + JSON.stringify(baicai));
      
  //   }
  // })();
  // 将商品信息写入文件
  writerStream = fs.createWriteStream('baicai.json');
  writerStream.write(JSON.stringify(carList, undefined, 2), 'UTF8');
  writerStream.end();

  browser.close();
  // (async () => {
  //     var pets = await Baicai.findAll({
  //         where: {
  //             price: '京东'
  //         }
  //     });
  //     console.log(`find ${pets.length} pets:`);
  //     for (let p of pets) {
  //         console.log(JSON.stringify(p));
  //     }
  // })();
  // (async () => {
  //   var p = await Baicai.findAll({
  //       where: {
  //           id: 'd-1564362846693'
  //       }
  //   });
  //     p.title = "那只猪看到了";
  //     p.price = "淘阿宝";
  //     p.milemeter = "一千万";
  //     await p.save();
  // })();
  // Baicai.update({ title: "那只猪看到了",price: "淘阿宝", milemeter: "一千万"}, {
  //   where: {
  //     id: 'd-1564362846693'
  //   }
  // }).then(() => {
  //   console.log("Done");
  // });
  // Baicai.destroy({
  //   where: {
  //     id: 'd-1564362846525'
  //   }
  // }).then(() => {
  //   console.log("删除成功");
  // });
  // (async () => {
  //   var p = await Baicai.findAll({
  //       where: {
  //           id: 'd-1564362846525'
  //       }
  //   });
  //     await p.destroy();
  // })();
})();

```



参考：https://juejin.im/post/5a506682f265da3e474435d9


