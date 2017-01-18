draft

Crontab执行脚本中的ssh命令访问被拒绝
2016-12-17 18:03:00
标签：crontab 环境变量 ssh
原创作品，允许转载，转载时请务必以超链接形式标明文章 原始出处 、作者信息和本声明。否则将追究法律责任。http://littledevil.blog.51cto.com/9445436/1883618
我们经常会通过crontab来执行定时任务，通常生产环境中在不同主机直接进行登录是通过ssh来连接的。比如我们通过在备份服务器上设定定时任务，定期通过scp（使用SSH的远程拷贝命令）来拷贝远程服务器的数据到备份服务器上，这也是我遇到的一个事情。

环境描述：

脚本与定时任务：
定时任务设置如下：
无论是否执行成功，都把信息输出到2222.txt中
wKioL1hVA8SA4TJ1AAAOa2l0LSc103.jpg-wh_50
脚本内容如下：
我这里仅用ssh命令来测试，实际上这个命令如果可以正常执行的话，你执行scp命令也一样
1
2
#!/bin/bash
ssh -v 192.168.50.135 'ls'

报错信息：
下图是crontab执行定时任务中的脚本的报错信息
你发现TCP连接是成功建立的，然后也在备份服务器上的~/.ssh/目录中的known_hosts文件中找到了host key。这个key是对方主机的公钥。在/etc/ssh/ssh_config中的 StrictHostKeyChecking 的值决定了是否把对方的公钥放在known_hosts文件中。如果是yes则表示不存放，如果是no则表示存放，如果是ask则表示询问。
下图这个意思是说KEY找到了而且匹配，就是说备份服务器存的对方服务器的公钥和对方现在使用的公钥一致。
最后是尝试发送自己使用的公钥让对方去验证，这里尝试了几次，并且更换了几种验证方式均宣告失败，所以最后拒绝访问。
wKioL1hVBHLh5tQxAAHGlcynAWM131.jpg-wh_50
下图是单纯运行脚本的输出信息：也就是直接在终端中运行脚本
可以看到验证是通过的，不过你会发现它使用的是一个叫做id_dsa的公钥文件发送给服务器，然后对方接受了。可是我这个备份服务器本地的~/.ssh/目录中并没有id_dsa的文件
wKiom1hVCR2g1Cm5AAGYbZ0ZsgY738.jpg-wh_50
我发现跳板机（Linux的跳板机，一般登录IDC的机器都要登录到跳板机，而且其他服务器也只接受来自跳板机的IP信息，你就算拨入了VPN，如果不先连接到跳板机，你也是无法登录服务器的，那可能有人问，我通过跳板机连接到了服务器，然后从这个服务器再SSH到另外的服务器是否可以呢？可以）的~/.ssh/目录中有id_dsa文件，于是我就拷贝到我的备份服务器上，测试后依然失败。

排查问题：

查看env
wKiom1hVDuiCFGgwAAAv7agSPjw962.jpg-wh_50

修改脚本：
修改~/.bashrc文件，也就是本地变量文件，在文件中增加如下内容，这些内容和也就是之前通过env查看到的
1
2
3
4
export SSH_CLIENT="192.168.90.58 14567 1314"
export SSH_TTY=/dev/pts/2
export SSH_AUTH_SOCK=/tmp/ssh-cvRpw15068/agent.15068
export SSH_CONNECTION="192.168.90.58 14567 192.168.90.91 1314"

修改脚本
1
2
3
#!/bin/bash
source $HOME/.bashrc
ssh -v 192.168.50.135 'ls'

验证：
再次执行就成功了。

总结：
crontab执行脚本不成功通常都是环境变量导致的，因为crontab执行脚本属于非登式shell，针对这种它是不加载/etc/profiel和~/.bash_profile。它的会先加载~/.bashrc，然后加载/etc/bashrc，最后加载/etc/profile.d/下的变量。所以我在家目录中的bashrc中设置了和env中一样的变量，这样在脚本里面指定就可以了。如果你只是在basrc中写，而不在脚本中source初始化文件也不行。原因我猜测可能是跟crontab自己的环境变量有关，而且在/etc/rond的配置文件中设置环境变量不生效，不知道为什么。

所以在crontab执行脚本，通常的建议是命令写全路径，另外还应该设置好环境变量
1
2
3
#!/bin/bash
source /etc/profile
source $HOME/.bashrc
如果是ssh这种远程执行，可以使用-i参数指定公钥。
