---
layout: post
title: "Linux笔记"
date: 2021-05-10
tags: ["OS"]
comments: true
toc: true
author: oneman233
excerpt: "也许是更好的操作系统"
---

# 开机自启动脚本

[参考](https://www.jianshu.com/p/1a160067d8fd)

# 开启SSH

树莓派的SSH开启方式可以参考[树莓派使用指南](https://blog.mynameisdhr.com/ShuMeiPaiShiYongZhiNan/)

Ubuntu虚拟机开启SSH主要有以下几步：

1. 安装ssh-server
    `sudo apt-get install openssh-server`
2. 查看已安装的服务
    `dpkg -l | grep ssh`
3. 确认ssh-server是否已经启动
    `dpkg -l | grep ssh`
4. 如果输出结果中有`sshd`说明已经启动成功

启动和关闭ssh服务可以使用以下两条命令：

```
sudo /etc/init.d/ssh stop
sudo /etc/init.d/ssh start
```

ssh-server的配置文件位于`/etc/ssh/sshd_config`，此处可以定义SSH的服务端口（默认为22）

# 连接SSH

用`ifconfig`命令可以找到当前网络设备的ip地址

# 压缩文件

主要有两种压缩方式：

## tar

使用命令：`tar -zcvf /home/test.tar.gz /test`

以上命令可以把当前位置的`/test`目录压缩为`test.tar.gz`后存放到`/home`目录下

## zip

安装zip可以使用命令：`yum install zip`

基本命令：`zip -r ./test.zip ./test`

可选参数：

1. -r：递归压缩路径
2. -m：文件压缩后删除源文件
3. -q：压缩时不显示指令执行过程
4. -rP：加密压缩，直接在命令行中指定密码（`zip -r -rP password ./test.zip ./test`）

解压时可以直接使用`unzip test.zip`命令将`test.zip`文件解压到当前目录，需要密码时会提示输入密码

在命令行中指定解压密码：`unzip -P password test.zip`

# 动态库的问题

[做树莓派的时候](https://blog.mynameisdhr.com/PythonDeCtypesShiYong/)因为用到了ctypes，需要预先编译好C函数的so动态库，但是把动态库从树莓派移动到Ubuntu虚拟机后报错：`wrong ELF class: ELFCLASS32`

在[StackOverflow上找到了一个答案](https://stackoverflow.com/questions/6172105/wrong-elf-class-elfclass32)，说当前JVM是64-bit，而运行的程序是32-bit

恍然大悟，在Ubuntu的64位系统下重新打包一遍动态库，问题解决

# 等待缓存锁问题

强制删除锁命令：

```
sudo rm /var/lib/dpkg/lock-frontend
sudo rm /var/cache/apt/archives/lock
sudo rm /var/lib/dpkg/lock
```

主要是因为Linux更新时没有镜像导致速度很慢，意外下载失败后第二次下载时缓存就会被锁住

# 命令大全

[参考](https://www.runoob.com/linux/linux-command-manual.html)