---
title:  Quantum Espresso Tutorial
date: 2026-06-22 16:30:00 +0800
categories: [Research]
tags: [QE, DFT, AIMD]
---

# Quantum Espresso 使用教程 ——by Silong Quan

## 1 登陆服务器 (如在本地计算可跳过此步)
- 下载安装[Xshell](https://cdn.netsarang.net/v8/Xshell-latest-p)和[Xftp](https://cdn.netsarang.net/v8/Xftp-latest-p)；
- 打开 Xshell，点击「新建」，点击「连接」填写主机地址、端口号等信息；再点击「用户身份验证」输入用户名与密码，完成后点击「确定」;
![主题](/assets/image/2026-06-22/1.png)

- 点击「打开」，选中刚刚新建的连接会话，点击「连接」登录服务器;
![主题](/assets/image/2026-06-22/2.png)

- 点击工具栏Xftp图标启动Xftp文件传输工具，窗口左侧为本地文件目录，右侧为服务器文件目录。
![主题](/assets/image/2026-06-22/3.png)

## 2 新建个人文件夹和工作目录
登陆服务器后，先新建自己的`个人文件夹`，以后所有的操作和计算在新建的个人文件夹中进行，如我以自己的姓名拼音新建个人文件夹「quansilong/」。新建个人文件夹后，进入`个人文件夹`（如「quansilong/」）. 
```console #bash
msmcquan@inspur-NF5468M5:~$ mkdir quansilong
msmcquan@inspur-NF5468M5:~$ ll
total 4
drwxr-xr-x 3 msmcquan quansilong 4096 6月  15 10:39 quansilong/
msmcquan@inspur-NF5468M5:~$ cd quansilong/
msmcquan@inspur-NF5468M5:~/quansilong$ pwd
/home/msmcquan/quansilong
```

在`个人文件夹`（如「quansilong/」）中新建`工作目录`（如「LiFePO4-c-010-QE/」），并进入`工作目录`「LiFePO4-c-010-QE/。此教程将以计算 $LiFePO_4$的电子结构为例。
```console #bash
msmcquan@inspur-NF5468M5:~/quansilong$ mkdir LiFePO4-c-010-QE
msmcquan@inspur-NF5468M5:~/quansilong$ cd LiFePO4-c-010-QE/
msmcquan@inspur-NF5468M5:~/quansilong/LiFePO4-c-010-QE$ pwd
/home/msmcquan/quansilong/LiFePO4-c-010-QE
```

## 3 准备队列系统提交脚本

<font size="4" color="red"><b>这是红色加粗的大号字体</b></font>
