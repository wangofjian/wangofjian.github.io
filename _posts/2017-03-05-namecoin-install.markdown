---
layout: post
category: "read"
title:  "win7安装namecoin以及.bit域名"
tags: [namecoin,nmcontrol,bitdomain]
---

昨天在win7系统里面试了一下.bit域名解析，在这里记录一下。

先说理解 namecoin是一种域名币，通过NMC可以购买.bit域名。
目前.bit域名还不能在常规的dns系统使用，必须借助nmcontrol软件实现。
namecoin同步了一个完整的区块链，可以查询.bit域名。
在本地安装nmcontrol，自动启动一个dns server，通过RPC调用namecoin获取.bit域名的信息。
如果要在本机使用.bit域名，将本机的dns指向127.0.0.1,启动nmcontrol处理理.bit域名，其他域名会转给常规dns处理。
这个软件存在的问题：我的笔记本在公司里面必须使用公司内网的DNS，必须要指定一下，否则内网就用不了了。

win7下安装的参考https://github.com/namecoin/nmcontrol，步骤如下：

一、下载安装namecoin-QT.exe nmcontrol.exe并安装 
namecoin必须要同步，大概三四天才能同步完成，共3.5G，目前只知道慢慢同步，欢迎大家提供新思路。

二、安装python pip 下载get.py 安装pip 下载pywin32
在https://www.python.org/ftp/python/2.7.13/python-2.7.13.msi下载python2.7
在https://bootstrap.pypa.io/get-pip.py下载get-pip.py 
然后执行python.exe get-pip.py
pip install bottle
在https://sourceforge.net/projects/pywin32/files%2Fpywin32/ 下载适合你的pywin32

三、设置配置文件
Windows下 

NMControl 配置文件目录 %appdata%\Nmcontrol
Namecoin 配置文件目录 %appdata%\Namecoin

在C:\Users\$YOURNAME\AppData\Roaming\Namecoin\ 新建namecoin.conf 添加rpc设置
server=1
rpcuser=winston
rpcpassword=USE_THIS_STRING_TO_GET_ROBBED._JUST_HAMMER_YOUR_KEYBOARD
namehistory=1

在C:\Users\$YOURNAME\AppData\Roaming\Nmcontrol\conf\ 配置server-dns.conf 添加
disable_standard_lookups=0
resolver=114.114.114.114, ;可以选择其他dns，注意最后面要加逗号

四、调试执行
下载nmcontrol 源码 https://github.com/namecoin/nmcontrol.git，进行build
build_windows_console.bat
build_windows_gui.bat
执行过程有报错，但是后面没影响，具体什么原因未知
启动nmcontrol  两种方式
python nmcontrolwin.pyw  # GUI version
python nmcontrol.py --debug=1  #console&debug&log

调试方法：
终端开启debug模式
查看日志 C:\Users\$YOURNAME\AppData\Roaming\Nmcontrol\log.txt 文件查看错误提示

测试方法：
nslookup.exe baidu.com 127.0.0.1 
nslookup.exe 0.bit 127.0.0.1
此处注意，我注册的onecoder.bit是在zeronet使用，没有ipv4地址，所以没有显示，但是命令行终端调试会显示查询到的json结果

一切没问题后，关闭测试窗口，启动安装版本的nmcontrl即可

其他：
在linux安装的wiki参考https://wiki.namecoin.info/index.php?title=Install_and_Configure_Namecoin 但是我的VPS是centos6，好多软件都版本太老，gcc等基础软件都不支持。等我有时间弄一个centos7试一下。

另外关于namecoin注册.bit域名的json格式，记录如下
假设域名为example.bit 1.2.3.4
子域名为www.example.bit 1.2.3.5

zeronet域名 
{"zeronet":{"":"1HeLLo4uzjaLetFx6NH3PMwFP3qbRbTf3D","subdomain":"1HeLLo4uzjaLetFx6NH3PMwFP3qbRbTf3D"}}
IP形式
{"ip":"1.2.3.4", "map": {"www": {"ip":"1.2.3.5"}}} 
以下两个不太懂，仅作记录
都指向一个IP
{"ns": ["1.2.3.4", "1.2.3.5"]}
指向另一个域名
{"ns": ["ns0.web-sweet-web.net", "ns1.web-sweet-web.net", "ns0.xname.org"]}
其他格式参考 
https://wiki.namecoin.info/index.php?title=Register_and_Configure_.bit_Domains#How_to_configure_your_domain

暂时就写到这里了。
