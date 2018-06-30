---
title: CentOS 6.9 安装gnome，使用VNC访问远程桌面
description: 介绍了在 CentOS 6.9 上安装 gnome 的步骤，以及使用 VNC Client 登录远程桌面。
categories: Linux
tags:
  - CentOS
  - 远程桌面
  - gnome
  - VNC
date: 2018-06-30 18:18:01
updated: 2018-06-30 18:18:01
---


要通过远程桌面访问最小安装的`CentOS`，需要先在服务端安装`gnome`和`VNC Server`，然后通过`VNC Client`登录远程桌面。

# 服务器端

## 安装 gnome 图形化桌面
1. 安装 gnome
```shell
$ sudo yum groupinstall -y "X Window System" "Desktop" "Chinese Support"
```

2. 设置开机启动『重启后生效』
编辑 vim /etc/inittab
```
将    id:3:initdefault:
改为  id:5:initdefault:
```

## 安装 VNCServer

1. 安装 tigerVNC
```shell
$ sudo yum -y install vnc *vnc-server*
```

2. 修改VNCServer主配置文件
```shell
$ sudo vim /etc/sysconfig/vncservers
```

复制最后两行并去掉行首注释符，修改用户名
```shell
$ VNCSERVERS="1:user-name"
$ VNCSERVERARGS[1]="-geometry 1366x768"
```

3. 设置VNCServer远程连接密码
```shell
$ vncpasswd
```

4. 启动vncserver服务
```shell
$ sudo service vncserver start
```

5. 启动服务后，会在第3步中的用户目录下生成`.vnc/xstartup`文件，编辑该文件
```shell
$ vim /root/.vnc/xstartup
``` 
将最后一行改为
```shell
gnome &
```

6. 重启vncserver服务
```shell
$ sudo service vncserver restart
```

7. 开机启动『可选』
```shell
$ chkconfig --level 345 vncserver on
```

备注：
如果是在AWS、阿里云等云主机上的话，需要配置安全组。vncserver的默认端口号是`5901`。

# 客户端
使用 [VNC-Viewer](https://www.realvnc.com/en/connect/download/viewer/) 客户端连接到远程桌面，在VNC Server一栏输入：
IP:1
xxx.xxx.xxx.xxx:1

输入1.3步中设置的密码，即可登录。

# 附加，安装Chromium的步骤
如果直接在`Google`官网下载[Chrome](https://www.google.com/chrome/)，安装`rpm`包的话，会报一些依赖没安装的错误。

这里介绍更简单的使用`yum`安装`Chromium`的方法。

1. 安装 NCSU ITECS 仓库 (64-bit only)
``` shell
$ sudo yum localinstall http://install.linux.ncsu.edu/pub/yum/itecs/public/chromium/rhel6/noarch/chromium-release-1.1-1.noarch.rpm
```

2. 安装/启用 hughesjr Chromium EL 6 repository (32-bit and 64-bit)
『只能安装旧版本』
```shell
$ sudo cd /etc/yum.repos.d
$ sudo wget http://people.centos.org/hughesjr/chromium/6/chromium-el6.repo
```

3. 安装 Chromium Browser on CentOS 6 / RHEL 6
```shell
$ sudo yum install chromium
```

# 总结：
本文介绍了在 CentOS 6.9 上安装 gnome 的步骤，以及使用 VNC Client 登录远程桌面。
最后附加了安装Chromium的步骤。

参考：
1. [https://blog.csdn.net/qq_34754363/article/details/72715341](https://blog.csdn.net/qq_34754363/article/details/72715341)
2. [https://www.if-not-true-then-false.com/2013/install-chromium-on-centos-red-hat-rhel/](https://www.if-not-true-then-false.com/2013/install-chromium-on-centos-red-hat-rhel/)