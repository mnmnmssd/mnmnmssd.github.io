---
title: node实战项目--B站爬虫
date: 2020-07-23 19:13:05
slug: nodeJS学习03
categories:
    - 学习
    - nodeJS
tags:
    - node
    - 学习笔记
    - 项目
---
# 1. node学习笔记day03
`node` 结合 `os` 模块与 `axios` 模块实现爬虫实战
# 2. 目标
爬取B站用户追番信息，并保存为`json`文件形式
## 2.1. 目标API
```url
https://api.bilibili.com/x/space/bangumi/follow/list?type=1&follow_status=0&pn=?&ps=?&vmid=?&ts=?
```
## 2.2. url各参数解析
**简要描述：** 

- 获取指定用户订阅番剧信息

**请求URL：** 
- ` https://api.bilibili.com/x/space/bangumi/follow/list `
  

**请求方式：**
- GET 

**参数：** 

| 参数名        | 必选 | 类型 | 说明               |
| ------------- | ---- | ---- | ------------------ |
| type          | 是   | int  | 固定值 1           |
| follow_status | 是   | int  | 固定值 0           |
| pn            | 是   | int  | 请求分页           |
| ps            | 是   | int  | 请求数据条数 , <50 |
| vmid          | 是   | int  | 用户Uid            |
| ts            | 否   | int  | 时间戳             |

 **返回示例**
``` json
{
    "code": 0,
    "message": "0",
    "ttl": 1,
    "data": {
        "list": [
            {    
                ...
            }
        ],
        "pn": 1,
        "ps": 1,
        "total": 20
    }
}

```
 **返回参数说明** 

| 参数名  | 类型   | 说明         |
| ------- | ------ | ------------ |
| code    | int    | 返回代码     |
| message | String | 信息         |
| data    | json   | 番剧数据     |
| list    | array  | 详细信息     |
| pn      | int    | 分页         |
| ps      | int    | 返回数据条数 |
| total   | int    | 总条数       |

# 3. axios 模块
## 3.1. 安装
安装`axios`模块，此模块基于`Promise`异步请求。简单方便
`npm install axios`
## 3.2. 导入使用
```javascript
const axios = require("axios");
```
# 4. 分析数据
```javascript
 //获取番剧条数
    let total = parseInt(res.data.data.total);
    //获取分页数
    page = Math.ceil(total / params.ps);
    console.log("page" + page);
    //迭代写入
    res.data.data.list.forEach((el) => {
    let postDetil = `"${el.season_id}":{
            "title":" ${el.title}",
            "count": "${el.total_count}",
            "season": "${el.season_title}",
            "evaluate": "${el.evaluate.replace(/\n/g, " ")}",
            "url": "${el.url}",
            "data_show": "${el.publish.release_date_show}"
            }
            `;
        bangmi[uid].push(postDetil);
    });
```
# 5. 保存数据
```javascript
let status = await f1(
        "./dist/" + uid + "/" + uid + ".json",
        "{\n" + bangmi[uid].toString() + "\n}",
        "a+"
    );
```
# 6. 运行后截图

- 提示用户输入要爬取的id
![20200723204539](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/20200723204539.png)

- 返回json数据保存在本地
![20200723204347](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/20200723204347.png)

# 7. 全部代码
```javascript
const axios = require("axios");
const rs = require("readline");
const fs = require("fs");

var vmid;
var url = "https://api.bilibili.com/x/space/bangumi/follow/list?";
var params = {
    type: 1,
    follow_status: 0,
    pn: 0,
    ps: 15,
    vmid: vmid,
};
var bangmi = {};

//封装写入操作
function f1(path, data, flag) {
    return new Promise((res, ract) => {
        fs.writeFile(path, data, { flag: flag, encoding: "utf-8" }, (err) => {
            if (err) ract(err);
            res("写入完毕");
        });
    });
}
//声明用户输入操作
var r1 = rs.createInterface({
    input: process.stdin,
    output: process.stdout,
});

//封装读取用户输入操作
function read(title) {
    return new Promise((res) => {
        r1.question(title, (answer) => {
            res(answer);
        });
    });
}

//声明初始化方法
async function init() {
    let uid = await read("查询的用户UID:");
    params.vmid = uid;
    bangmi[uid] = [];

    //判断用户文件夹是否已存在
    if (!fs.existsSync("./dist/" + uid)) {
        fs.mkdir("./dist/" + uid, (err) => {
            if (err) console.log(err);
        });
    }
    //定义循环控制次数与分页
    var i = 1;
    var page;

    do {
        await axios
            .get(
                url +
                    "type=" +
                    params.type +
                    "&follow_status=" +
                    params.follow_status +
                    "&pn=" +
                    params.pn +
                    i +
                    "&ps=" +
                    params.ps +
                    "&vmid=" +
                    params.vmid
            )
            .then((res) => {
                //获取番剧条数
                let total = parseInt(res.data.data.total);
                //获取分页数
                page = Math.ceil(total / params.ps);
                console.log("page" + page);
                //迭代写入
                res.data.data.list.forEach((el) => {
                    let postDetil = `"${el.season_id}":{
                            "title":" ${el.title}",
                            "count": "${el.total_count}",
                            "season": "${el.season_title}",
                            "evaluate": "${el.evaluate.replace(/\n/g, " ")}",
                            "url": "${el.url}",
                            "data_show": "${el.publish.release_date_show}"
                        }
                        `;
                    bangmi[uid].push(postDetil);
                });
                i++;
            });
    } while (i <= page);

    //写入json文件
    let status = await f1(
        "./dist/" + uid + "/" + uid + ".json",
        "{\n" + bangmi[uid].toString() + "\n}",
        "a+"
    );
    console.log(status);

    //关闭输入
    r1.close();
}

init();
```

> 2020/7/23
> by ---笑的心酸
