计算机网络学习笔记1-网络命令详解ping、arp、ipconfig、tracert、route详解

计算机网络  Ocean 2015年09月27日发布
推荐  0 推荐
已收藏  16 收藏，2.1k 浏览
1、PING
＞＞用的最多的命令，使用 ping可以测试计算机名和计算机的 ip 地址,验证与远程计算机的连接,该命令只有在安装了 tcp/ip 协议后才可以使用。

ping 域名 查看某个域名对应的ip地址，或者利用站长工具。
常用参数
-t ping 指定的计算机直到中断。ctrl c停止 例:c:\>ping 160.219.1.8 -t

-a 将地址解析为计算机名。例:c:\>ping -a 160.219.1.8（如图）其中htsc-dc1 为计算机名



-l 自定定义ping数据包大小单位字节，默认为 32 字节；最大值是 65,527。例:c:\>ping 160.219.1.8 -l 1024



-n 指定ping命令的执行次数，而不是默认的4次。例:c:\>ping 160.219.1.8 -n 100 指定ping100次后结束

以上只是ping命令的常用参数其他参数可以用 ping -？进行查看。

特别提示：在ping命令执行过程中可以按ctrl break键可以查看当前ping的统计信息，而不用中断ping命令

用ping命令检测网络故障的典型次序
① ping 127.0.0.1：该命令被送到本地计算机而不会离开本机，如果没有收到应答包，就表示TCP/IP的安装或运行存在某些最基本的问题。

② ping 本机IP：该命令多用于手工配置IP地址的局域网用户，用户计算机始终都应该对该命令做出应答，如果没有收到应答，局域网用户应断开网络电缆，然后重新发送此命令，如果运行正确，则有可能是网络中有另一台计算机配置了相同的IP地址。若仍然有错，则表示本地配置或安装有问题。

③ ping 局域网内其它IP：该命令离开用户计算机，经过网卡和网络电缆到达其他计算机，再返回。收到应答表明本地网络的网卡和载体运行正确。若没有收到应答，则可能是子网掩码错误、网卡配置错误、或网络电缆不通。
同一个局域网内其它ip都可以被ping通吗？

④ ping 网关IP：若错误，表示网关地址错、或网关未启动，或到网关的线路不通。
怎样查看自己的网关？

⑤ ping 远程IP：若收到应答，表示网关运行正常，可以成功访问Internet。

⑥ ping localhost：localhost是127.0.0.1的别名，是操作系统的网络保留名，系统应该能够将该名字转换成该地址。如果有问题，则表示主机文件（/Windows/host）中存在问题。

⑦ ping 域名：执行此命令时，计算机会先将域名转换为IP地址，一般是通过DNS服务器。如果有问题，则可能DNS服务器地址配置错误或DNS服务器故障。该功能还可用于查看域名对应的IP地址。

如果以上所有ping命令都能正常运行，通常说明用户计算机进行本地和远程通信的功能基本具备。

特别说明：ping不成功不意味着网络一定有问题，有些路由器和防火墙设置了过滤ping数据包的功能，因此当收不到返回包时，不一定说明网络有错。同样，ping命令的成功也不表示所有的网络配置都没有问题，例如，某些子网掩码错误就可能无法用这些方法检测到。

ping命令补充


使用ipconfig产看缺省网关。

2、ARP：
》》显示和修改“地址解析协议”(ARP) 所使用的到以太网的 IP 或令牌环物理地址翻译表。该命令只有在安装了 TCP/IP 协议之后才可用。arp类型分为静态和动态，动态项目由系统自动创建保存时间大概15-20分钟，静态项目有管理员手工输入创建，永久保存但重启后会失效。

多次提到的缓存是什么鬼？

常用参数

-a 用于查看所有网络接口缓存中的项目。包括IP地址mac地址和类型 例:c:\>arp -a



-a ip 用于查看指定目的ip缓存中的项目。例:c:\>arp -a 160.219.0.3

-d 用于清除所有网络接口的arp缓存。例:c:\>arp -d

-d ip 用于清除指定目的IP的arp缓存。例:c:\>arp -d 160.219.0.3

-s 增加一条静态arp项。例:c:\>arp -s 157.55.85.212 00-aa-00-62-c6-09

以上仅为arp命令的常用参数更多参数可以输入apr /?查看





3、ipconfig
》》显示所有当前的 TCP/IP 网络配置值、刷新动态主机配置协议 (DHCP) 和域名系统 (DNS) 设置。

