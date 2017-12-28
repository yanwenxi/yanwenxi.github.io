---
title: Windows下Nginx的启动、停止等命令
date: 2017-04-28 16:50:18
tags: nginx
---
在Windows下使用Nginx，我们需要掌握一些基本的操作命令，比如：启动、停止Nginx服务，重新载入Nginx等，下面我就进行一些简单的介绍。
#### 1、启动：
```
C:\server\nginx-1.0.2>start nginx

或

C:\server\nginx-1.0.2>nginx.exe
```

注：建议使用第一种，第二种会使你的cmd窗口一直处于执行中，不能进行其他命令操作。

#### 2、停止：
```
C:\server\nginx-1.0.2>nginx.exe -s stop

或

C:\server\nginx-1.0.2>nginx.exe -s quit
```

注：stop是快速停止nginx，可能并不保存相关信息；quit是完整有序的停止nginx，并保存相关信息。

#### 3、重新载入Nginx：
```
C:\server\nginx-1.0.2>nginx.exe -s reload
```

当配置信息修改，需要重新载入这些配置时使用此命令。不重新启动不会生效。

#### 4、重新打开日志文件：
```
C:\server\nginx-1.0.2>nginx.exe -s reopen
```
#### 5、查看Nginx版本：
```
C:\server\nginx-1.0.2>nginx -v
```
#### 小结：
注：以上C:\server\nginx-1.0.2需要改成自已nginx的安装目录。每次写路径太麻烦，可以把C:\server\nginx-1.0.2配置成环境变量，这样就不用每次都写路径了，在任意目录下打开git或者cmd直接执行命令，系统会自己在环境变量path中查找。在此做下特殊说明：window下就算nginx配置好了环境变量，有些命令（比如：nginx -s stop）也无法用，会报错。

一、在cmd中，逐步进入到nginx的安装目录，在此目录下执行那些命令都可以。一步步进安装目录如果麻烦，可以执行
```
start /D E:\nginx\nginx-1.6.2 nginx -s start
Windows下的话可以用start命令，/D参数指定起始目录，这样在执行nginx命令前重新设定了指定的目录。
```
此时会cmd会自动把路径跳转到nginx的安装目录了，此时也可以执行命令了。

二、也可以在安装目录下打开git,输入./nginx按回车，即启动了nginx,再按ctrl+c关闭nginx.

三、可以双击nginx.exe启动nginx,当修改了nginx.conf配置文件时，此时要重启，先在任务管理器中结束nginx的进程（打开任务管理器，输入英文字母n，可以快速定位到以n开头的应用，方便查找了），然后在双击运行nginx。
