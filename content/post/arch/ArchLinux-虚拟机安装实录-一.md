---
title: 虚拟机下的ArchLinux 安装实录(一)
description: 已落后版本
slug: 虚拟机下的ArchLinux 安装实录
date: 2020-08-10 23:23:10
categories:
    - 学习笔记
    - Arch
    - Linux
---
# 虚拟机下的ArchLinux 安装实录(一)
## 获取镜像 -> 创建虚拟机

### 获取安装镜像

首先在 [Archlinux.org](https://www.archlinux.org/download/) 处选择适合的镜像与下载方式

![image-20200811113135424](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/image-20200811113135424.png)

这里选择 [163](http://mirrors.163.com/archlinux/iso/2020.08.01/) 源的下载镜像(ISO)

![image-20200811150119046](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/image-20200811150119046.png)

### 创建虚拟机

选择自定义(高级) -> 下一步

![image-20200811150218403](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/111.png)

默认就好 -> 下一步

![image-20200811150306980](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/2222.png)

选择刚刚下载好的镜像 -> 下一步

![image-20200811150345665](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/image-20200811150345665.png)

操作系统选择 `Linux` , 版本根据自己下载的内核选择 , 这里选择 `5.x` 内核 -> 下一步

![image-20200811150517381](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/image-20200811150517381.png)

根据自己情况选择安装位置 -> 下一步

![image-20200811150606483](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/image-20200811150606483.png)

根据自己情况自行配置 , 也可后期更改 -> 下一步

![image-20200811150658662](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/3333.png)

![image-20200811150713274](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/4444.png)

这里选择 `NAT` 连接方式 -> 下一步

![image-20200811150821883](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/image-20200811150821883.png)

默认即可 -> 下一步

![image-20200811150844908](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/image-20200811150844908.png)

![image-20200811150853037](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/image-20200811150853037.png)

视情况而定 , 这里选择 `20G` -> 下一步

![image-20200811150944834](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/image-20200811150944834.png)

一直下一步直到出现完成界面 -> 完成

![image-20200811151034804](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/image-20200811151034804.png)

### 更改启动模式

在虚拟机详情页面 ，点击进入虚拟机设置 ，进入 `选项` 选项卡 ，点击 `高级` 选项 ，在 `固件类型` 处选择 `UEFI`   ，点击确定

![image-20200811152442125](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/5555.png)

![image-20200811152420629](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/6666.png)


## Arch安装

###  安装菜单页面

开启刚创建的虚拟机

这里选择 *`Arch Linux install medium`*, 敲回车[Enter]进入

![image-20200811152739303](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/9999.png)

等待......  进入Live默认Shell

出现这个就代表进入成功了 ，如果没有进入 ，就要考虑是否镜像下载完整了

![image-20200811151606159](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/image-20200811151606159.png)

### 键盘布局

初始键盘布局是标准的 `QWERTY` 布局 ，如果想更改布局 ，则输入以下命令查看支持的布局

`ls /usr/share/kbd/keymaps/**/*.map.gz`

![image-20200811152119693](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/8888.png)

设置键盘布局为以下命令

`loadkeys  布局名称`

这里并不更改布局 ，进行下一步的安装

### 验证启动模式

验证启动模式来为后面的 `GRUB` 引导安装做准备 ，输入以下命令

`ls /sys/firmware/efi/efivars`

若正确显示为以下界面 ，则表示系统以 `UEFI` 模式启动 ，若提示目录不存在，则可能以 `BIOS` 或 `CSM` 等模式启动 ，本教程是以 `UEFI` 模式为基础的

![image-20200811153154877](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/image-20200811153154877.png)

### 网络连接

虚拟机启动了 `NAT` 模式 ，所以默认是直接连接了网络的 ， 可以执行 `ip link` 命令查看网络信息 ， 执行 `ping` 命令查看网络连通情况

![image-20200811153430905](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/image-20200811153430905.png)

### 时间校正

输入以下命令来确保系统时间准确

`timedatectl set-ntp true`

可以使用 `timedatectl status` 来查看时间状态

![image-20200811153720383](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/image-20200811153720383.png)

## 硬盘分区

分区是很重要的 ，这里也有几个坑

### 建立分区

这里使用 `fdisk` 工具来建立分区

输入 `fdisk -l` 查看硬盘信息 ，其中以 `loop`/`rom`/`airoot` 结尾的可以省略

这里我的硬盘被识别并命名为 `/dev/sda` ， 记好这个名称 ， 接下来将在这块硬盘上进行分区

 ![image-20200811154130565](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/7777.png)

输入 `fdisk /dev/sda` 进入分区命令界面 ， 可以输入 `m` 查看命令帮助

这里输入 `g` 新建 `GPT 分区表`

![image-20200811154329134](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/101010.png)

输入 `n` 来新建分区 ， 第一个分区我们把它作为一个 `EFI 系统分区` 

分区标识默认1即可 [Enter]， 分区开始 ，这里我们输入 `2048`  ，敲击回车

结束标识 ，可以直接输入分区大小 ， boot分区不需要太大 ， 输入 `+500M` 即可 ，第一个分区建立完毕。

![image-20200811154722520](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/image-20200811154722520.png)

接下来建立第二个分区 ， 我们把它作为 `SWAP 交换空间`的分区 ，可以理解为 win 的虚拟内存 , 依旧输入 `n`  ，分区标识这里选择 3 ，因为我们打算 2 来作为主分区的标识 ，起始标识默认即可 ， 结束标识因为是作为交换空间来使用 ， 所以可以适当设置大一些 ，至此第二个分区建立完毕

![image-20200811155117805](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/image-20200811155117805.png)

接下来建立主分区 ，输入 `n` ，分区标识默认 2 ，起始标识默认 ， 结束标识默认直到最后 ，至此三个分区全部建立完毕

![image-20200811155248590](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/image-20200811155248590.png)

输入 `w` 来保存并退出分区

![image-20200811155320845](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/image-20200811155320845.png)

可以再执行 `fdisk -l` 查看分区情况 ， 可以看到多出了三个新建的分区

![image-20200811155406918](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/111111.png)

### 设置文件系统类型

接下来设置每个分区的文件系统与格式化

这里注意:  *`EFI系统分区的文件系统必须是FAT32的系统`* ，如果是其他类型的系统 ， 会导致 `GRUB` 安装失败

首先格式化 `EFI 系统分区`  ，输入 `mkfs.fat -F32 /dev/sda1` 来设置分区系统类型为 `FAT32` 并格式化

接下来格式化并设置 `SWAP 分区`  ，输入 `mkswap /dev/sda3` 来初始化 `SWAP 分区` ，接着输入 `swapon /dev/sda3` 激活 `SWAP`

最后格式化主分区 ， 输入 `mkfs.ext4 /dev/sda2` 格式化并设置主分区格式

![image-20200811160513632](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/image-20200811160513632.png)

### 挂载分区

接下来进行分区挂载 ， 首先将主分区挂载到 `/mnt` 目录 ，这也是根目录

`mount /dev/sda2 /mnt`

进行 `EFI 系统分区` 挂载

```shell
mkdir /mnt/efi
mount /dev/sda1 /mnt/efi
```

![image-20200811161158469](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/image-20200811161158469.png)

执行 `lsblk` 查看挂载情况

![image-20200811161344180](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/image-20200811161344180.png)

`SWAP` 分区会自动挂载 ，所以无需设置

### 安装系统镜像和软件包

#### 设置源

因为不可抗力的情况 ，国内使用默认源会出现下载缓慢的情况 ，所以要设置国内源 ，比如清华源等 ，这里编辑 `/etc/pacman.d/mirrorlist` 可以设置国内镜像源 ， 这里我们添加清华源

``` shell
vim /etc/pacman.d/mirrorlist
添加一条记录
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
更新源
pacman -Syy
```

#### 安装软件包

使用 `pacstrap` 脚本 ， 安装 `base` 软件包和 `linux 内核`等等 ，新版Arch不带Vim等软件 ，所以也建议安装上 ，以及 `dhcpcd` , `haveged` 等

最基本的套件为

```shell
pacstrap /mnt base linux linux-firmware
```



可以直接执行我这条指令

```shell
pacstrap /mnt base base-devel linux linux-firmware dhcpcd haveged
```

等待下载与安装完成  ，安装完成后进入系统配置

## 系统配置

生成 `fstab` 文件

```shell
genfstab -U /mnt >> /mnt/etc/fstab
cat /mnt/etc/fstab
```

![image-20200811163334589](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/121212.png)

### Chroot

进入新安装的系统

```shell
arch-chroot /mnt
```

### 语言与时区设置

#### 设置时区

```she
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc
```

#### 设置语言地区

```shel
vim /etc/locale.gen
找到需要的地区语言 ，将注释移除
如: en_US.UTF-8 UTF-8
	zh_CN.UTF-8 UTF-8
执行
locale-gen
生成locale信息
创建 locale.conf 文件 ，并添加
LANG=en_US.UTF-8
也可设置为中文 ，视情况而定
```

### 网络配置

编辑主机名称

```shel
vim /etc/hostname
XXX
```

编辑 `hosts`  添加

```she
127.0.0.1	localhost
::1			localhost
127.0.0.1	XXX.localdomain XXX
# 其中XXX为上一步创建的主机名
```

设置 `dhcpcd` / `haveged` 开机启动

`dhcpcd` 会自动分配ip

`haveged` 会在开机时自动搜集 `系统熵` 解决开机启动缓慢的问题

```she
systemctl enable dhcpcd
systemctl enable haveged
```



### 用户设置

设置 `root` 密码

```she
passwd
```



添加一个用户并设置权限与密码

```she
# useradd -m -g users -s /bin/bash XXX
passwd XXX
vim /etc/sudoers
在 root ALL=(ALL) ALL 下添加一句
XXX ALL=(ALL) ALL
```

## 安装GRUB引导

安装 `grub` 和 `efibootmgr` 软件包

```shel
pacman -S grub efibootmgr
```

#### GRUB 安装

安装前使用 `uname -a` 查看自己的系统架构

![image-20200811171434724](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/131313.png)

```she
grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=GRUB 
# 注意
# --target后为你的系统架构 ，如我的为x86_64 ，--efi-directory后为你的 EFI系统分区地址
```

此处有一个小坑 ，若你的 `EFI 系统分区` 不是`fat32`类型 ， 就会安装失败

如果为`fat32`仍安装失败 ， 可以用 `gdisk` 将分区设置 `ef00`

如图所示 ， 即为安装完成

![image-20200811172429643](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/1414.png)

#### 生成配置文件

```shell
grub-mkconfig -o /boot/grub/grub.cfg
```

![image-20200811172715340](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg/blog/2020/image-20200811172715340.png)

## 安装完成

退出新系统 , 重启

```she
exit
reboot
```

### 进入新系统 ，开始愉快玩耍

## 使用AUR

既然用上了 `Arch` 不能不来用一下 `AUR` ，`AUR` 工具很多 ，这里选择 `yay` 

首先添加 `pacman` 的 `archlinuxcn` 源

修改 `/etc/pacman.conf`  ，在最后添加以下内容

```she
[archlinuxcn]
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

执行 `pacman -Syy` 更新源

安装 `archlinuxcn-keyring` 来导入 `GPG key`

安装 `yay` 

```shell
pacman -S yay
```

配置 `AUR` 源

```she
yay --aururl "https://aur.tuna.tsinghua.edu.cn" --save
```



剩下的就是愉快的安装软件与配置 ，接下来会更新美化 、配置等教程

## 几个坑点

- `EFI 系统分区`必须要是 `FAT32` 格式
- 新版`arch`貌似在安装后不包括 `dhcpcd` ，所以在执行安装安装脚本时请安装 `dhcpcd` 服务 ，否则只能进入 `LiveCD` 安装了
- 如果安装后开机启动缓慢 ，只停留一个界面光标闪烁 ，这是搜集`系统熵`的问题 ， 安装 `haveged` 并开启服务可解决
- `archlinux-keyring` 安装失败 ，并提示 `could not be locally signed(无法在本地签署)`  ， 请看[这里](https://www.archlinuxcn.org/gnupg-2-1-and-the-pacman-keyring/) 

> 2020年8月11日
>
> by ---笑的心酸