常用参数
/all 显示所有适配器的完整 TCP/IP 配置信息。相对于不带-all参数显示的内容更完整包括当前计算机名、mac地址、网卡描述、dns设置等信息。例:c:\>ipconfig /all

举例：

在盘符提示符中输入： ipconfig /all 后回车。
显示如下:
Windows IP Configuration 【Windows IP 配置】（中文意思，下同）
    Host Name . . . . . . . . . . . . : PCNAME 【域中计算机名、主机名】
    Primary Dns Suffix . . . . . . . : 【主 DNS 后缀】
    Node Type . . . . . . . . . . . . : Unknown 【节点类型】
    IP Routing Enabled. . . . . . . . : No 【IP路由服务是否启用】
    WINS Proxy Enabled. . . . . . . . : No 【WINS代理服务是否启用 】
Ethernet adapter: 【本地连接】
    Connection-specific DNS Suffix : 【连接特定的DNS后缀】
    Description . . . . . . . . . . . : Realtek RTL8168/8111 PCI-E Gigabi 【网卡型号描述】
    Physical Address. . . . . . . . . : 00-1D-7D-71-A8-D6 【网卡MAC地址】
    DHCP Enabled. . . . . . . . . . . : No 【动态主机设置协议是否启用】
    IP Address. . . . . . . . . . . . : 192.168.90.114 【IP地址】
    Subnet Mask . . . . . . . . . . . : 255.255.255.0 【子网掩码】
    Default Gateway . . . . . . . . . : 192.168.90.254 【默认网关】
    DHCP Server. . . . . . . . . : 192.168.90.88 【DHCP管理者机子IP】
    DNS Servers . . . . . . . . . . . : 221.5.88.88 【DNS服务器地址】
    Lease Obtained. . . . . . . . . . . : 2011年4月1号 8：13：54 【IP地址租用开始时间】
    Lease Expires . . . . . . . .. . . .: 2011年4月10号 8：13：54 【IP地址租用结束时间】
    
时间不太够，有空大家一起来完善。
/release 如果网卡ip设置为自动获取此参数可以向DHCP 服务器发送重新获得ip的请求，以释放所有适配器或特定适配器的当前 DHCP 配置并丢弃 IP 地址配置。例:c:\>ipconfig /release
不懂
还有两个比较常用的参数就是release和renew了，一般情况下，这两个参数是一起使用的，ipconfig/release为释放现有的IP地址，ipconfig/renew命令则是向DHCP服务器发出请求，并租用一个IP地址。但是一般情况下使用ipconfig/renew获得的IP地址和之前的地址一样，只有在原有的地址被占用的情况下才会获得一个新的地址。

/flushdns 清除当前dns缓存，如果修改了dns服务器想要立即生效可以使用此命令 例:c:\>ipconfig /flushdns

4、Tracert
》》（跟踪路由）是路由跟踪实用程序，用于确定 IP 数据报访问目标所采取的路径。Tracert 命令用 IP 生存时间 (TTL) 字段和 ICMP 错误消息来确定从一个主机到网络上其他主机的路由。最多30跳。

-d 防止 tracert 试图将中间路由器的 IP 地址解析为它们的名称。这样可加速显示 tracert 的结果。例;c:\>tracert -d 8.8.8.8

-h 指定搜索目标的路径中存在的跃点的最大数。默认值为 30 个跃点。例:c:\>tracert -d -h 5 8.8.8.8

5、route
》》用于显示和修改本地的路由表

常用命令和参数
print 显示本地路由表。例:c:\>route print

add 添加路由条目

例：要添加目标为 10.41.0.0，子网掩码为 255.255.0.0，下一个跃点地址为 10.27.0.1 的路由，请键入：route add 10.41.0.0 mask 255.255.0.0 10.27.0.1

change 更改现存路由条目

例：要将目标为 10.41.0.0，子网掩码为 255.255.0.0 的路由的下一个跃点地址由 10.27.0.1 更改为 10.27.0.25，请键入：route change 10.41.0.0 mask 255.255.0.0 10.27.0.25

delete 删除路由条目

例：要删除目标为 10.41.0.0，子网掩码为 255.255.0.0 的路由，请键入：route delete 10.41.0.0 mask 255.255.0.0

-f 清除所有不是主路由（网掩码为 255.255.255.255 的路由）、环回网络路由（目标为 127.0.0.0，网掩码为 255.255.255.0 的路由）或多播路由（目标为 224.0.0.0，网掩码为 240.0.0.0 的路由）的条目的路由表。如果它与命令之一（例如 add、change 或 delete）结合使用，表会在运行命令之前清除。

