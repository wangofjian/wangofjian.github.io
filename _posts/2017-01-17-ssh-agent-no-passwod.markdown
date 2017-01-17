draft

ssh免密码登录  
http://blog.csdn.net/netingcn/article/details/1698934

实现安全的免密码ssh登录

1、用ssh-keygen生成一对公私钥,命令如下
ssh-keygen -t rsa
回车会让你输入存放公私钥的路径和名称，默认存放在~/.ssh/目录下
接下来输入一个口令,用来保护这个私钥，如果此处你输入了口令，则在连接你上传公钥的机器时会问你要这个密码的，如果口令为空，就可以直接连接上，没有口令很不安全的，一旦有人复制了你的passphrase，那么他就可以不用密码登录上了。

2、把生成的公钥上传到你想连接服务器上,并且将其内容追加到一个叫~/.ssh/authorized_keys的文件里，命令如下
cat 公钥文件 | ssh 用户@192.168.1.9 "cat - >> ~/.ssh/authorized_keys"
(注意，如果不存在~/.ssh文件夹，请手工创建)

如果在第一步中没有输入口令，至此就可以实现无需输入密码即可登陆ssh服务器拉，反之，你用ssh 用户@服务器地址,则会提示你输入私钥的口令，输入正确的口令，即可登陆，你也可以直接回车，这时会提示你输入服务器的密码。

到现在你还没有实现真正的无密码登陆，不过可以使用ssh-agent来自动提供私钥，命令如下
eval `ssh-agent`
ssh-add
这里会提示输入一次passphrase,这里输入了正确的口令后，再在本session中来用ssh就不需要在输入口令，但是重新打开一个终端，在那里面使用ssh还是需要输入口令，如果想复用已经存在的ssh-agent，可以用一个简单的perl脚本来实现，脚本内容如下：

#!/usr/bin/env perl
# author: jianingy
# 2007/06/24

use strict;
my $exist;
my @dirs = grep {-r $_} glob "/tmp/ssh-*";
foreach (@dirs) {
    my @socks = glob("{$_}/agent.*");
    next unless (@socks);
    foreach (@socks) {
        $exist = $_;
        # check agent
        unless (system "SSH_AUTH_SOCK=$_ ssh-add -l >/dev/null 2>&1") {
            print "export SSH_AUTH_SOCK=$_/n";
            exit 0;
        }
    }
}

if ($exist) {
    system "SSH_AUTH_SOCK=$exist ssh-add";
    print "export SSH_AUTH_SOCK=$exist/n";
} else {
    system "ssh-agent";
    exec $0;
}

用toucth创建一个文件，名为ssh-attach(名字随便叫)，把上面的内容加进去，修改该文件的权限,chmod 755 ssh-attact,把它放到/usr/bin小，然后在.bashrc中加上下面一行，
eval `ssh-attac`

至此，就是实现了免密码ssh登陆了（当然至少还是需要输入一次口令的(passphrase)）

(注：本文参照 http://www.jianingy.com/ssh-pass.html）
