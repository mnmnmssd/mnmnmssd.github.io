---
title: hexo+Github+阿里云实现个人博客与自动发布
date: 2020-07-01 22:14:01
slug: hexo安装
categories:
    - 教程
    - 学习
---
# 前言
> 假设以下操作你已经拥有了自己的域名与服务器
# hexo安装与基本语法
## 安装前环境配置:nodejs
前往[nodejs官网](https://nodejs.org/zh-cn/download/)下载所需版本，安装即可
安装好后打开终端输入`node -v`,出现以下代码即表示安装成功
![20200701223401](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/20200701223401.png)
## hexo 配置
在终端中输入`npm install hexo-cli -g`，回车等待安装完成
安装成功后输入`hexo -v`回车，出现以下代码即表示安装成功
![20200701223647](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/20200701223647.png)
新建或选择一个储存`博客文件`的文件夹
在文件夹下打开终端，输入`hexo init`回车，hexo即可在当前目录下生成博客所需的文件，稍作等待后，即可新建完成
目录结构为
```
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```
其余命令自行查询[hexo官方文档](https://hexo.io/zh-cn/)
接下来安装hexo的git插件来使hexo对git支持
在当前目录下输入`npm install hexo-deployer-git --save`回车即可安装
接下来配置服务器方面
# 阿里云方面配置
`下列操作以Centos7系统为代表`
安装`nginx`、`git`过程省略
选择/新建一个目录来作为自己`博客`文件存储的地方
修改权限为：`chmod -R 777 /XXX/XXX/blog/`
新建一个用来发布博客的工具用户`adduser git`,修改他的权限
执行下列命令
```
chmod 740 /etc/sudoers
vim /etc/sudoers
```
在打开的文档中找到`root ALLALL=() ALL`,在他的下面添加一行`git ALLALL=() ALL`,保存退出
输入`sudo passswd git`为`git`用户设置密码及解除锁定
## nginx的配置
进入nginx的安装目录，输入`vim conf/nginx.conf`回车，即可打开nginx的配置文件,修改端口80的参数
```
    server {
        listen       80;   //监听的端口
        server_name  www.xinsuan.xyz; //匹配主机名，例如访问的为www.xinsuan.xyz,自动匹配成功后转发到相应的服务器中

        #access_log  logs/host.access.log  main;
        location / {
            root   /XXX/XXXX/blog; //网站主目录，这里改为要存储博客的目录
            index  index.html index.htm; //匹配网站主页文件
            expires 7d; //缓存设置 (可选)
        }

        ...

    }
```
至此，nginx方面配置结束
## Git配置
输入`su git`切换到新建的`git`用户，选择/新建一个目录来作为远程仓库配合博客发布使用
在新建的目录中输入`git init --bare XXX.git `，其中XXX为自定义名字，配置目录权限
```
chmod -R 777 XXX.git
```
进入`XXX.git`目录下的`hooks`目录，新建钩子文件`post-receive`,编辑文件
```
#!/bin/sh

git --work-tree=存储博客目录 --git-dir=刚刚新建的远程仓库目录 checkout -f
例如：git --work-tree=/usr/bin/blog --git-dir=/usr/repo/XXX.git checkout -f
```
这个文件就是当文件上传到远程仓库后自动`转发`
接下来给文件以运行权限
```
chmod +x post-receive
```
###配置`sshKey`实现免密登录
服务端输入以下命令：`mkdir ~/.ssh`、`vim ~/.ssh/authorized_keys`

本机进入`C:\Users\XXX\.ssh`目录，如果没有则需要git生成`sshKey`
输入以下命令
```shell
git config --global user.name "username" //配置用户名
git config --global user.email "useremail" //配置邮箱

ssh-keygen -t rsa -C "useremail" //生成公钥与私钥,过程中连按三次回车即可，无需设置密码等
```
再次进入`C:\Users\XXX\.ssh`，查看`id_rsa.pub`中的内容(公钥)，将其复制到服务器刚刚新建的`authorized_keys`文件中，保存退出。
至此，服务器方面暂时结束
# 配置GIthub与hexo
进入`hexo`博客目录，打开`_config.yml`文件，这是`hexo`的配置文件，具体配置参考[官方文档](https://hexo.io/zh-cn/)，这里只说一下关于git方面的配置
修改文件中的以下内容
```config
deploy:
  type: git
  repo: git@远程仓库地址/ip:/usr/repo/XXX.git //远程仓库地址
  //例如：git@10.10.10.10:/usr/repo/blog.git
  branch: master //分支选择master即可
```
# 实现自动发布
接下来就是自动发布了，新建一篇文章，输入以下命令即可自动发布
```
hexo new "新建文章"
hexo clean && hexo d -g  //构建并推送至远程仓库，到达远程仓库后触发hooks，自动将文件转发到了博客目录，实现自动化
```
## 收尾工作
将`git`用户权限限制，切换到`root`用户，编辑`/etc/passwd`,在最后添加一句
```
git:x:1000:1000::/home/git:/usr/bin/git-shell  
//这是限制该用户只能使用git-shell，所以并不能登录等其他操作，只是一个发布文章的工具用户
```
其余修改`hexo`主题、各种`hexo`插件等，后期介绍
# 参考文档
> [hexo官方文档](https://hexo.io/zh-cn/)