-p 默认情况下添加路由条目在系统重启后会丢失，使用“-p”参数后指定路由被添加到注册表并在启动系统的时候初始化 IP 路由表。（静态路由）例:c:\>route -p add 10.41.0.0 mask 255.255.0.0 10.27.0.1

以上这些都是windows下网管员常用的一些网络命令，更高级的功能可以查看命令帮助 “命令 /?”

路由概念 route print
》》通过路由器将数据从一个网络传输到另一个网络称之为路由。路由选择负责在网络中选择一段最优先的路径将数据传输到目的网络，路由选择的基础和依据是路由表，路由表由目的网络ID、子网掩码、网关、接口和计费组成，通过route print可查看计算机的路由表。



路由表规则怎么起作用的我忘了

IPv4 Route Table
　　===========================================================================
　　Interface List
　　0x1 ........................... MS TCP Loopback interface
　　0x10003 ...00 03 ff 25 88 8c ...... Intel 21140-Based PCI Fast Ethernet Adapter
　　(Generic)
　　===========================================================================
　　===========================================================================
　　Active Routes:
　　Network Destination     Netmask        Gateway     Interface     Metric
　　0.0.0.0                 0.0.0.0        172.16.11.1 172.16.11.30  20
　　127.0.0.0               255.0.0.0      127.0.0.1   127.0.0.1     1
　　172.16.11.0             255.255.255.0  172.16.11.30 172.16.11.30 20
　　172.16.11.30            255.255.255.255 127.0.0.1  127.0.0.1     20
　　172.16.255.255          255.255.255.255 172.16.11.30 172.16.11.30 20    
　　224.0.0.0               240.0.0.0       172.16.11.30 172.16.11.30 20
　　255.255.255.255         255.255.255.255 172.16.11.30 172.16.11.30 1
　　Default Gateway: 172.16.11.1
　　===========================================================================
　　Persistent Routes:
　　None
路由表中每一个路由表项(或路由)都由五个字段组成：
　　网络目标地址(Network Destination)：代表某个可能的目的地址，它是一个IP地址或子网，即表示IP数据包被转发到何处的地址。好理解
　　掩码(Netmask):一个用于将某数据包中的IP地址中的目标地址字段与上面可能的网络地址匹配起来的位模式。好理解，和目标地址配合使用
　　网关(Gateway)：下一跳的IP地址，数据包必须被转发到此，才能到达特定的目的网络。
　　接口(Interface)：下一跳的接口，这个接口必须用于将数据包进行转发，以达到特定的目的网络。
　　跳数(metric):表示到达目的的过程中经过了多少跳数(路由器数)，即路由的成本。

有了网关及目标地址还要接口做什么？

示例一：目标主机在本地子网上
　　假设这个服务器(172.16.11.30)要将数据包发往同一子网内的另一台主机(IP地址为172.16.11.80)。那么这个数据包的源地址为172.16.11.30，目标地址为172.16.11.80。下面我们将展示Windows是如何用其路由表来决定选择使用哪条路由的：
　　1、 Windows首先依次从路由表中取出每一个路由，并将数据包的目标地址(172.16.11.80)与选中路由的掩码执行逻辑“与”运算。下面展示结果，这里，路由表中的每一个路由是通过其网络目的地址确定的：

Route                   Netmask           172.16.11.80 AND Netmask
0.0.0.0                 0.0.0.0           0.0.0.0
127.0.0.0               255.0.0.0         172.0.0.0
172.16.11.0             255.255.255.0     172.16.11.0
172.16.11.30            255.255.255.255   172.16.11.80
172.16.255.255          255.255.255.255   172.16.11.80
224.0.0.0               224.0.0.0         160.0.0.0
255.255.255.255         255.255.255.255   172.16.11.80
2、对每一个路由来说，这个“与”运算的结果要与路由的网络目标地址比较，二者的一次匹配意味着这条路由可用于将数据包转发到其目标地址。如果发现不只有一个匹配，Windows就会选用拥有最长匹配的路由(即1的位数最高的路由)。如果这并没有产生唯一的路由，Windows就任意地选用一个作为路由。从上面的列表中，这个“与”运算的结果导致了两个匹配(路由1和3)，因此Windows选择了拥有最长匹配的路由，即第三行的那个。其结果是Windows知道了使用哪个路由将数据包传送到目的地。下面是这条路由在服务器的路由表中看起来的样子：

