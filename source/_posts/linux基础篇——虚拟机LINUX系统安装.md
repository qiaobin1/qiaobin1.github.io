---
title: linux基础篇——虚拟机LINUX系统安装
date: 2023-03-23 09:25:00
author: 乔彬
hide: false
summary: linux基础篇——虚拟机LINUX系统安装
categories: 嵌入式
tags:
  - linux
  - 嵌入式开发
---


## 一、系统分区分区
### 1、挂载
- 必须分区
  - /（根分区）
  - swap分区（交换分区——暂存，内存2倍，不超过2GB)
- 推荐分区
  - /boot（启动分区，200mb） 
### 2、文件系统结构
![文件系统结构](https://img-blog.csdnimg.cn/052d1c2231f94416903f8c5ca1ede8be.png)
### 3、总结
- 分区：把大硬盘分为小的逻辑硬盘
- 格式化：清空文件；写入文件系统
- 分区设备文件名：给每个分区定义设备文件名
- 挂载：给每个分区分配挂载点（必须是空目录）
## 二、网络连接的三种模式
![网络连接的三种模式](https://img-blog.csdnimg.cn/4af95475d0b04b0e9d77713cb79f34a9.png)
- 1、桥接模式
虚拟系统可以和外部通讯，但是容易造成ip冲突；
虚拟机自动生成与主机IP同一网段的地址用于通讯；
**原因**：以192.168.0网段最多255个，虚拟系统linux网段与外部一致；可以互相通讯；
- 2、NAT模式
网络地址转换模式，虚拟系统可以和外部系统通讯，不造成IP冲突；
**原理**：虚拟系统生成自己的IP地址（例如：192.168.100.88）的同时，在实际系统生成同一网段的ip地址（例如：192.168.100.99）用于通讯，并将实际系统的IP地址作为代理，用于与外部系统通讯；
- 3、主机模式：独立的系统；
与主机共享专用网络；
## 三、真实主机与虚拟机的关系
- centos7.6:   https://vault.centos.org/7.6.1810/isos/x86_64/
- vm15.5.1:    https://www.nocmd.com/windows/740.html
![真实主机与虚拟机的关系](https://img-blog.csdnimg.cn/0b71efc3b42a44f59b17e6600ee020b2.png)
## 四、虚拟机克隆、快照、迁移与删除
### 1、克隆
- 方式1，直接拷贝一份安装好的虚拟机文件
- 方式2，使用vmware的克隆操作
（注意，克隆时，需要先关闭linux系统）
### 2、快照
若使用虚拟机系统时，想回到原先的一个状态，就需要提前创建一个快照；
步骤：
- 安装好系统后，先做一个拍摄快照A
- 系统出故障之后，在快照管理里快速恢复到快照A
### 3、迁移
- 虚拟机系统安装好；它的本质是文件；
- 虚拟机的迁移——将安装好的虚拟机系统文件夹整体拷贝到另外位置；
### 4、删除
1. 用vmware移除；
2. 点击菜单从磁盘中删除**或者**手动删除虚拟系统对应文件夹
## 五、安装vmtools
- 可以在windows下更好的管理vm虚拟机
- 可以设置windows和centos的共享文件夹
![共享文件原理图](https://img-blog.csdnimg.cn/e10e0cb110334615aa1c442e22ede771.png)
### 5.1安装vmtools的步骤
1. 启动centos7虚拟机（以管理员权限打开）
2. 点击vm菜单的虚拟机->install vmware tools
3. centos虚拟机桌面会出现vmware tools 
4. 打开vmware tools 出现一个xx.tar.gz的安装包
5. 复制xx.tar.gz，将其复制到opt文件夹
（主文件夹—>其他位置->计算机->opt文件夹）
6. 从终端进入到opt目录
    即，在终端输入 cd /opt/
7. 在终端opt目录下解压
    即tar -zxvf xx.tar.gz(打入前两个字符，再使用tab键即可自动补充复杂的文件名）
 8. 进入到解压后的目录
      cd xx/（tab自动补充文件名）
  9. 利用ls可以查看所在目录下的文件
  10. 安装
      使用命令./vmware-install.pl(文件名.pl)
      然后点击一系列回车予以确认，（即全部使用默认设置）
   11. 注意：安装vmtools需要有**gcc**  
      如何判断是否安装gcc，在终端输入gcc -v 
   ###  5.2 设置共享文件夹
![设置共享文件夹](https://img-blog.csdnimg.cn/9c43ed9f28974ce7b3edfe3ddc55a3c6.png)
## 六、Linux的目录结构
- 基本介绍
1. linux的文件系统采用层级式树状结构，在结构中最上层是根目录“/”，然后在此目录下再创建其他的目录。

2. 记住经典的话：在linux世界里，一切皆文件
![linux目录结构](https://img-blog.csdnimg.cn/41322e9f47ea4f44b972af591a8aa6eb.png)
3. Linux目录结构是规定好的：
![具体目录结构1](https://img-blog.csdnimg.cn/cdbd89bd1f3c408790f3a9b53962a67b.png)
![具体目录结构2](https://img-blog.csdnimg.cn/68623a65f29447b0a20d4927c5a9c306.png)
![具体目录结构3](https://img-blog.csdnimg.cn/f961d13f1aa94fe0ab4b69ea004df1c5.png)
![具体目录结构4](https://img-blog.csdnimg.cn/ef8d8b4f147a43a9b18b107fd5b5a488.png)
-  基础命令
增添用户命令：useradd jack(用户名)
删除用户指令：userdel -r jack(用户名）
显示命令：ls（显示该文件夹下有什么文件）
查看linux虚拟系统IP：ifconfig
用 ping 192.168.2.129（虚拟系统的ip地址）
作用：用来判断虚拟系统ip与真实系统ip是否可以联通