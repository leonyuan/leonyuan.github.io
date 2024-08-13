---
title: 内部基于Chromium开源项目定制开发
updated: 2024-08-11 16:43:10Z
created: 2024-07-11 08:13:40Z
latitude: 27.66032060
longitude: 113.88308060
altitude: 0.0000
---

# 内部基于Chromium开源项目定制开发

[内部基于Chromium开源项目定制开发.pdf](../_resources/内部基于Chromium开源项目定制开发.pdf)

## 一、准备环境

### 安装Slackware64 15.0
1. **下载Slackware Linux**：
   - 从国内的开源镜像站下载Slackware64 15.0 ISO镜像文件。比如这里从阿里云镜像站下载：https://mirrors.aliyun.com/slackware/slackware-iso/slackware64-15.0-iso/slackware64-15.0-install-dvd.iso

2. **创建Slackware安装介质**：
   - 将ISO镜像文件写入USB闪存盘或DVD。可以使用工具如Rufus（适用于Windows）或Etcher。

3. **调整磁盘分区**：
   - 在Windows中使用磁盘管理工具（Disk Management）调整磁盘分区，为Slackware Linux腾出足够的未分配空间。

4. **禁用快速启动**（如果启用了快速启动）：

   - 在Windows中，进入控制面板 -> 电源选项 -> 选择电源按钮的功能 -> 更改当前不可用的设置 -> 取消选择“启用快速启动”。

5. **启动电脑进入BIOS/UEFI设置**：
   - 重启电脑并进入BIOS/UEFI设置（通常通过按下启动时显示的特定按键，如F2、F10、DEL等）。
   - 在启动顺序中，将USB闪存盘或DVD设置为第一启动设备。

6. **启动Slackware安装程序**：
   - 保存并退出BIOS/UEFI设置，电脑会从USB闪存盘或DVD启动进入Slackware安装程序。

7. **安装Slackware**：
   - 按照Slackware安装向导的指示进行操作，选择目标分区、格式化成Linux分区、选择安装全部包、安装LILO引导程序到MBR，并完成安装。

###  获取Chromium源码

1. **下载已打包好的Chromium源码包，下载地址请从项目Leader处获取。**：

   ```sh
   $ tar xf chromium-ungoogled-126.0.6478.114.tar.gz
   $ cd chromium-ungoogled
   $ tree -L 2
   .
   ├── build
   │   ├── README
   │   ├── chromium-make.sh
   │   ├── chromium-ungoogled.appdata.xml
   │   ├── chromium-ungoogled.png
   │   ├── chromium.SlackBuild
   │   ├── chromium.default
   │   ├── chromium.sh
   │   ├── patches
   │   ├── slack-desc
   │   └── widevine-versions.txt
   └── src
       └── chromium-126.0.6478.114
   ```


## 二、定制和编译Chromium

1. **定制Chromium**：

   Chromium项目源代码位于目录：`src/chromium-126.0.6478.114`，找到相应文件进行相应逻辑功能开发。

   - 修改源码进行定制，例如更改UI、增加功能等。
   - 定制可以包括修改HTML、CSS、JavaScript文件，以及C++源文件。

2. **编译Chromium**：

   执行`build/chromium-make.sh`脚本编译：

 ```sh
   $ pwd
   /data/chromium-ungoogled/build
   $ ./chromium-make.sh
   Building ...
   -- Compiling the lot.
   -- Using custom clang.
   ninja: Entering directory `out/Release'
```


## 三、测试和打包

1. **运行测试**：

   - 在构建完成后，可以运行定制的Chromium浏览器进行测试：

```sh
   $ pwd
   /data/chromium-ungoogled/src/chromium-126.0.6478.114
   $ ./out/Release/chrome
```

2. **打包浏览器**：

   - 创建Slackware包的工具可以是`makepkg`。假设您已经在`out/Release`目录下有一个构建好的二进制文件，直接执行`build/chromium-makepkg.sh`将其打包：

```sh
  $ pwd
  /data/chromium-ungoogled/build
  $ ./chromium-makepkg.sh
```

     这会创建一个Slackware包`chromium-ungoogled-126.0.6478.114-x86_64-1.txz`，您可以使用`installpkg`进行安装：

 ```sh
  $ sudo installpkg chromium-ungoogled-126.0.6478.114-x86_64-1.txz
```

