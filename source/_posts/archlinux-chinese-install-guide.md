---
title: Arch Linux从入门到传教——中文安装教程
date: 2022-03-26 02:19:37
author: 
 - Tony Smith
tags: 
 - ArchLinux
 - Linux
 - 保姆级教程
categories: 教程
---

<div class="info">

> 本教程最后更新时间2025年10月7日

</div>

<div class="warning">


> Warning
>
> 本文仅为 [Arch Linux](https://archlinux.org) 的第三方安装教程，不代表 [Arch Linux](https://archlinux.org) 官方，因此跟[Arch Linux Wiki](https://wiki.archlinux.org)中的[Installation guide](https://wiki.archlinux.org/title/Installation_guide_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))有所差异，但这也意味着本教程的内容将会**更为全面**
>

</div>

<div class="info">

>[Arch Linux](https://archlinux.org) 的特点是[Rolling Update](https://zh.wikipedia.org/zh-tw/%E6%BB%BE%E5%8B%95%E7%99%BC%E8%A1%8C)(滚动更新)，是指软件开发中**经常性**将更新发送到软件的概念
>
>也就是说，[Arch Linux](https://archlinux.org) 的软件库是经常性更新的，你也需要**经常更新软件源**，操作方法很简单，只要`sudo pacman -Syu`即可；如果不经常更新，则可能导致滚动更新后**无法正常使用系统**
>
>如果你并不想~~或者说懒~~经常更新软件源，我建议你使用`linux-lts`内核

</div>

# 准备操作

首先你需要确保你的网络畅通（放心，这不需要你准备梯子，Arch Linux是有中国镜像源的），Arch Linux是**在线安装**

## 下载LiveCD

Arch Linux有多个镜像源，在[Arch Linux Downloads](https://archlinux.org/download/)中往下滑就可以找到

我个人使用的是[清华大学开源软件镜像站](https://mirrors.tuna.tsinghua.edu.cn/)，你也可以用[北京外国语大学开源软件镜像站](https://mirrors.bfsu.edu.cn/)，又或者[中国科学技术大学开源软件镜像](https://mirrors.ustc.edu.cn)

这里以清华大学开源软件镜像站为例

首先前往[清华大学开源软件镜像站](https://mirrors.tuna.tsinghua.edu.cn/archlinux/iso/2022.03.01/)中下载ISO

![](https://lantapic.bangumi.cyou/img/20220326030059.png)

下载完成之后备用

## 刻录到U盘

你可以使用以下几种方式来将Arch Linux镜像刻录到U盘中

<div class="danger">

> 刻录到U盘会导致数据丢失，请在操作之前备份好你的U盘数据

</div>

<div class="info">

>推荐使用Ventoy，因为Ventoy只需要一次安装到U盘之后就不再需要刻录的操作，还可以跟资料共存

</div>


| 名称                                          | 使用方式                                        | 备注               |
| --------------------------------------------- | ----------------------------------------------- | ------------------ |
| [Ventoy](https://ventoy.net/)                 | 将Ventoy安装到U盘后直接把ISO文件复制到U盘中即可 | 一次安装，一盘多用 |
| [Rufus](https://rufus.ie/)                    | 选择ISO文件然后选择U盘直接刻录即可              | 注意选择引导方式   |
| [balenaEtcher](https://www.balena.io/etcher/) | 选择ISO文件然后选择U盘点击Flash开始刻录         | 最简单的方法       |

## 确定分区（必要）

在纯文字的环境中你可能**很难分辨**分区，如果**操作失误**（删错分区）~~则删库跑路~~

所以我建议你**事先**在Windows环境中**确定**要安装的系统分区，以确保不会出现失误导致的~~数据灰飞烟灭~~

## 引导设置（UEFI用户）

进入BIOS中，如果你是有UEFI的电脑那么你应该能看到带有**CSM**字样的选项，将他**Disable**掉

还有一些电脑带有**Secure Boot（安全启动）**，找到**Secure Boot**的字样**Disable**即可

## 重启开始安装

确保您的U盘已经写入了镜像、分区已经确定好没问题之后

~~万事具备，只欠东风~~

重启进入LiveCD

# LiveCD

## 确保网络畅通

前面提到，Arch Linux是在线安装方式，所以你必须要确保LiveCD中的系统已经连接到了网络

### 有线网络

如果你是有线网络，大概率不需要担心网络问题，因为LiveCD已经通过DHCP服务自动联网

不过发现网络出现玄学问题，你可以执行`systemctl restart dhcpcd`来重启DHCP服务让Live CD重新获取IP

### WiFi连接

LiveCD中自带连接WiFi的工具，你可以一步一步让Live CD连接到WiFi

```bash
[archlinux@archlinux ~]$ iwctl  # 进入 iwd 的交互提示符
[iwd]# device list  # 列出所有 WiFi 设备
[iwd]# station <device> scan  # 扫描 WiFi 网络
[iwd]# station <device> connect <ssid>  # 连接到 WiFi 网络
```

（以上文段来自[Chi_Tang的博客](https://chitang233.github.io/posts/arch-guide/#WiFi-%E8%BF%9E%E6%8E%A5)）

### 测试网络

最简单的办法就是用Ping

```bash
ping -c 3 archlinux.org
```

没有报错则网络连接正常

## 更改系统时间（可选，但还是建议执行）

```bash
timedatectl set-ntp true
```

## 分区

<div class="info">
在本教材中会使用 EXT4 格式作为演示，你可以转为使用目前比较新颖的BTRFS，但要注意BTRFS的情况下需要单独设置一个交换分区而不能使用`swapfile`这样文件形式上的交换文件
</div>

先通过`lsblk`或`fdisk -l`的方式确定硬盘，出现`/dev/某某某`的就是这个硬盘的设备名，记住硬盘的设备名，等会要用到

### 分区的工具

LiveCD中有`parted`、`fdisk`、`cfdisk`等工具，前两个比较麻烦，`cfdisk`拥有半图形界面，更适合小白使用

首先我们要打开cfdisk

<div class="info">

> 设sdX为你的硬盘号

</div>

```bash
cfdisk /dev/sdX # 后面这个就是硬盘设备名
```

然后就会出现一个界面，这里以一个中文化的界面作为对照

![](https://lantapic.bangumi.cyou/img/20220326033131.png)

### 分区格式

<div class="info">

>  用户资料分区是可选的，可以将用户资料存放在系统盘中

</div>

#### MBR（传统引导）推荐分区格式：

| 用途                 | 文件系统 | 分区推荐大小 | 挂载点 | 文章中的分区号                   |
| -------------------- | -------- | ------------ | ------ | -------------------------------- |
| 系统分区             | ext4     | 50GiB        | /      | /dev/sda2                        |
| 用户资料分区（可选） | ext4     | 50GiB        | /home  | （本文章中没有设置用户资料分区） |

#### GPT（UEFI引导）推荐分区格式：

<div class="danger">

> 如果你之前已经在硬盘中另外装了其他使用UEFI引导的系统（比如Windows）请务必不要把引导分区删掉重建，否则会导致原来的系统无法进入！

</div>

| 用途                 | 文件系统 | 分区推荐大小 | 挂载点 | 文章中的分区号                   |
| -------------------- | -------- | ------------ | ------ | -------------------------------- |
| 引导分区             | FAT32    | 300MiB       | /boot  | /dev/sda1                        |
| 系统分区             | ext4     | 50GiB        | /      | /dev/sda2                        |
| 用户资料分区（可选） | ext4     | 50GiB        | /home  | （本文章中没有设置用户资料分区） |

## 格式化分区

<div class="danger">

> 如果你之前已经在硬盘中另外装了其他使用UEFI引导的系统（比如Windows）请务必不要把引导分区格式化，否则会导致原来的系统无法进入！

</div>

<div class="info">

> 设引导分区为 /dev/sda1，系统分区为 /dev/sda2

</div>

```bash
mkfs.ext4 /dev/sda2 # 格式化 /dev/sda2 为 ext4 文件系统（系统分区或用户资料分区）
mkfs.vfat /dev/sda1 # 格式化 /dev/sda1 为 FAT32 文件系统（引导分区）
```

## 挂载分区

### GPT

<div class="info">

> 设引导分区为 /dev/sda1，系统分区为 /dev/sda2，用户资料分区为/dev/sda3

</div>

```bash
mount /dev/sda2 /mnt # 挂载系统分区到 /mnt( / 分区)
mkdir /mnt/boot /mnt/home # 新建 /mnt/boot 文件夹和 /mnt/home 文件夹
mount /dev/sda1 /mnt/boot # 挂载引导分区到 /mnt/boot
mount /dev/sda3 /mnt/home # 挂载用户资料分区到 /mnt/home （ /home 分区，在有用户资料分区的情况下才执行）
```

### MBR

```bash
mount /dev/sda1 /mnt  # 挂载系统分区到 /mnt( / 分区)
mkdir /mnt/home # 创建 /mnt/home 文件夹
mount /dev/sda2 /mnt/home # 挂载用户资料分区到 /mnt/home( /home 分区，在有用户资料分区的情况下才执行)
```

（以上文段参考了[Chi_Tang的博客](https://chitang.tech/posts/arch-guide/#%E6%8C%82%E8%BD%BD)并进行修改）

## 更换软件源

接下来我们需要更新一下镜像源以加快我们的下载速度

```bash
nano /etc/pacman.d/mirrorlist # 新建软件源列表
```

在软件源列表中第一行写入`Server = 地址`即可，这里给大家列几个镜像源

| 名称                                   | 地址 |
| -------------------------------------- | ---- |
| [清华大学开源软件镜像站](https://mirrors.tuna.tsinghua.edu.cn) | https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch |
| [北京外国语大学开源软件镜像站](https://mirrors.bfsu.edu.cn)  | https://mirrors.bfsu.edu.cn/archlinux/$repo/os/$arch         |
| [中国科学技术大学开源软件镜像](https://mirrors.ustc.edu.cn) | https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch |

在上面选择一个镜像源，假如我要用清华的，那就`Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch`

当然如果速度还是上不去可以尝试删除其他镜像源

保存并退出即可

## 安装本体到硬盘

### 内核的选择

下面为Arch Linux官方源中可选的部分内核，可以根据你自己的需求选择

| 内核        | 特点                                           |
| ----------- | ---------------------------------------------- |
| `linux`     | 官方的默认内核                                 |
| `linux-lts` | 官方的长期支持内核，版本较低，但相对不容易滚挂 |
| `linux-zen` | 社区制作的更适合日常使用的内核                 |

（以上表格来自[Chi_Tang的博客](https://chitang.tech/posts/arch-guide/#%E5%86%85%E6%A0%B8%E7%9A%84%E9%80%89%E6%8B%A9)）

### 安装命令

```bash
pacstrap /mnt base base-devel linux-firmware <linux-kernel> <linux-kernel>-headers grub vim networkmanager iwd os-prober efibootmgr
```

将 `<linux-kernel>` 替换为你所选定的内核

## 生成分区表

```bash
genfstab -U /mnt > /mnt/etc/fstab
```

可以自己检查一下 `/mnt/etc/fstab` 以确保信息正确无误

## 切换到安装好的系统

接下来我们要通过**chroot**切换到我们安装好的系统的终端进行初步的系统设置

```bash
arch-chroot /mnt
```

<div class="info">

> 要退出返回LiveCD就直接输入`exit`即可

</div>

## 设置时区

```bash
ln -sf /usr/share/zoneinfo/<Region>/<City> /etc/localtime
hwclock --systohc
```

`<Region>`和`<City>`分别是你所在的地区和城市，例如上海时间`Asia/Shanghai`

## 设置网络

### 计算机名

```bash
vim /etc/hostname
```

在文件中写入你的计算机名，例如 `my-pc-1s-archlinux`

保存并退出

### hosts 文件

```bash
vim /etc/hosts
```

在文件中写入下面的内容

```none
127.0.0.1 localhost
::1       localhost
127.0.1.1 <hostname>.localdomain  <hostname>
```

将 `<hostname>` 替换为你的计算机名，保存并退出

## 配置本地化

### locale.gen

```bash
vim /etc/locale.gen
```

在文件中找到 `en_US.UTF-8 UTF-8` 及 `zh_CN.UTF-8 UTF-8`，删除前面的 `#` 以取消注释

同样地，在这里也可以设置其他语言的的 `locale`，例如 `ja_JP.UTF-8 UTF-8`

```bash
locale-gen  # 生成本地化文件
```

### locale.conf

```bash
vim /etc/locale.conf
```

在文件中加入 `LANG=en_US.UTF-8`，保存并退出

<div class="warning">

> 请不要在这里设置任何其他的语言，否则可能会导致终端乱码

</div>

## 安装引导

如果你需要GRUB能引导到其他系统（例如Windows）我们要先开启GRUB使用**os-prober**才可以让GRUB引导到其他系统

```bash
vim /etc/default/grub
```

在结尾或者其他位置中添加`GRUB_DISABLE_OS_PROBER="false"`保存并退出即可

### UEFI + GPT

```bash
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=grub # 在 /boot 中安装GRUB引导
grub-mkconfig -o /boot/grub/grub.cfg  # 生成 GRUB 配置
```

### Legacy + MBR

<div class="info">

> 设sdX为你的硬盘号

</div>

```bash
grub-install --target=i386-pc /dev/sdX  # 在 /dev/sdX 安装引导，不要加分区号
grub-mkconfig -o /boot/grub/grub.cfg  # 生成 GRUB 配置
```

## 重启前的准备

```bash
passwd root # 设置root的密码
systemctl enable NetworkManager # 让NetworkManager开机启动，注意大小写
```

## 重启

<div class="success">

> 到这里，我们的LiveCD的安装步骤就完成了，曙光就在眼前了！接下来我们进入系统进行初步设定

</div>

# 系统初步配置

## 创建新用户

很多软件是不能直接使用root用户的，而且直接使用root用户是很危险的，~~比如运行一个`rm -rf /*`都不带`sudo`的~~

```bash
useradd -m -G wheel 用户名
passwd 用户名
```

### 将新建的用户设置权限

在终端中运行

```bash
EDITOR=vim visudo
```

在这个配置文件中找到`%wheel ALL=(ALL:ALL) ALL`，如果前面带个`#`号就给它删掉，保存并退出

## 设置SWAP（交换文件）

```bash
sudo dd if=/dev/zero of=/swapfile bs=1M count=<size>  # 在 size 处填写需要的 swap 空间大小(单位 MiB)
sudo mkswap /swapfile
sudo chmod 600 /swapfile
sudo swapon /swapfile
```

使用vim编辑`/etc/fstab`，在末尾中加入

```bash
/swapfile none swap defaults 0 0 # 注意空格
```

## 设置pacman.conf

```bash
sudo vim /etc/pacman.conf
```

### pacman美化


<div class="info">

> 这一步是可选的，只是为了让你的pacman更好看

</div>

把`Color`前面的`#`去掉，让pacman能显示出颜色（能帮助你更好地判断内容）

把 `VerbosePkgLists` 前面的`#`去掉，让 pacman 以表格显示更详细的信息

##  额外软件源

#### multilib

`multilib` 软件源中包含一些 32 位的依赖包

滑到文件后面，找到

```none
#[multilib]
#Include = /etc/pacman.d/mirrorlist
```

去掉这两行前面的 `#` 以启用 `multilib` 软件源

### Arch Linux CN

`Arch Linux CN` 源中包含许多在国内使用 Linux 常用的软件包，而且软件包下载速度比较快

在文件最后面添加下面这两行

```none
[archlinuxcn]
Server = https://mirrors.bfsu.edu.cn/archlinuxcn/$arch
```

保存并退出后更新软件源

```bash
sudo pacman -Sy
```

安装 `archlinuxcn-keyring`

```bash
sudo pacman -S archlinuxcn-keyring
```

## AUR 

AUR源包含着很多有用的个人开发者软件，所以安装AUR工具可以让我们从AUR源中获取很多个人开发者的优秀软件

```bash
sudo pacman -S yay
```

## 安装中文

### 安装Fcitx5输入法

```bash
sudo pacman -S fcitx5-im # Fcitx5本体组
sudo pacman -S fcitx5-chinese-addons # 中文输入引擎
sudo pacman -S fcitx5-pinyin-zhwiki # 中文维基百科词库
yay -S fcitx5-pinyin-moegirl # 萌娘百科词库（二刺螈词库）
```

<div class="warning">

> 因为萌娘百科词库本体在Github，所以你可能需要开梯子才能访问

</div>

### 设置环境变量

```bash
vim ~/.xprofile
```

添加下面这几行:

```bash
export LANG=zh_CN.UTF-8
export LC_ALL=zh_CN.UTF-8
export GTK_IM_MODULE=fcitx # 带有fcitx字样的是为了能让Fcitx5输入法能正常使用
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx
export SDL_IM_MODULE=fcitx
```

<div class="info">

> Fcitx5输入法需要进入图形界面的“设置”中才能进一步地设置Fcitx5

</div>

### 安装字体

安装下面几个字体以让中文能正常显示

```bash
sudo pacman -S wqy-microhei # 文泉驿
sudo pacman -S adobe-source-sans-fonts  # 思源黑体
```

# 图形界面

## 显卡驱动

<div class="info">

> 部分显卡驱动需要启用mutilib源（问题不大，我们前面已经启用了）
> 还有一些带有<sup>AUR</sup>的就是来自AUR的软件包，前面我们已经安装了yay所以问题不大

</div>

<div class="info">

> 以下资料整理来自[Chi_Tang的博客](https://chitang.tech/posts/arch-guide/)

</div>

#### Intel 显卡

```bash
sudo pacman -S xf86-video-intel # 驱动本体
sudo pacman -S mesa # OpenGL 支持
sudo pacman -S lib32-mesa # 32 位 OpenGL 支持
sudo pacman -S vulkan-intel # Vulkan 支持
paru -S intel-opencl  # OpenCL 支持
```

#### NVIDIA 显卡

##### 专有驱动

本体:

|           | GeForce 930(NV110 及更新) | GeForce 630-920(NVE0) | GeForce 400/500/600(NVCx & NVDx) | GeForce 8/9(NV5x, NV8x, NV9x, NVAx) 不推荐安装专有驱动 | GeForce 7 及以下 |
| --------- | ------------------------- | --------------------- | -------------------------------- | ------------------------------------------------------ | ---------------- |
| linux     | nvidia                    | nvidia-470xx-dkmsAUR  | nvidia-390xx-dkmsAUR             | nvidia-340xx-dkmsAUR                                   | 不支持           |
| linux-lts | nvidia-lts                | nvidia-470xx-dkmsAUR  | nvidia-390xx-dkmsAUR             | nvidia-340xx-dkmsAUR                                   | 不支持           |
| 其他      | nvidia-dkms               | nvidia-470xx-dkmsAUR  | nvidia-390xx-dkmsAUR             | nvidia-340xx-dkmsAUR                                   | 不支持           |

```bash
sudo pacman -S <driver> # 驱动本体
sudo pacman -S <driver>-utils # OpenGL 支持
sudo pacman -S lib32-<driver>-utils # 32 位 OpenGL 支持
sudo pacman -S opencl-nvidia  # OpenCL 支持
```

##### 开源驱动

```bash
sudo pacman -S xf86-video-nouveau # 驱动本体
sudo pacman -S mesa # OpenGL 支持
sudo pacman -S lib32-mesa # 32 位 OpenGL 支持
```

#### AMD / ATI 显卡

##### 开源驱动

| 架构                                            | 驱动              | OpenGL | 32 位 OpenGL | Vulkan        | 32 位 Vulkan        |
| ----------------------------------------------- | ----------------- | ------ | ------------ | ------------- | ------------------- |
| RDNA, RDNA 2, GCN 1, GCN 2, GCN 3, GCN 4, GCN 5 | xf86-video-amdgpu | mesa   | lib32-mesa   | amdvlk        | lib32-amdvlk        |
| GCN 1, GCN 2, TeraScale 或更老                  | xf86-video-ati    | mesa   | lib32-mesa   | vulkan-radeon | lib32-vulkan-radeon |

```bash
sudo pacman -S opencl-mesa  # OpenCL 支持
```

##### 闭源驱动

AMD 闭源驱动仅支持 `RDNA`, `RDNA 2`, `GCN 3`, `GCN 4`, `GCN 5` 架构的显卡

```bash
sudo pacman -S xf86-video-amdgpu # 驱动本体
paru -S amdgpu-pro-libgl  # OpenGL 支持
paru -S opencl-amd  # OpenCL 支持
paru -S vulkan-amdgpu-pro # Vulkan 支持
```

#### 其他显卡

```bash
sudo pacman -S xorg-drivers
sudo pacman -S mesa # OpenGL 支持
sudo pacman -S lib32-mesa # 32 位 OpenGL 支持
sudo pacman -S pocl # OpenCL 支持
```

## 图形桌面

常见的图形桌面有以下几种

| 名称                                                         | 官方预览图                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [KDE Plasma](https://chitang.tech/posts/arch-guide/#kde-plasma) | ![KDE Plasma](https://kde.org/content/plasma-desktop/plasma-widgets.png) |
| [GNOME](https://chitang.tech/posts/arch-guide/#gnome)        | ![GNOME](https://www.gnome.org/wp-content/uploads/2021/03/wgo-splash-40.png) |
| [Xfce 4](https://chitang.tech/posts/arch-guide/#xfce)        | ![Xfce 4](https://cdn.xfce.org/about/screenshots/4.16-1.png) |

还有一种就是[深度](https://www.deepin.org)的DDE（深度桌面环境），我不推荐你使用，深度的桌面环境在其他系统会有很多的Bug

我推荐你使用KDE Plasma，比较简洁美观

#### KDE Plasma

```bash
sudo pacman -S plasma kde-applications
```

`kde-applications` 是可选的，其中包含 KDE 的其他应用

#### GNOME

```bash
sudo pacman -S gnome gnome-extra
```

`gnome-extra` 是可选的，其中包含 GNOME 的其他应用

#### XFCE

```bash
sudo pacman -S xfce4 xfce4-goodies
```

`xfce4-goodies` 是可选的，其中包含 XFCE 的其他应用

## 图形登陆管理器

图形登录管理器用于更方便地启动桌面环境及登录账户

#### SDDM

推荐与 [KDE Plasma](https://chitang.tech/posts/arch-guide/#kde-plasma) 配合使用

```bash
sudo pacman -S sddm
sudo systemctl enable sddm
```

#### GDM

推荐与 [GNOME](https://chitang.tech/posts/arch-guide/#gnome) 配合使用

```bash
sudo pacman -S gdm
sudo systemctl enable gdm
```

#### LXDM

```bash
sudo pacman -S lxdm
sudo systemctl enable lxdm
```

#### LightDM

```bash
sudo pacman -S lightdm
sudo systemctl enable lightdm
```

# 常用软件和工具

下面给大家讲几个常用的软件和工具

> 这里注意一下，带有**<sup>AUR</sup>**的就是来自**AUR源**的软件，如果你先前没有安装yay请翻回目录中安装

## 通讯软件

| 名称                                  | 包名                   | 备注                                              |
| ------------------------------------- | ---------------------- | ------------------------------------------------- |
| LinuxQQ sup>AUR</sup> | aur/linuxqq   | 来自腾讯官方的基于QQNT框架（修改版Electron）的QQ客户端 |
| 微信<sup>AUR</sup>                    | aur/wechat-bin | 微信官方原生Linux桌面版（基于Qt,与最新的Windows、macOS版本一致）                           |
| Telegram                              | telegram-desktop       | Telegram官方客户端                                |

## Zsh
zsh 是一种 Linux 下的 Shell，相比于Linux默认的 bash 它可以扩展功能和美化以方便终端使用。
```bash
sudo pacman -S zsh
```

将 zsh 设置为默认的 Shell
<div class="warning">
如果你正在使用KDE的Konsole终端，你需要另外再设置使用 zsh
</div>
```bash
chsh -s /bin/zsh
```

### 一些有用的插件

安装 oh-my-zsh（一个 zsh 的配置框架）
```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

安装历史命令自动补全
```bash
git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
```

安装高亮显示
```bash
git clone git://github.com/zsh-users/zsh-syntax-highlighting $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
```

安装完毕后编辑配置文件 `~/.zshrc`，将配置文件中的plugins加入刚刚安装的两个插件，像这样：
```
plugins=(
  git
  zsh-autosuggestions
  zsh-syntax-highlighting
)
```

更新配置文件
```bash
source .zshrc
```


# 疑难解答

## 更新pacman源的时候出现error setting certificate verify locations

输入以下命令解决

```bash
trust extract-compat
```

（来自[Reddit r/Proxmox](https://www.reddit.com/r/Proxmox/comments/plh41w/arch_linux_lxc_error_setting_certificate_verify/)）