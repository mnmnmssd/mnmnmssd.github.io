---
title: node学习笔记day01
date: 2020-07-17 16:41:54
slug: nodeJS学习01
categories:
    - 学习
    - Spring
tags:
    - node
    - 学习笔记
---
# 1. node学习笔记day01
# 2. node简介
是一个运行js的环境 , 不是一门新的语言 , 不是js的框架 , 而是独立于浏览器来运行js的环境或js编译器 , 采用谷歌的V8引擎 , node可以用来写后端及操作文件或数据库 , 使js更强
node中可以输出一下`window`/`document`对象 , 会发现没有定义 , 这是因为这两个对象都是浏览器环境下的 , 而node作为一个独立环境是不存在这两个对象的定义的
```javascript
console.log(window);
//node报错：ReferenceError: window is not defined
```
# 3. node安装
百度node官网 , 下载安装包 , 安装完成自动配置环境变量 , 控制台输入`node -v`来检测node是否安装成功及查看node版本
# 4. node运行js文件
可在命令行使用`node`命令之后 , 输入js语句 , 回车运行 , 也可输入`node XX.js`来运行js文件
# 5. node运行示例
```javascript
class user {
    constructor(){
        this.name = "小明";
        this.pwa = "123456";
    }
    toString() {
        return this.name+"  "+this.pwa;
    }
}

var u1 = new user();

console.log(u1.toString());
```
保存为文件后 , 使用`node xx.js`运行后结果
```
PS E:\LearnnodeJS\day01> node .\test.js
小明  123456
```
# 6. node模块化
可跨文件使用变量或对象等 , 每个文件都可以看作一个模块
使用`commonJS`规范 , 包含三个关键字：`require`、`exports`、`module`
js导出某对象：`exports.name = name`  (xx.js)
js导入某对象：`var name = require('/xx.js')` (test.js)
模块化示例：
```JavaScript
//xx.js 导出模块
var user = {
    name:"zs",
    sex:"man",
    age:12
}

exports.user = user;
//或使用
module.exports.user = user;
```
```javascript
//test.js  导入xx模块
var u2 = require('./xx.js');
console.log(u2.user.name);
```
输出结果
```
PS E:\LearnnodeJS\day01> node .\test.js
zs
```
`exports` 本质上是指向了 `module.exports` , 所以两者使用没有区别 , 但要注意如果不小心重写了`exports` , 如：`exports = {user:"zs"}` 此时`exports`不在指向`module.exports` , 而是被重写为一个对象或属性 , 此时将没有了导出功能 , 所以以下方法：
```javascript
exports = {user:"zs"} //错误的 , 将改变指向 , 不具备导出功能
//运行后输出 :  {}
module.exports = {user:"zs"}  //正确的 , 将导出一个对象
//运行后输出 : { user: 'zs' }
```
其两种的运行结果不同 , 所以应尽量使用`module.exports`来进行模块的导出
模块导入后会在内存中产生`缓存` , 下次导入时若`缓存`中存在 , 将直接获取 , 若想重新导入 , 就要清除缓存
```javascript
var a = require('./xx');
var b = require('./xx');
console.log(a == b);
//将输出 true
```
这是因为模块导入形成缓存 , 在第二次导入时 , 将`直接`在缓存中拿取 , 不会`产生`新对象
# 7. node的require加载机制
`require`中关于路径：
若为`require('/home/xx.js')` , 将前往磁盘根目录下层级寻找 //绝对
若为`require('./xx.js')` , 将在主模块文件下寻找   //相对
若为`require('xx')` , 将视为加载`第三方`模块 , 其加载顺序为 : 

- 首先查看当前目录下的`node_modules`文件夹 , 若当前目录下没有，则去父级目录查找，一直找到磁盘根目录
- 在`node_modules`中查找第三方模块名称文件夹 , 若找到，则进入该文件夹，查看文件间内的`package.json`文件，查找其`main`属性内容，并将其内容作为第三方模块的主入口
- 若没有`main`属性，则默认加载第三方模块文件夹内的`index.js`文件

# 8. node对于文件的操作
导入文件操作模块`fs` , 其中文件操作有同步与异步两种方式，node默认异步操作
## 8.1. 对文件的读取操作
`readFile`/`readFileSync`方法，异步与同步
### 8.1.1. 同步
```javascript
//ts.txt 内容：hello
var f1 = fs.readFileSync('ts.txt',{flag:'r',encoding:'utf-8'});
console.log(f1)
//输出：hello
```
`readFileSync(path,[options])`
参数列表：
- path ( 所读取文件目标路径 ) : <String | number | Buffer>
- options ( 文件属性,可选参数 ) : <Object | String>
  - flag ( 对文件的操作 ) : <String> 默认值 'r'
  - encoding ( 文件编码 ) : <String> 默认值 'null'

