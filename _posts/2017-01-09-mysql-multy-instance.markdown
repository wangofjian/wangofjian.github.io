draft

http://coderschool.cn/1296.html



3
Centos中Mysql创建多个数据库实例并相互转换
Posted by 撒得一地 on 2015年12月15日 in Mysql优化
上一篇: Mysql定位慢查询
下一篇: 忘记了Mysql下root密码，如何重置新密码?
科学上网    轻松上Google | 修改host解决Google打不开
推荐使用EightSS网络加速服务，秒开国外网页，谷歌Facebook随意上，支持各种平台，还可免费试用！EightSS -> 官网直达。
本文章将会演示如何在已有Mysql数据库基础上创建第二个Mysql实例，并将原数据库转移到第二个创建的数据库实例中。
描述
有时候，几个不同的数据库在同一个Mysql数据库实例中会出现问题。出现问题的场景可能是这样的：
两个个项目使用的是同一个数据库。因为某些原因，一个项目需要重新启动它的数据库。在这种情况下，这两个数据库都会受到影响。
如果其中一个项目是多线程的，一不小心就可能创造出太多的MySQL连接，超过了Mysql最大的连接数。在这种情况下，使用同一实例的其他项目将受到影响。分离数据库实例可以帮助避免这种奇怪的情况。
步骤
创建第二个Mysql实例
步骤1
创建一个新的Mysql安装目录
1
mkdir -p /var/lib/mysqlSecondInstance
步骤2
使用下列命令设置权限：
1
2
3
chmod --reference /var/lib/mysql /var/lib/mysqlSecondInstance
 
chown --reference /var/lib/mysql /var/lib/mysqlSecondInstance
在这里,"–reference"表明了原有数据库实例的权限。上面/var/lib/mysql是你之前安装第一个数据库实例的目录。
步骤3
在/etc/目录下复制现有配置文件my.cnf,并在/etc目录下重命名。
/etc/my.cnf文件包含了mysql数据库的配置属性。所以，为了创建第二个mysql数据库实例，我们必须在同一个目录下创建第二个配置文件。
1
cp -p /etc/my.cnf /etc/mySecondInstance.cnf
步骤4
编辑新复制过来的的配置文件mySecondInstance.cnf，我使用vim命令进行编辑：
1
vim /etc/mySecondInstance.cnf
并将配置文件修改成如下：
1
2
3
4
5
6
7
[mysqld]
datadir=/var/lib/mysqlSecondInstance
socket=/var/lib/mysql/mysqlSecondInstance.sock
port=3307
[mysqld_safe]
log-error=/var/log/mysqlSecondInstance.log
pid-file=/var/run/mysqld/mysqlSecondInstance.pid
步骤5
现在，我们必须安装Mysql到最新的目录下，并使用新的端口监听，比如用3307端口。
1
mysql_install_db --user=mysql --datadir=/var/lib/mysqlSecondInstance
上面的mysql_install_db在你原先Mysql安装目录下的scripts目录下，比如我的mysql_install_db所在目录：
/var/lib/mysql/scripts/mysql_install_db。你根据你第一个Mysql安装路径找到相应的安装命令。
上面的命令运行成功后我们就创建了第二个Mysql实例。现在，我们学习下如何启动和关闭这个实例。
开启和关闭Mysql第二个实例
开启：
1
mysqld_safe --defaults-file=/etc/mySecondInstance.cnf &
验证下3307端口是否监听这个实例
1
netstat -tanp | grep 3307
连接到新创建的Mysql实例
1
mysql -u root --port=3307 -h 127.0.0.1 -p
在这里,root为数据库用户名,127.0.0.1为主机地址。你可以根据你自己的地址进行更改。
使用该命令后，它会要求密码。此时密码为空，直接回车键即可，因为我们还没有设置任何密码。
进入后，我们会得到"mysql>"提示，你可以在这里设置用户密码。
关闭Mysql实例
1
mysqladmin -S /var/lib/mysql/mysqlSecondInstance.sock shutdown -p
如果有必要频繁的开始/停止这个实例，我们可以创建一个小脚本：
1
vim mySecondInstance
将下面的脚本复制到mySecondInstance文件中：
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
#!/bin/bash
start(){
  mysqld_safe --defaults-file=/etc/mySecondInstance .cnf &
}
 
stop(){
  mysqladmin -S /var/lib/mysql/mySecondInstance .sock shutdown -p
}
case "$1" in
       start)
                start
                ;;
           stop)
                stop
                ;;
esac
使脚本文件可以被执行，使用如下命令：
1
chmod 755 mySecondInstance
现在，我们可以如下这样启动和停止第二MySQL实例：
1
2
3
service mySecondInstance start
 
service mySecondInstance stop
当它要求输入用户密码时，你提供该数据库实例密码即可。
将数据库从第一个实例转移到第二个实例中
假设，在第一个Mysql数据库实例中，包含了如下的数据库：
1
2
3
4
5
6
7
mysql> show databases;
+-----------------------+
| Database |
+-----------------------+
| DatabaseOne |
| DatabaseTwo |
+-----------------------+
如果你想复制"DatabaseOne"数据库到新的第二个Mysql数据库实例中，那么可以这样：
步骤1
先导出数据库
1
mysqldump -u root -p DatabaseOne > databaseOne.sql
步骤2
进入第二个mysql实例并创建一个数据库,然后导入sql:
1
2
3
mysql -u root --port=3307 -h 127.0.0.1 -p
mysql > create database DatabaseOne
mysql > exit;
步骤3
从外部导入数据库第二个实例中
1
mysql -u root --port=3307 -h 127.0.0.1 -p DatabaseOne < databaseOne.sql
总结
这样，我们可以成功地创建MySQL数据库实例并对两者数据库进行转换。我们可以遵循相同的方法来创建多个数据库实例。
英文原文:http://www.codeproject.com/Tips/1063881/MySQL-Double-Instance-Creation-and-Database-Transf
译文地址:http://coderschool.cn/1296.html





mysql start tips

[root@l-qics.y.cn7 /home/q/wall.wang/localhost]# sudo service mysqld start
Initializing MySQL database:  Installing MySQL system tables...
OK
Filling help tables...
OK

To start mysqld at boot time you have to copy
support-files/mysql.server to the right place for your system

PLEASE REMEMBER TO SET A PASSWORD FOR THE MySQL root USER !
To do so, start the server, then issue the following commands:

/usr/bin/mysqladmin -u root password 'new-password'
/usr/bin/mysqladmin -u root -h l-qics.y.cn7 password 'new-password'

Alternatively you can run:
/usr/bin/mysql_secure_installation

which will also give you the option of removing the test
databases and anonymous user created by default.  This is
strongly recommended for production servers.

See the manual for more instructions.

You can start the MySQL daemon with:
cd /usr ; /usr/bin/mysqld_safe &

You can test the MySQL daemon with mysql-test-run.pl
cd /usr/mysql-test ; perl mysql-test-run.pl

Please report any problems with the /usr/bin/mysqlbug script!

                                                           [  OK  ]
Starting mysqld:                                           [  OK  ]
