---
title: node学习笔记day02
date: 2020-07-21 19:53:30
slug: nodeJS学习02
categories:
    - 学习
    - nodeJS
tags:
    - node
    - 学习笔记
---
# 1. node学习笔记day02
# 2. node文件流
流的出现是为了解决读取文件时，文件内容过于庞大而造成计算机内存不够导致的问题
其将庞大文件分为多个流，以流的形式经由内存写入磁盘
## 2.1. 读取流
`createReadStream`来自node的fs模块，此方法将创建一个输出流
## 2.2. 写入流
`createWriteStream`此方法将创建一个输入流
## 2.3. node管道流
`pipe` 创建管道，并将输出流中的数据通过管道插入到输入流中
# 3. node事件
```
Node.js 是单进程单线程应用程序，但是因为 V8 引擎提供的异步执行回调接口，通过这些接口可以处理大量的并发，所以性能非常高。

Node.js 几乎每一个 API 都是支持回调函数的。

Node.js 基本上所有的事件机制都是用设计模式中观察者模式实现。

Node.js 单线程类似进入一个while(true)的事件循环，直到没有事件观察者退出，每个异步事件都生成一个事件观察者，如果有事件发生就调用该回调函数.
```
## 3.1. 原生js写事件绑定与监听
```javascript
var userEvent = {
    
    eventName:{  //事件列表

    },
    on:function(ent,fn) {  //绑定事件
    
        if (this.eventName[ent]) {
            this.eventName[ent].push(fn)
        } else {
            this.eventName[ent] = [];
            this.eventName[ent].push(fn)
        }
        
    },
    emit:function(ent,msg) { //监听事件
        
        if (this.eventName[ent]) {
            this.eventName[ent].forEach(element => {
                element(msg)
            });
        }
    },
    delent:function(ent) {  //删除事件
        if (this.eventName[ent]) {
            delete this.eventName[ent]
        } else {
            console.log("err")
        }
    }
}
```
调用原生js事件
```javascript
//绑定一个名为success的事件，该事件接受一个参数并输出
userEvent.on('success',(msg)=>{ 
    console.log(msg)
})
//调用success事件，并传入参数
userEvent.emit('success','done')
//删除success事件
userEvent.delent('success')
```
## 3.2. node事件模块
```javascript
const events = require('events');

//创建一个事件对象
let evt = new events.EventEmitter();

//绑定click事件，该事件接受一个参数并输出
evt.on('click',(data)=>{
    console.log("wow"+data)
})

//监听click并调用，传入一个参数
evt.emit('click','good');
```
# 4. node路径模块
`path`
## 4.1. 路径片段组合
```javascript
const path = require('path')

let arr = ['/','ts','keke']
let ps = path.join(...arr)
console.log(ps)

//输出\ts\keke
```
# 5. node OS模块
```javascript
const os = require('os')

let osc = os.cpus();

console.log(osc)
```
# 6. node URL 模块
## 6.1. 解析url地址并返回对象
`parse`
```javascript
const url = require('url')

console.log(url);

let Bilibili = "https://www.bilibili.com/video/BV1i7411G7kW?p=9";

let urlObj = url.parse(Bilibili);

console.log(urlObj);
```
# 7. axios
基于`promise`的`HTTP`库，跨平台，轻量级，vue指定
## 7.1. 特性
- 从浏览器中创建 XMLHttpRequests
- 从 node.js 创建 http 请求
- 支持 Promise API
- 拦截请求和响应
- 转换请求数据和响应数据
- 取消请求
- 自动转换 JSON 数据
- 客户端支持防御 XSRF

## 7.2. 安装
`npm install axios`
## 7.3. 导入使用
示例：get请求
```javascript
axios.get(url,params)
.then(res => {
    console.log(res)
})
.catch(err => {
    console.error(err); 
})
```

# 8. request 模块
官网介绍`自2020年2月份以来，本模块已弃用`...
不过还是要了解了解
## 8.1. 安装
`npm install request`
## 8.2. 导入使用
示例：简单get请求
```javascript
req.get("https://www.baidu.com",(err,response,body)=>{
    if (err) console.log(err)
    console.log(response)
    console.log(body)
})
```
## 8.3. 封装
```javascript
function reqGet(url) {
    return new Promise((reslove, reject) => {
        req.get(url, (err, response, body) => {
            if (err) reject(err)
            reslove({response,body})
        })
    })
}
```
> 2020年7/21
> by 笑的心酸