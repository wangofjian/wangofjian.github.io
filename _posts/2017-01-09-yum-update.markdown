draft

修改yum镜像，以及相关命令

CentOS镜像使用帮助

收录架构

i386
x86_64
SRPMS
收录版本

所有版本

更新时间

每4小时更新一次

使用说明

首先备份/etc/yum.repos.d/CentOS-Base.repo

mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
下载对应版本repo文件, 放入/etc/yum.repos.d/(操作前请做好相应备份)

CentOS7
CentOS6
CentOS5
运行以下命令生成缓存

yum clean all
yum makecache
相关链接

官方主页: http://www.centos.org/
邮件列表: http://www.centos.org/modules/tinycontent/index.php?id=16
论坛: http://www.centos.org/modules/newbb/
文档: http://www.centos.org/docs/
Wiki: http://wiki.centos.org/
镜像列表: http://www.centos.org/modules/tinycontent/index.php?id=32
本文档由网易公司收集整理, 希望能对国内开源软件用户有所帮助. 请转载时给出原始链接, 否则我们保留追究法律责任的权利

还可以考虑
epel
其他源


