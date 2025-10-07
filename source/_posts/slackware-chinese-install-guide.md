---
title: 这可能是你能找到的全网有关Slackware Linux最详细的中文安装教程
date: 2025-10-01 10:46:27
author: 
 - Tony Smith
tags: 
 - Slackware
 - Linux
 - 保姆级教程
categories: 教程
---
# 前言
因为某些原因，我开始对各种Linux发行版或类UNIX系统的日常使用环境感兴趣。最近我在研究 Slackware Linux，发现有关 Slackware Linux 的中文资料奇少~~（可能这也是为什么有人说 Slackware Linux 的存在感很低的原因吧）~~，所以闲来没事决定写一篇教程作为自己的研究记录报告。

在本教程中，我会和大家介绍一下 Slackware Linux 以及分享一下我安装Slackware Linux总结下来的经验。

## 什么是 Slackware Linux
Slackware Linux 是一个从1993年7月16日创立的Linux发行版，距今已有30多年历史，且仍然活跃维护中，属于是为数不多现存最古老的Linux发行版。以稳定、保守和“最接近UNIX”而闻名。

Slackware Linux 遵循[KISS （保持简洁，傻瓜）](https://zh.wikipedia.org/wiki/KISS%E5%8E%9F%E5%88%99)，所以使用Slackware Linux会有一定的门槛，适合对Linux比较熟悉的人群，不适合新手使用。

Slackware Linux 对于32位的老架构的电脑仍有积极维护，甚至你可以通过 Slackware Linux 安装到最新的适配了32位的软件。

## 有关 Slackware Linux 使用前你需要注意的一些事情
### 版本
Slackware Linux 有两个版本：
- 稳定版本（截止本文章撰写时最新的稳定版本是2022年发布的15.0），软件不会频繁更新，只会有定期的安全性更新。
- Slackware -current（[滚动更新](https://zh.wikipedia.org/zh-cn/%E6%BB%BE%E5%8B%95%E7%99%BC%E8%A1%8C)版本），Slackware Linux 测试最新的软件都会在这个分支上，*很少会有软件不稳定的情况发生，就算有也会下次更新解决。*[（来源）](https://ltlnx.tw/slackware-chinese-install-guide.html)适合习惯了滚动更新发行版想要追新的人。**（本文将以 Slackware -current 作为演示）**

值得注意的是 Slackware -current [没有**官方构建的 Live CD ISO**](https://www.reddit.com/r/slackware/comments/1bxmynb/why_isnt_there_a_slackware_current_dvd_iso/)，但是你可以使用 AlienBob 构建的Live CD ISO。

稳定版本下载：[32位](https://mirrors.slackware.com/slackware/slackware-iso/slackware-15.0-iso/) [64位](https://mirrors.slackware.com/slackware/slackware-iso/slackware64-15.0-iso/)
Slackware -current下载（**AlienBob 构建的Live CD ISO**）：[32位](https://slackware.nl/slackware/slackware-current-iso/)   [64位](https://slackware.nl/slackware/slackware64-current-iso/)

### 包管理器
Slackware Linux 的包管理器在安装某个包的时候**不会去安装这个包所需要的依赖**，包括卸载时也不会。（卸载时不处理依赖有个好处就是不会被误删一些还在使用的包）如果你觉得安装包的时候还需要自己手动处理依赖很麻烦的话可以使用一些第三方工具去解决。

# 基础安装
本教程将以 Slackware -current 作为演示，使用 UEFI 引导环境。
## 准备工作
下载好 Slackware -current 的 Live CD ISO ，并使用你喜欢的方式刻录到U盘（例如使用Balena Etcher，或者使用Ventoy）
## 进入Live CD
GRUB 引导选择第一个
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-112154.png)
进入后会提示你是否需要调整键盘布局，如果你是美式键盘直接按下回车就可以了
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-112332.png)
接下来输入 `root` 登录到 root 用户（没有密码）
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-112353.png)
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-112406.png)
### 硬盘分区
对于很多人来说我推荐使用 `cfdisk` 进行分区，因为其类图形界面更方便观察硬盘的分区表。
输入 `lsblk` 确认安装的硬盘号
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-112640.png)
```bash
cfdisk /dev/sdX
```
<div class="info">
sdX 为你确认安装的硬盘号
</div>
对于 UEFI 引导使用 GPT 分区表，如果你是 Legacy 引导则使用 DOS(aka MBR) 分区表。
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-112654.png)

#### 分区格式

<div class="info">

>  用户资料分区是可选的，可以将用户资料存放在系统盘中

</div>

#### MBR（传统引导）推荐分区格式：

