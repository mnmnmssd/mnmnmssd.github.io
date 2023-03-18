---
title: hexo升级实录
date: 2020-07-18 19:49:33
description: 已落后版本
slug: hexo升级实录
categories:
    - 随笔
    - hexo
---
# hexo升级实录
今天升级了`nodeJS`到14版本，然后使用`hexo`时出现如下警告
```
(node:17352) Warning: Accessing non-existent property 'lineno' of module exports inside circular dependency
(Use `node --trace-warnings ...` to show where the warning was created)
(node:17352) Warning: Accessing non-existent property 'column' of module exports inside circular dependency
(node:17352) Warning: Accessing non-existent property 'filename' of module exports inside circular dependency
(node:17352) Warning: Accessing non-existent property 'lineno' of module exports inside circular dependency
(node:17352) Warning: Accessing non-existent property 'column' of module exports inside circular dependency
(node:17352) Warning: Accessing non-existent property 'filename' of module exports inside circular dependency
```
并且构建文件错误，遂想到是因为`node`版本与当前`hexo`不匹配的原因，进到hexo的官网发现其发布了新的版本，所以应升级尝试一下
hexo升级分为以下四步，还是很简单的
- 使用npm升级命令 `npm -i hexo -g` ，但是并无效果
- 安装`npm-check`: `npm install -g npm-check`，并使用该命令来检查插件版本
- 安装`npm-upgrade` : `npm install -g npm-upgrade`,并使用该命令来升级插件
- 使用`npm update -g` 与 `npm update --save`命令

此时全局hexo升级完成，但是用来发布博客的版本尚未升级
此时应新建一个博客文件夹，执行`hexo init`命令，然后将旧博客的配置文件:`package.json` 、 `_config.yml` 以及 `主题文件` 、`md文章`复制到新的博客文件夹相应位置中
然后在新目录下执行 `npm update --save`来安装/升级所需依赖
此时`hexo`升级工作完成
