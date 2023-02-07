---
title: 新版Windows Terminal美化配置
description: 已落后版本
date: 2020-06-09 10:14:58
slug: Windows Terminal美化配置
categories:
    - 美化
    - 教程
---
# 关于新版Windows Terminal
## 简介
Windows 终端是一个面向命令行工具和 shell（如命令提示符、PowerShell 和适用于 Linux 的 Windows 子系统 (WSL)）用户的新式终端应用程序。 它的主要功能包括多个选项卡、窗格、Unicode 和 UTF-8 字符支持、GPU 加速文本呈现引擎，还可以用于创建你自己的主题并自定义文本、颜色、背景和快捷键绑定。
![wt](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/wt.png)
## 下载与安装
下载途径有两种：
**`需要系统版本190X及以上`** 
可在[` 微软商店 `](https://aka.ms/terminal)内搜索下载
亦可在[` GitHub发布页 `](https://github.com/microsoft/terminal/releases)下载
**需要注意：**git处有两种版本
![版本](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/dl.jpg)
第一个为**预览版** ，第二个为**稳定版**，请自行选择下载
安装界面如图(因为我这里已经安装了所以显示为启动，正常应为安装):
![wt-install](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/wt-install.png)

# 美化准备工作
> * PowerShell7
> * 良好的网络环境
## PowerShell7安装
在[` PowerShell `](https://github.com/PowerShell/PowerShell)处选择对应系统下载
![ps7](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/![ps7](ps7.png).png)
其中` LTS `为长期更新版本, ` stable ` 稳定版本，` preview `预览版本
## 将Terminal添加到右键菜单
这里有两种方式修改:
* 通过修改注册表(不推荐)
* 使用git上的一键脚本配置

这里着重说明一下第二种方式(下列操作默认管理员身份运行)
点击[一键脚本](https://github.com/lextm/windowsterminal-shell)跳转页面
**注意**` 需要PowerShell版本为7 `
可下载` install.sp1 `文件然后在` PowerShell7 `中找到文件执行命令 ` .\install.ps1 `
亦可采用git页面给出的命令安装
```
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://raw.githubusercontent.com/lextm/windowsterminal-shell/master/install.ps1')) 
```
**注意：可能会报下列错误** 

`
.\install.ps1: File C:\Users\lenovo\Desktop\install.ps1 cannot be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at https://go.microsoft.com/fwlink/?LinkID=135170.
`

这是因为`PowerShell`默认执行权限的问题,此时只需打开`PowerShell`输入下列命令即可:
```
set-ExecutionPolicy RemoteSigned
```
## Windows Terminal配置文件更改
打开`Windows Terminal`，点击标题栏小三角，打开设置页面
目前官方仍未更新GUI页面，所以打开为一个JSON文件
![setting](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/setting.png)
### 将PowerShell7添加至Windows Terminal启动菜单
在配置文件中修改以下属性
属性名称:`list`
在其中添加:
``` json
{
    "guid": "{574e775e-4f2a-5b96-ac1e-a2962a402336}", //程序对应的guid属性唯一值
    "hidden": false,      //是否在启动列表隐藏
    "name": "pwsh7",    //启动列表名称
    "tabTitle": "pwsh7",    //选项卡标题名称
    "source": "Windows.Terminal.PowershellCore",    //这会存储源自配置文件的配置文件生成器的名称
    "startingDirectory": ".",   //加载 shell 时所处的起始目录 这一步一定要设为'.'，否则会影响后续操作
    "historySize": 9001,        //历史记录大小
    "commandline": "C:/Program Files/PowerShell/7/pwsh.exe -nologo", //配置文件中使用的可执行文件
    "snapOnInput": true //键入时滚动到输入行
}
```
其中`guid`生成可以点击[此处](http://www.ofmonkey.com/transfer/guid)
### 修改默认启动shell
此时我们修改默认的启动shell为新安装的`PowerShell7`

属性名称: `defaultProfile`
属性值: 对应shell的`guid`属性

将此值改为我们上方设置的`PowerShell7`的`guid`
### 修改字体，背景及配色方案等
在`profiles` 属性下的 `defaults`属性中添加/修改些默认参数:
```json
{
    "acrylicOpacity": 0.25, //设置背景模糊度
    "useAcrylic": true, //开启背景毛玻璃效果
    "colorScheme": "Monokai Night", //设置配色方案，需要搭配后面的配色方案使用
    "suppressApplicationTitle":true, //启动自定义选项卡标题
    "fontSize": 11, //字体大小
    "fontFace": "Fira Code", //设置字体
}
```
#### 字体修改：
选择喜欢的字体下载(最好是支持PowerLine的，否则后续会出现字符渲染的问题)
这里以` Fira Code `字体为例
点击此处[开始下载Fira Code](https://github.com/tonsky/FiraCode/releases/download/5/Fira_Code_v5.zip)
安装好字体后，在`defaults`属性中添加`"fontFace": "Fira Code"`值

#### 配色方案修改:
下方网址寻找喜欢的配色方案
[terminalsplash](https://terminalsplash.com/)
[atomcorp](https://atomcorp.github.io/themes/)
多种配色方案，此处以第一个网址为例，进入后等待加载完毕
选择喜欢的配色方案，点击` code `
![themes](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/themes.png)
复制弹出的代码
![themes-2](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/themes-2.png)
粘贴至` 配置文件 `的` schemes `属性中
如
```json
    "schemes": [{
        "name": "Monokai Night", //配色方案名称
        "background": "#1f1f1f",
        "foreground": "#f8f8f8",
        "black": "#1f1f1f",
        "blue": "#6699df",
        "cyan": "#e69f66",
        "green": "#a6e22e",
        "purple": "#ae81ff",
        "red": "#f92672",
        "white": "#f8f8f2",
        "yellow": "#e6db74",
        "brightBlack": "#75715e",
        "brightBlue": "#66d9ef",
        "brightCyan": "#e69f66",
        "brightGreen": "#a6e22e",
        "brightPurple": "#ae81ff",
        "brightRed": "#f92672",
        "brightWhite": "#f8f8f2",
        "brightYellow": "#e6db74"
    }]
```
修改默认配色方案` colorScheme `属性值为自定义的配色方案名称
#### 背景修改：
* 开启背景毛玻璃效果
```json
"acrylicOpacity": 0.25, //设置背景模糊度
"useAcrylic": true, //开启毛玻璃效果
```
* 设置背景图片
```json
"backgroundImage":"图片路径",
"backgroundImageStretchMode":"背景拉伸", //可选属性: "none"、"fill"、"uniform"、"uniformToFill"
"backgroundImageAlignment":"背景对齐方式",//可选属性: "center"、"left"、"top"、"right"、"bottom"、
                                                //"topLeft"、"topRight"、"bottomLeft"、"bottomRight"
"backgroundImageOpacity":"背景不透明度", //0-1
```
* 其他属性如滚动条，光标颜色等可参考`官方文档`[链接](https://docs.microsoft.com/zh-cn/windows/terminal/)


# PowerShell配置
新版的`PowerShell`虽然功能更新不少，但默认的操作和界面比较不耐看，此时我们便需要借助一些插件来完善他
## PowerShell插件配置
使用管理员身份打开`PowerShell`
依次输入下方三个命令安装插件
```
Install-Module -Name PSReadLine -AllowPrerelease -Force 
Install-Module posh-git -Scope CurrentUser
Install-Module oh-my-posh -Scope CurrentUser                                                
```
然后输入以下命令来配置插件
```
notepad.exe $PROFILE 
```
这会打开默认`PowerShell` 的启动配置文件，如果提示没有该文件，点击创建就好
在打开的文件中输入
```
# 引入 posh-git
Import-Module posh-git

# 引入 oh-my-posh
Import-Module oh-my-posh

#引入 PSReadLine
Import-Module PSReadLine

# 设置 PowerShell 主题
Set-Theme Paradox

#设置 Tab 键补全
Set-PSReadlineKeyHandler -Key Tab -Function MenuComplete

# 设置 Ctrl+d 为退出 PowerShell
Set-PSReadlineKeyHandler -Key "Ctrl+d" -Function ViExit

# 设置 Ctrl+z 为撤销
Set-PSReadLineKeyHandler -Key "Ctrl+z" -Function Undo

# 设置向上键为后向搜索历史记录
Set-PSReadLineKeyHandler -Key UpArrow -Function HistorySearchBackward

# 设置向下键为前向搜索历史纪录
Set-PSReadLineKeyHandler -Key DownArrow -Function HistorySearchForward
```
亦可参考[官方样例](https://raw.githubusercontent.com/PowerShell/PSReadLine/master/PSReadLine/SamplePSReadLineProfile.ps1)来自行修改
## 使PowerShell支持git bash
此时修改的已经差不多了，但`PowerShell`的`git`功能仍有一些缺陷
我们需要修改环境变量，使`PowerShell`更上一层楼
![path](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/path.png)
将`git Bash`目录下的`usr/bin`路径复制进去就好了

# 后记
这是我的第一篇文章，自微软`Bulid 2019`以来，宣布新的终端`Windows Terminal`，我就一直在关注，并第一时间下下来使用，那时的`Windows Terminal`充满bug，动不动就会闪退，蛋疼的操作，蛋疼的配置，让我着实没能香起来，最近`Bulid 2020`大会，微软正式推出`Windows Terminal 1.0`正式版，迫不及待下载后，一个字，香，两个字，真香，虽然还有一些小瑕疵，譬如图形化配置界面，还整json文件呢，真就人均极客了嗷。并没有使用过`cmder`、`Xshell`等第三方终端，所以以上观点仅代表个人审美。溜了溜了

顺便这篇文章的生成与发布均使用新版`Windows Terminal`

>### 参考文献:
> [微软官方文档](https://docs.microsoft.com/zh-cn/windows/terminal/)
> [如何打造好看还好用的 Windows Terminal---ChrAlpha](https://blog.ichr.me/post/windows-terminal-configuration/)
> [5 个 PowerShell 主题，让你的 Windows 终端更好看---少数派](https://sspai.com/post/52907)