| 用途                 | 文件系统 | 分区推荐大小 | 挂载点 | 文章中的分区号                   |
| -------------------- | -------- | ------------ | ------ | -------------------------------- |
| 系统分区             | ext4     | 50GiB        | /      | /dev/sda3                        |
| 用户资料分区（可选） | ext4     | 50GiB        | /home  | （本文章中没有设置用户资料分区） |
| 交换分区（可理解为虚拟内存，可选）    |   1GiB    |   无  |   /dev/sda2

#### GPT（UEFI引导）推荐分区格式：

<div class="danger">

> 如果你之前已经在硬盘中另外装了其他使用UEFI引导的系统（比如Windows）请务必不要把引导分区删掉重建，否则会导致原来的系统无法进入！

</div>

| 用途                 | 文件系统 | 分区推荐大小 | 挂载点 | 文章中的分区号                   |
| -------------------- | -------- | ------------ | ------ | -------------------------------- |
| 引导分区             | FAT32    | 300MiB       | /boot  | /dev/sda1                        |
| 系统分区             | ext4     | 50GiB        | /      | /dev/sda3                        |
| 用户资料分区（可选） | ext4     | 50GiB        | /home  | （本文章中没有设置用户资料分区） |
| 交换分区（可理解为虚拟内存，可选）    |   1GiB    |   无  |   /dev/sda2


在确认分区好后保存更改并退出
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-113922.png)
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-113937.png)

#### 格式化分区

<div class="danger">

> 如果你之前已经在硬盘中另外装了其他使用UEFI引导的系统（比如Windows）请务必不要把引导分区格式化，否则会导致原来的系统无法进入！

</div>

<div class="info">

> 设引导分区为 /dev/sda1，系统分区为 /dev/sda2

</div>

```bash
mkfs.ext4 /dev/sda3 # 格式化 /dev/sda3 为 ext4 文件系统（系统分区或用户资料分区）
mkfs.vfat /dev/sda1 # 格式化 /dev/sda1 为 FAT32 文件系统（引导分区）
mkswap /dev/sda2 # 格式化 /dev/sda2 为 swap 分区（交换分区）
```

### 运行安装向导
在确认硬盘分区完毕后，输入 `setup` 开始运行 Slackware Linux 的安装向导。
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-114102.png)
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-114116.png)
安装向导将会按照如下列表的步骤依次进行，我们将从设置交换分区开始。
#### 设置交换分区
将选择光标移动至 "ADDSWAP"
选择我们刚刚分区好的交换分区
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-114128.png)
这一步将会开始检查交换分区的坏块，需要一分钟的时间，你也可以选择 "No" 跳过检查
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-114152.png)
检查完毕后将自动配置交换分区，并自动写入到系统的 `/etc/fstab` 中
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-114217.png)
#### 选择系统安装分区
选择我们刚刚分区好的系统盘
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-114517.png)
这里他会询问你是否需要格式化该分区，三个选项分别是“快速格式化（不进行坏块检查）”、“格式化（进行坏块检查）”、“不进行格式化”，由于我们刚刚已经进行过格式化分区的步骤，所以我们可以直接选择 “No” 跳过格式化的步骤。
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-114547.png)
确认好系统安装分区后，也会自动选择安装的EFI分区**（仅UEFI引导会有此步骤）**
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-114715.png)
#### 选择使用的安装源
这里一共又七个选项，最主要的三个选项是
1、从 CD 或 DVD 安装
2、从U盘进行安装
3、从HTTP服务器进行安装（即网络安装）
这里因为我使用的是虚拟机，所以选择第一个，如果你在实体机上安装应该选择第二个~~（2025年了不会真的有人特地为了装一个Linux拿一张DVD刻ISO吧）~~，当然你也可以使用网络安装。
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-114802.png)
#### 选择要安装的包（按系列）
这一步可以自由选择需要安装的包，你可以通过按下空格键取消或勾选你需要或不需要的包以丰富或精简你的系统。
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-115012.png)
#### 选择提示模式
这一步推荐直接选择第一项 "full" （完整安装模式）
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-115201.png)
接下来就是等待安装完毕。
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-115332.png)
#### 安装完毕后的部分设置
安装完毕后会询问你要不要使用一个U盘作为启动盘（就是通过U盘直接引导进硬盘里的系统），我们这里选择Skip
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-123903.png)
如果你在使用UEFI引导的话，他会告诉你[LILO](https://zh.wikipedia.org/wiki/LILO)（一种非常远古的Linux的引导启动工具，在GRUB出现之前都是使用这个）已经不能在UEFI环境下使用，会询问你是要跳过LILO的安装并安装ELILO还是仍然安装LILO
我们这里选择跳过LILO的安装
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-123942.png)
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-124306.png)
接着会询问你是否要安装引导入口（像是安装一些Linux发行版会在你的BIOS界面留下"grub"的引导选项这样），并特别标注**苹果电脑不要安装**
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-124317.png)
接着会询问你是否安装GPM，GPM的作用是你在纯文本界面的时候仍然可以使用鼠标进行选择文本的操作
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-124646.png)
#### 配置你的网络
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-124754.png)
设置主机名
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-124816.png)
设置一个域名给这个主机
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-124842.png)
接着会询问你是否要设置VLAN ID，一般家庭网络都不需要，我们选择No
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-124903.png)
网络管理器推荐选择使用NetworkManager
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-124949.png)
配置完毕后选择"Yes"保存设置
#### 确认开机自启的服务
一般保持默认即可，可按照自己的需求设置。
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-125018.png)
#### 终端字体自定义
这一步骤可以设置终端的字体个性化设置，我这里不做自定义，选择"No"
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-125140.png)
#### 硬件时间设置
这里他会询问你的BIOS时间是否设置到了[协调世界时](https://zh.wikipedia.org/zh-cn/%E5%8D%8F%E8%B0%83%E4%B8%96%E7%95%8C%E6%97%B6)，一般来说我们只需要保持本地时区的时间设置，选择"No"
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-125238.png)
#### 设置时区
选择 "Asia/Shanghai"
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-125433.png)
#### 设置默认的文本编辑器
我这边习惯使用vim，可按照你自己的习惯选择，要注意elvis不支持UTF-8
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-125509.png)
#### 设置X的默认窗口管理器
这一步它可以帮你自动写好xinitrc。
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-125559.png)
因为我这里安装了KDE桌面，所以选择第一个，其他的可以按照你自己的需求。
#### 设置root密码
接下来我们需要设置root密码，如果不设置密码那么root用户将被禁用。
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-125701.png)
注意，输入密码时不会显示任何字符
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-125748.png)
#### 完成安装向导，重启电脑
在完成配置向导后会返回到主菜单，选择Exit退出，推出你的安装介质（U盘），重启电脑。
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-125823.png)
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-125837.png)
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-125848.png)
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-125936.png)
# 进行基础配置
完成安装并重启电脑后，使用root用户登录
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-130604.png)
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

