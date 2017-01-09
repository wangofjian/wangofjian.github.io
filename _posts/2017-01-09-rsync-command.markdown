草稿

https://www.huzs.net/?p=920



rsync的文件同步，复制，镜像，增量备份 v1.2
发表回复
rsync是一个linux下的：快速，多功能，远程（本地）文件复制工具。
官方网站：http://rsync.samba.org/
维基百科：http://zh.wikipedia.org/wiki/Rsync

rsync是Unix下的一款应用软件，它能同步更新两处计算机的档案与目录，并适当利用差分编码以减少数据传输。rsync中一项与其他大部分类似程式或协定中所未见的重要特性是镜像对每个目标只需要一次传送。rsync可拷贝／显示目录属性，以及拷贝档案，并可选择性的压缩以及递归拷贝。

比如网站搬家只需要使用一行命令，填入系统密码即可。同步远程服务器上的文件夹到本地，如果本地有的文件，远程没有，则删除之。

rsync -rave "ssh -p 22 -l root" --delete 192.168.0.200:/www/web/ /www/web/
在常驻模式（daemon mode）下，rsync默认监听TCP埠873，以原生rsync传输协定或者透过远程shell如RSH或者SSH伺服档案。SSH情况下，rsync用户端执行程式必须同时在本地和远程机器上安装。

Rsync 的特色：

快速：第一次同步时 rsync 会复制全部内容，但在下一次只传输修改过的文件。
安全：rsync 允许通过 ssh 协议来加密传输数据。
更少的带宽：rsync 在传输数据的过程中可以实行压缩及解压缩操作，因此可以使用更少的带宽。
特权：安装和执行 rsync 无需特别的权限
基本语法：rsync options source destination

源和目标都可以是本地或远程，在进行远程传输的时候，需要指定登录名、远程服务器及文件位置

样例：

1 在本地机器上对两个目录进行同步

rsync -zvr /var/opt/installation/inventory/ /root/temp
参数：

-z 开启压缩
-v 详情输出
-r 表示递归
2 利用 rsync -a 让同步时保留时间标记

rsync 选项 -a 称为归档模式，执行以下操作

递归模式
保留符号链接
保留权限
保留时间标记
保留用户名及组名
rsync -azv /var/opt/installation/inventory/ /root/temp/
3 仅同步一个文件

rsync -v /var/lib/rpm/Pubkeys /root/temp/
4 从本地同步文件到远程服务器

rsync -avz /root/temp/ root@192.168.200.10:/home/root/temp/
就像你所看到的，需要在远程目录前加上 ssh 登录方式，格式为 username@machinename:path

5 同步远程文件到本地

和上面差不多，做个相反的操作
rsync -avz root@192.168.200.10:/var/lib/rpm /root/temp

同步远程服务器上的文件夹到本地，如果本地有的文件，远程没有，则删除之。

rsync -rave "ssh -p 22 -l root" --delete 192.168.0.200:/www/web/ /www/web/
6 同步时指定远程 shell

用 -e 参数可以指定远程 ssh ，比如用 rsync -e ssh 来指定为 ssh

rsync -avz -e ssh root@192.168.200.10:/var/lib/rpm /root/temp
7 不要覆盖被修改过的目的文件
使用 rsync -u 选项可以排除被修改过的目的文件

rsync -avzu root@192.168.200.10:/var/lib/rpm /root/temp
8 仅仅同步目录权（不同步文件）
使用 -d 参数

rsync -v -d root@192.168.200.10:/var/lib/
9 查看每个文件的传输进程
使用 – -progress 参数

rsync -avz –-progress root@192.168.200.10:/var/lib/rpm/ /root/temp/
10 删除在目的文件夹中创建的文件
用 – -delete 参数

rsync -avz – -delete root@192.168.200.10:/var/lib/rpm/
11 不要在目的文件夹中创建新文件
有时能只想同步目的地中存在的文件，而排除源文件中新建的文件，可以使用 – -exiting 参数

rsync -avz –existing root@192.168.1.2:/var/lib/rpm/
12 查看源和目的文件之间的改变情况

用 -i 参数

rsync -avzi root@192.168.200.10:/var/lib/rpm/ /root/temp/
输出结果中在每个文件最前面会多显示 9 个字母，分别表示为
> 已经传输
f 表示这是一个文件
d 表示这是一个目录
s 表示尺寸被更改
t 时间标记有变化
o 用户被更改
g 用户组被更改

13 在传输时启用包含和排除模式

rsync -avz – -include ‘P*’ – -exclude ‘*’ root@192.168.200.10:/var/lib/rpm/ /root/temp/
14 不要传输大文件

使用 – – max-size 参数

rsync -avz – -max-size=’100K’ root@192.168.200.10:/var/lib/rpm/ /root/temp/
15 传输所有文件

不管有没有改变，再次把所有文件都传输一遍，用 -W 参数

rsync -avzW root@192.168.200.10:/var/lib/rpm/ /root/temp

使用常驻模式，让备份服务器自动获取增量备份。
rsync软件一般系统都会自带，如果没有自带请自行安装即可。安装完毕后我们开始配置.

大致流程：1、建立配置文件  2、设置权限 3、添加排除的文件夹或文件 4、启动rsync的daemon mode

配置文件：
1、rsync的主配置文件。

/etc/rsync/rsyncd.conf
配置文件内容为：

uid = www
gid = www
read only = true
transfer logging = true
max connections = 10
slp refresh = 300
hosts allow = 8.8.8.8
pid file = /var/run/rsyncd.pid
lock file = /var/run/rsyncd.lock
#log file = /var/log/rsyncd.log
#log format = %h %o %f %l %b

[web]
path = /www/web
comment = Mirror to us backup server
read only = true
list = false
auth users = zhangshan
secrets file = /etc/rsync/pass.conf
#exclude from = /etc/rsync/exclude.txt

[mysql]
path = /www/mysql
comment = Mirror to us MySql backup server
read only = true
list = false
auth users = zhangshan
secrets file = /etc/rsync/pass.conf
2、rsync的验证密码配置文件

mkdir /etc/rsync
vim /etc/rsync/pass.conf
填入用户名密码

zhangshan:zspassword
设置密码文件（/etc/rsync/pass.conf）的权限为600

chmod 600 /etc/rsync/pass.conf
3、目录排除文件

/etc/rsync/exclude.txt
文件内容为：一行一个要排除的目录

/www/www.abc.com/cache
/www/www.123.com
4、启动rsync服务器.

rsync --daemon --config=/etc/rsync/rsyncd.conf
客户机的配置过程：
1、建立密码文件并设置600权限。

mkdir /etc/rsync
/etc/rsync/pass.conf
文件内容为“密码”。注意，只填密码，否则会报1530错误。

zspassword
chmod 600 /etc/rsync/pass.conf
2、启动客户端

rsync -avzP --delete --password-file=/etc/rsync/pass.conf zhangshan@8.8.8.8::web /www/mirror
命令说明：同步服务器web模块中配置的目录到本地目录的/www/mirror，如果本地没有的文件，这复制过来。如果本地和服务器的文件一致，则不复制。如果本地文件较旧或者较新，这替换掉本地的文件。

本条目发布于2012 年 7 月 18 日。属于Linux分类。作者是admin。