　　Network Destination   Netmask     　 Gateway        　Interface   Metric
　　172.16.11.0           255.255.255.0  172.16.11.30 　　172.16.11.30   20
　　3、 Windows现在要使用下面的算法来决定下一步做什么：
　　a) 如果路由的网关字段与服务器上的一个网络接口的地址相匹配(或者如果网关是空的话)，那么Windows就会用在路由中指定的接口将数据包直接发送到目标地址。
　　b) 如果路由的网关字段并不与服务器上网络接口的任意地址相匹配，Windows将会把数据包转发给路由中的网关字段的地址。
　　很明显，这里符合条件a，路由的网关字段(172.16.11.30)即为分配给服务器单个网卡的地址。Windows因此会作出决定认为目标地址位于本地子网上，这也就是说Windows不需要将数据包发送到任何路由器，而是直接发送给其目的地址。在此例中，Windows使用服务器的172.16.11.30的网络接口，简单地将数据包发送给172.16.11.80，接收主机得到了数据包。

示例二：目标主机在远程子网上
　　现在，让我们继续同样的过程，不过这次我们假定服务器想把数据包发往一个不同子网上的一台主机(IP地址为172.16.10.200)。换句话说，数据包的源地址为172.16.11.30，目标地址为172.16.10.200。下面我们看一下Windows是如何利用路由表决定选择哪条路由的： 网管网bitsCN_com
　　1、Windows从路由表中取出每一条路由，将数据包的目标地址(172.16.10.200)与路由中的掩码进行“与” 运算。运算结果如下：

    Route           Netmask                  172.16.10.200 AND Netmask
    0.0.0.0         0.0.0.0                   0.0.0.0
    127.0.0.0       255.0.0.0                 172.0.0.0 
    172.16.11.0     255.255.255.0             172.16.10.0
    172.16.11.30    255.255.255.255           172.16.10.200 
    172.16.255.255  255.255.255.255           172.16.10.200
    224.0.0.0       224.0.0.0                 160.0.0.0 
    255.255.255.255 255.255.255.255           172.16.10.200
2、对于每一条路由来说，“与”运算的结果要与路由中网络目标地址相比较，二者匹配意味着这条路由可被用于将数据包转发到其目标地址。从我们上面的第二张路由表，你可以看出这次只有一个匹配，也就是说是第一行，这个路由的网络目地字段(0.0.0.0)与“与”运算的结果匹配。因此Windows用来将数据包转发到其目标地址的路由即为下面的路由：

　　Network Destination 　　　Netmask 　　　　Gateway 　　　　Interface Metric
　　0.0.0.0 　　　　　　　　　　0.0.0.0 　　　172.16.11.1　　 172.16.11.30 20
　　3. 然后Windows就会使用前述的算法来决定下一步做什么，这次符合条件b，因为路由的网关字段(172.16.11.1)与分配给服务器的单独网卡的地址(172.16.11.30)并不匹配。Windows因此会决定目标地址位于一个远程子网上，将数据包转发给路由器，路由器通过继续转发数据包将其传送到目的地。在此例中，Windows使用服务器的172.16.11.30网络接口，将数据包发送到在网关字段中所显示的地址。一旦位于172.16.11.1的路由器收到了数据包，它会决定下一步需要采取什么步骤，才能将数据包转发到其最后的目标地址172.16.10.200，而这又依赖于172.16.11.10/24网络是172.16.11.11/24(由单个路由器连接)或一个远程网络的邻近子网(由几个中间网络之间的路由器连接)。

netstat
netstat -s——本选项能够按照各个协议分别显示其统计数据。如果你的应用程序（如Web浏览器）运行速度比较慢，或者不能显示Web页之类的数据，那么你就可以用本选项来查看一下所显示的信息。你需要仔细查看统计数据的各行，找到出错的关键字，进而确定问题所在。







netstat -e——本选项用于显示关于以太网的统计数据。它列出的项目包括传送的数据报的总字节数、错误数、删除数、数据报的数量和广播的数量。这些统计数据既有发送的数据报数量，也有接收的数据报数量。这个选项可以用来统计一些基本的网络流量。



netstat -r——本选项可以显示关于路由表的信息，类似于后面所讲使用route print命令时看到的 信息。除了显示有效路由外，还显示当前有效的连接。

netstat -a——本选项显示一个所有的有效连接信息列表，包括已建立的连接（ESTABLISHED），也包括监听连接请求（LISTENING）的那些连接，断开连接（CLOSE_WAIT）或者处于联机等待状态的（TIME_WAIT）等

netstat -n——显示所有已建立的有效连接。 好像不会显示listening的。
2015年09月27日发布 更多
