---
title: linux_实操篇
date: 2023-03-23 09:25:00
author: 乔彬
top: true
hide: false
cover: true
summary: Linux远程登录，vi与vim编辑器，inux的开机、重启与用户注销，用户管理
categories: 嵌入式
tags:
  - linux
  - vim
---

@[toc](linux_实操篇]
## 六、Linux远程登录

### 6.1为什么linux要远程登录

![为什么linux要远程登录](https://img-blog.csdnimg.cn/6c33daa9ad544defb877426e469847a2.png)
![登录客户端](https://img-blog.csdnimg.cn/e58eea25cd3943a0aaebe736b4f4c732.png)

### 6.2远程登录linux-Xshell7
![远程登录linux](https://img-blog.csdnimg.cn/148d98df3a834b4d81495a990e72c058.png)
下载地址：
 https://www.xshell.com/zh/free-for-home-school/
![点击xshell新建](https://img-blog.csdnimg.cn/d5f6bb2ca800433a85e0ba5af83d9ff1.png)
### 6.3远程上传下载文件-Xftp7
![远程上传下载文件](https://img-blog.csdnimg.cn/1da5dbfa5a3248a1818b7f24a17707e9.png)
![点击Xftp新建](https://img-blog.csdnimg.cn/08e901ea88ba4dab9972d9f537bb9fba.png)
#### 6.3.1解决中文乱码
点击属性——>选项——>编码方式改变
## 七、vi与vim编辑器
### 7.1vim快速入门
#### 7.1.1基本介绍
![vi与vim基本介绍](https://img-blog.csdnimg.cn/893e2b71f3b54c8da64569d69d21f4e2.png)
#### 7.1.2 vi与vim常用三种模式
![vi与vim常用三种模式](https://img-blog.csdnimg.cn/2e4b0cbf33724e0f81ea8f4a822f4389.png)
#### 7.1.3vi和vim基本使用
![在这里插入图片描述](https://img-blog.csdnimg.cn/82ee46be844646fc8c30571bb5caeebb.png)
### 7.2vi和vim快捷键
~~~ linux
正常模式下:
 1.复制：5yy
 2.粘贴：p
 3.删除：5dd
 4.撤销：u
 5.到达文件中某一行：1G
命令行模式下：
 1.查找某个单词：/关键字,回车
 2.查找下一个：n
 3.设置行号：set nu
 4.取消行号：set nonu
~~~
#### 7.2.1 快捷键练习
![各种模式切换](https://img-blog.csdnimg.cn/11b5864bda644148badb62e482a63cda.png)

1、使用xshell远程登录
2、登陆后在终端输入vim Hello.java——>进入正常模式
3、使用**i**——>进入编辑模式，写入代码
4、Esc退出编辑模式
5、使用:——>进入命令行模式
6、使用wq（写入并退出）
i
正常模式下
- 拷贝当前行——yy
  拷贝当前行向下5行 ——5yy
  粘贴——p
- 删除当前行——dd
  删除当前行向下5行——5dd
- 编辑/etc/profile 文件
   到达该文档的最末行——G
   到达该文档的第20行——20+G
   到达该文档的最首行——gg
- 撤销当前动作——u

命令行模式下——:
- 查找某个单词——/关键字，回车查找。
   查找下一个——输入n。
 - 设置文件行号——set nu
    取消文件行号——set nonu
## 八、linux的开机、重启与用户注销

~~~ linux
关机：
shutdown -h now
shutdown -h 1
halt
重启：
shutdown -r now
reboot
~~~
 ### 8.1关机与重启的命令
![关机与重启](https://img-blog.csdnimg.cn/9f74ecbb5f164bf8b76fa8801c2f05c0.png)
**细节**：不管重启还是关机，首先运行sync命令，把内存中的数据写入到磁盘；
- **关机**
 shutdown -h now ——立即关机
 shutdown -h 1——1分钟后关机（shutdown)
 halt——关机(**最高权限者关机才能用**）
 - **重启**
 shutdown -r now——立即重启
 reboot——立即重启
 - 撤销关机/重启命令——shutdown -c
 - 把内存数据同步到磁盘——sync
  ### 8.2用户登录与注销
  ![用户登录与注销](https://img-blog.csdnimg.cn/31bad7091beb44a1b2885dbb255180e3.png)

## 九、用户管理

~~~ linux
用户添加：useradd [-d 指定目录] [-g 用户组] 用户名
指定密码：passwd 用户名
删除用户: userdel [-r] 用户名
查询用户信息： id 用户名
切换用户：su - 用户名
注销用户：logout
查看当前登录用户信息：whoami
用户组添加：groupadd 组名
用户组删除：groupdel 组名 
~~~
 ### 9.1用户
 - 用户添加
 useradd 用户名——默认用户的家目录在/home/用户名
 useradd -d 指定目录 新的用户名
    useradd -g 用户组 用户名
  - 指定密码
    passwd  用户名
  - 删除用户
    userdel 用户名——保留家目录
    userdel -r 用户名——用户及家目录均被删除
- 查询用户信息指令
id 用户名
- 切换用户
su - 切换用户名
注：权限高——权限低（不需要密码），反之需要
       当返回原来用户时，使用logout（注销）指令
 - 查看当前登录用户信息
 whoami
 - 修改用户的组
 usermod -g 用户组 用户名

 ### 9.2用户组
 系统可以对有共同权限的多个用户进行管理
 - 新增组
 groupadd 组名
 - 删除组
 groupdel 组名
 ### 9.3 用户和组相关文件
 - /etc/passwd 文件（用户配置文件）
 每行含义：用户名:口令:用户标识号:组标识号:注释性描述:主目录:登录Shell
  - /etc/shadow 文件（口令配置文件）
 每行含义：登录名:加密口令:最后一次修改时间:最小时间间隔:最大时间间隔:警告时间:不活动时间：失效时间：标志
  - /etc/group 文件（组配置文件）
 每行含义：用户名:口令:组标识号:组内用户列表
 ## 十、运行级别
 ### 10.1基本介绍
~~~ linux
init 0-6 切换运行级别
设置默认运行级别为3:systemctl set-default multi-user.target
设置默认运行级别为5:systemctl set-default graphical.target
~~~
- 运行级别说明：
0：关机
1：单用户（找回丢失密码）
2：多用户状态没有网络服务
3：多用户状态有网络服务
4：系统未使用保留给用户
5：图形界面
6：系统重启
- 切换运行级别——init 级别号（3）
- 查看当前运行级别——systemctl get-default
- 设置默认运行级别为3——systemctl set-default multi-user.target
- 设置默认运行级别为5——systemctl set-default graphical.target

### 10.2找回root密码
1. 启动系统，在进入开机界面，在界面中按“e”进入编辑界面
2. 进入编辑界面，使用光标，找到以“Linux16开头的行”，在行末输入：init=/bin/sh(进入单用户模式）
3. 输入完成后，按快捷键"ctrl+x",进入单用户模式
4. 在光标闪烁位置输入mount -o remount,rw /
5. 在新的一行最后输入：passwd,回车确认
6. 输入两次新的密码
7. 接着，在鼠标闪烁位置输入： touch /.autorelabel 回车确认
8. 最后在光标闪烁位置输入 exec  (空格)/sbin/init  回车确认
9. 直到系统自动重启
## 十一、帮助指令
1. man——获得帮助信息
基本语法：man [命令或配置文件]
案例：查看ls命令的帮助信息
注：在linux下，隐藏文件是以.开头，选项可以组合使用，比如：ls -al;ls -al root
2. help——获得shell内置命令帮助信息
基本语法：help命令
3. 百度——菜鸟教程
## 十二、文件目录命令
~~~ linux
pwd——显示当前工作目录的绝对路径
ls -al——显示当前目录下所有文件和目录
cd cd..——切换目录
mkdir -p——创建目录
rmdir——删除空目录
touch——创建新文件
\cp -r——复制文件或文件夹
rm -rf——移除文件或文件夹
mv——移动文件或重命名
~~~
1. **显示当前工作目录的绝对路径**
语法：pwd
2. **显示当前目录下所有文件和目录**
语法：ls [选项]  [目录和文件]
选项:
 -a 显示当前所有文件（包含隐藏文件夹）
 -l 以列表方式显示信息
 3. **切换目录**
 语法：cd [参数]
 参数：
 cd 或者cd ~   ——回到自己的家目录
 cd .. ——返回上一级目录
 4. **创建目录**
 语法：mkdir [选项] [目录]
 选项：-p 创建多级目录
 案例：
 创建一个目录 mkdir /home/dog
 创建多级目录 mkdir -p /home/animal/dog
 5. **删除空目录**
 语法：rmdir [选项] [目录]
 案例：rmdir /home/animal
 6. **创建空文件夹**
 语法：touch 文件名称
 7. **拷贝文件到指定目录**
 语法：cp [选项] source dest
 常用选项：
 -r : 递归复制整个文件夹
 案例：
 拷贝文件：cp /home/hello.txt /root
 拷贝整个文件夹：cp -r /home/tom /root
 强制覆盖不提示：\cp -r /home/tom /root
 8. **移除文件或目录**
 语法：rm [选项] 文件、目录路径
 常用选项：
 -r : 递归删除整个文件夹
 -f : 强制删除不提示
 案例：
 删除文件——rm /home/hello.txt
 删除文件夹——rm -rf  /home/tom
 9. **移动文件与目录或重命名**
 语法：
 mv oldFile NewFile(重命名）
 mv /temp/moveFile /targetFolder (移动文件）

 