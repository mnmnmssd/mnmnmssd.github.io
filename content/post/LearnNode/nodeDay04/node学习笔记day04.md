---
title: node学习笔记day04
date: 2020-08-04 17:50:45
slug: nodeJS学习04
categories:
    - 学习
    - nodeJS
tags:
    - node
    - 学习笔记
---
# 1. node学习笔记day04
## 1.1. cheerio模块
>cheerio是jquery核心功能的一个快速灵活而又简洁的实现，主要是为了用在服务器端需要对DOM进行操作的地方

### 1.1.1. 安装
`npm install cheerio`

### 1.1.2. 使用与示例
示例操作
```html
<body>
    <h1>hello world</h1>
    <a href="www.baidu.com" />
    <div class="test">test</div>
</body>
```
使用 `load` 加载DOM，并返回一个 `JQuery` 对象
```javascript
let $ = cheerio.load(demo);
console.log($(".test").text());
//输出
// PS E:\LearnnodeJS\day04> node .\demo1.js
// test
console.log($("a").attr("href"));
//输出
// PS E:\LearnnodeJS\day04> node .\demo1.js
// www.baidu.com
```
和 `JQuery` 操作基本相同
## 1.2. axios 流模式返回
通过设置 `responseType` 属性为 `stream` ，生成流，可以来写入文件
### 1.2.1. 使用和示例
```javascript
axios
    .get("https://xinsuan.xyz/", { responseType: "stream" })
    .then((res) => {
        let ws = fs.createWriteStream("./test.html");

        res.data.pipe(ws);

        res.data.on("close", () => {
            ws.close();
        });
    })
    .catch((err) => {
        console.error(err);
    });
```
运行后写入 `test.html` 文件，如下
![20200803162838](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/20200803162838.png)

使用流模式可以实现下载文件等操作

# 2. puppeteer 无界面谷歌浏览器
>Puppeteer 是一个 Node 库，它提供了一个高级 API 来通过 DevTools 协议控制 Chromium 或 Chrome。Puppeteer 默认以 headless 模式运行，但是可以通过修改配置文件运行“有头”模式。

具有以下功能:

- 生成页面 PDF。
- 抓取 SPA（单页应用）并生成预渲染内容（即“SSR”（服务器端渲染））。
- 自动提交表单，进行 UI 测试，键盘输入等。
- 创建一个时时更新的自动化测试环境。 使用最新的 JavaScript 和浏览器功能直接在最新版本的Chrome中执行测试。
- 捕获网站的 timeline trace，用来帮助分析性能问题。
- 测试浏览器扩展

## 2.1. 示例与使用
```javascript
const puppeteer = require("puppeteer");

async function test() {
    //生成浏览器对象
    // headless 设置为 false 为有界面打开，默认无界面
    let browser = await puppeteer.launch({ headless: false });

    //生成页面对象
    let page = await browser.newPage();
    //打开页面
    await page.goto("https://www.xinsuan.xyz");
    //页面内DOM操作
    page.$$eval("#one-words p", (elements) => {
        elements.forEach((element) => {
            //浏览器控制台输出
            console.log(element.innerHTML);
        });
    });
    //监听浏览器控制台事件
    page.on("console", (...args) => {
        //终端输出
        console.log(args);
    });
}

test();
```
对DOM进行操作，聚焦或者点击
```javascript
const puppeteer = require("puppeteer");

async function test() {
    let brower = await puppeteer.launch({ headless: false });
    let page = await brower.newPage();
    await page.goto("http://www.baidu.com");
    let wd = await page.$("#form input[name = 'wd']");
    await wd.focus();
    
    await page.keyboard.type("嘻嘻嘻");

    let su = await page.$("#form #su");

    await su.click();
}

test();
```

> 2020年8月4日
> by --- 笑的心酸