### 8.1.2. 异步
```javascript
fs.readFile('ts.txt',{flag:'r',encoding:'utf-8'},(err,data)=>{
    if (err) throw err; //出现异常抛出
    console.log(data); //成功打印内容：hello
})
```
`readFile(path,[options],callback)`
参数列表：
- path ( 所读取文件目标路径 ) : <String | number | Buffer>
- options ( 文件属性,可选参数 ) : <Object | String>
  - flag ( 对文件的操作 ) : <String> 默认值 'r' 
  - encoding ( 文件编码 ) : <String> 默认值 'null'
- callback ( 回调函数 ) : <Funtion> 
  - err : 出错
  - data : 内容

### 8.1.3. 封装
```javascript
function f2(path) {
    return new Promise((res,rect)=>{
        fs.readFile(path,{flag:'r',encoding:'utf-8'},(err,data)=>{
            if (err) rect(err)
            res(data)
        })
    }) 
}

f2('ts.txt').then((res)=>{
    console.log(res)
})
```
## 8.2. 对文件的写入操作
`writeFile`/`writeFileSync`方法，异步与同步
写入文件若不存在，将自动创建文件
### 8.2.1. 同步
```javascript
fs.writeFileSync('ts.txt','world',{flag:'a+',encoding:'utf-8'});
```
`writeFileSync(path,data,[options])`
参数列表：
- path ( 所读取文件目标路径 ) : <String | number | Buffer | URL>
- data ( 写入的数据 ) : <String | ...>
- options ( 文件属性,可选参数 ) : <Object | String>
  - flag ( 对文件的操作 ) : <String> 默认值 'w'; 'w'为覆盖写入; 'a+'为追加写入 
  - encoding ( 文件编码 ) : <String> 默认值 'null'

### 8.2.2. 异步
```javascript
fs.writeFile('ts.txt','World--Sync',{flag:'a+',encoding:'utf-8'},(err)=>{
    if (err) console.log(err)
    console.log("done");
})
```
`writeFile(path,data,[options],callback)`
参数列表：
- path ( 所读取文件目标路径 ) : <String | number | Buffer | URL>
- data ( 写入的数据 ) : <String | ...>
- options ( 文件属性,可选参数 ) : <Object | String>
  - flag ( 对文件的操作 ) : <String> 默认值 'w'; 'w'为覆盖写入; 'a+'为追加写入 
  - encoding ( 文件编码 ) : <String> 默认值 'null'
- callback ( 回调函数 ) : <Funtion>
  - err ( 出现错误 ) : <ERROR> 
  
### 8.2.3. 封装
```javascript
function f1(path,data,flag) {
    return new Promise((res,ract)=>{
        fs.writeFile(path,data,{flag:flag,encoding:'utf-8'},(err)=>{
            if (err) ract(err)
            res("写入完毕")
        })
    })
}

async function f() {
    await f1('test.html','<h1>ddddd</h1>','a+');
}
```
## 8.3. 其他操作
`unlink` : 删除文件操作
`mkdir` : 创建目录
`readdir` : 读取目录
`rmdir` : 删除目录
...等等 [中文文档](http://nodejs.cn/api/)
# 9. node 输入输出
## 9.1. 输入
`readLine`
```javascript
var readline = require('readline')

var r1 = readline.createInterface({
    input:process.stdin,
    output:process.stdout
})

r1.question('整点啥',(answer)=>{
    console.log(answer)
    r1.close();
})

r1.on('close',()=>{
    process.exit(0);
})
```
`createInterface` : 创建输入输出对象，调用了终端进程的输入输出
`input:process.stdin` : 创建输入
`output:process.stdout` : 创建输出
`question` : 提问方法，使用完毕后应关闭输入流，否则程序不会终止
`close` : 关闭输入流
`on` : 监听事件，并作出响应
`process.exit(0)` : 退出终端进程

## 9.2. 封装
```javascript
function read(title) {
    return new Promise((res)=>{
        r1.question(title,(answer)=>{
            res(answer)
        })
    })
}
```

# 10. 常用npm命令
`node -v`  查看node版本
`npm init` 自动在目录下生成`package.json`文件，加入`-y`可跳过问答
`npm -v`   查看npm版本
`npm list` 查看当前目录下安装的包，加入`-g`查看全局安装的包
`npm config list` 查看配置信息
`npm install 包名` 自动安装包及其所需依赖 
`npm install 包名 --save -dev`  安装包的开发环境所需依赖
`npm install 包名 --save`   安装包的生产环境所需依赖
`npm uninstall 包名`  卸载某个包

> 2020/07/17
> by ---- 笑的心酸