## 启动桌面环境
登录到你的新用户并输入 `startx` 启动KDE桌面环境
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-131315.png)
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-131343.png)
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-131354.png)
# 进一步设置以方便日用

## 设置中文
在启动KDE成功后打开系统设置并设置为中文
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-131534.png)

### 解除安装中文字型
<div class="info">
以下内容引用 [ltlnx](https://ltlnx.tw/slackware-chinese-install-guide.html)
</div>

Slackware 預裝的中文字型有好幾款，其中包括時下最流行的免費中文字型——思源黑體與思源明體。但在這兩套高品質開源中文字型出現之前，Linux 使用者們想顯示中文，除了從 Windows 或 Mac OS 複製字型來用（非法），只有幾種選擇：

- 文鼎的四套開源字型，兩套繁體兩套簡體；有明體跟楷體可以使用。
- CwTeX 系列字型。（後來爆出是由文鼎字型描邊重製而成，遊走法律邊緣）
- 文泉驛的兩套字型：文泉驛正黑，與文泉驛微米黑。

Slackware 在思源系列字體推出之前就已經打滾多年，因此包含當時最沒有版權爭議的中文顯示字體——文泉驛正黑，也是再正常不過的事。不過，文泉驛正黑因為是由部件組字的關係，字型品質其實不怎麼好；既然有了思源黑體，就可以讓文泉驛正黑功成身退了。要解除安裝，直接打開一個新的終端機視窗，先輸入

```bash
sudo su
```

並輸入密碼以切換到 root 帳號。再來執行：

```bash
slackpkg remove wqy-zenhei-font-ttf
```

就可以將其移除。Slackware 系統中同時存在一套品質也不怎麼好的日文字型，這邊一併移除：

```bash
slackpkg remove sazanami-fonts-ttf
```

最後為了防止它們在安裝更新時一起被裝回來，可以用你習慣的文字編輯器開啟 `/etc/slackpkg/blacklist` 文字檔，並在檔案末端加入

```bash
wqy-zenhei-font-ttf
sazanami-fonts-ttf
```

如此一來，重開機之後，介面的中文字應該都是以思源黑體顯示。

再來在安裝軟體、輸入法之前，我們需要先設定第三方軟體源。

## 安装和使用Fcitx 5
现阶段 Slackware -current 已默认使用最新的 Fcitx 5（而稳定版本仍在使用老版本的 Fcitx），所以不用太折腾安装输入法的事情，直接用Fcitx 5的拼音就好了。
![](./img/Slackware-Chinese-Install-Guide/QQ20251001-131704.png)
即使使用了最新的 Fcitx 5，但是X11的环境变量其实是没有设置好的，所以需要我们进一步地进行设置

编辑 `.xprofile`

```bash
vim ~/.xprofile
```

添加下面这几行：

```
export LANG=zh_CN.UTF-8
export LC_ALL=zh_CN.UTF-8
export GTK_IM_MODULE=fcitx # 带有fcitx字样的是为了能让Fcitx5输入法能正常使用
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx
export SDL_IM_MODULE=fcitx
```

## 設定第三方套件庫
<div class="info">
以下内容引用 [ltlnx](https://ltlnx.tw/slackware-chinese-install-guide.html)
</div>

我們剛剛已經用過了 Slackware 內建的套件管理程式—— slackpkg，但 slackpkg 並不支援直接下載第三方套件，只能安裝從其他來源下載的套件。好在，一群 Slackware 開發者維護了一個類似 Arch Linux 的 AUR、拿來放編譯套件腳本的地方：[slackbuilds.org](https://slackbuilds.org/)。簡單來說，套件腳本會做的事就是使用原始碼壓縮檔（tarball），用特定的方式產生安裝套件。而許多人也開發了與這個套件庫互動的程式，如 [sbopkg](https://sbopkg.org/)、[sbotools](https://pink-mist.github.io/sbotools/) 或 [sboui](https://github.com/montagdude/sboui)。我自己使用的是 sboui，因此接下來會示範如何安裝與設定 sboui。

### 安裝

- 首先，先到 slackbuilds.org 搜尋 sboui。我們會到達這個頁面：

slackbuilds.org 上的 sboui 頁面

我們捲動到下面，點擊 Source Downloads 跟 Download SlackBuild 下面的連結來下載原始碼檔案，分別是 sboui-<版本>.tar.gz 跟 sboui.tar.gz。在最下面可以看到 This requires: libconfig，這代表我們需要下載另一個套件—— libconfig 的腳本。點一下連結，你會被帶到 libconfig 的下載頁面：

slackbuilds.org 上的 libconfig 頁面

在這裡，我們一樣捲動到下面，點擊 Source Downloads 跟 Download SlackBuild 下面的連結，分別是 libconfig-<版本>.tar.gz 跟 libconfig.tar.gz。

- 下載完之後，先解壓縮 sboui.tar.gz 跟 libconfig.tar.gz。要確定他們是解壓縮到各自的資料夾，而不是當前的資料夾。
- 解壓縮完後，將 sboui-<版本>.tar.gz 移動到 sboui 資料夾內，再把 libconfig-<版本>.tar.gz 移到 libconfig 資料夾內。上述動作做完之後，Downloads 資料夾應該會長這樣：（在我寫這篇文章的時候，libconfig 的版本是 1.7.2，sboui 的版本是 2.3）

```bash
$ tree ~/Downloads
/home/ltlnx/Downloads/
├── libconfig
│   ├── README
│   ├── libconfig-1.7.2.tar.gz
│   ├── libconfig.SlackBuild
│   ├── libconfig.info
│   ├── remove_scanner.patch.gz
│   └── slack-desc
└── sboui
    ├── README
    ├── doinst.sh
    ├── sboui-2.3.tar.gz
    ├── sboui.SlackBuild
    ├── sboui.info
    └── slack-desc
```

- 再來，先到 libconfig 資料夾內（cd libconfig），執行以下指令：

```bash
$ chmod +x libconfig.SlackBuild
$ su  # 切換到 root 帳號，如果有設定 sudo 也可以執行 sudo su
# ./libconfig.SlackBuild
```

到這邊，libconfig 就會開始編譯，跑完之後在 /tmp 資料夾內會有包裝好的套件。
- 再來安裝剛包裝好的套件：

```bash
installpkg /tmp/libconfig-1.7.2-x86_64-2_SBo.tgz
```

你看到的檔案名稱很可能會跟我的不同，但開頭一定是 libconfig。可以在打完 /tmp/libconfig 後按一下 Tab 鍵以自動完成。

- 最後，用一樣的方式安裝 sboui：

```bash
$ dirs
~/Downloads/libconfig
$ cd ../sboui
$ chmod +x sboui.SlackBuild
$ su
# ./sboui.SlackBuild
# installpkg /tmp/sboui-2.3-x86_64-1_SBo.tgz
```

完成之後，可以試試看以 root 帳號在終端機執行 sboui --version：

```bash
# sboui --version
sboui 2.3
Copyright (C) 2016-2022 Daniel Prosser
Expat/MIT License: https://opensource.org/licenses/MIT
This is free software; you are free to change it and redistribute it.
This software is presented 'as is', without warranty of any kind.
```

## 通讯软件
没时间写了，等待更新吧……（2025年10月7日）
