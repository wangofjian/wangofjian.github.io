草稿状态，等待修改

一、服务器部署
1、服务器日志目录 /export/postgreslog/
2、修改rsyslog server: vim /etc/rsyslog.conf

开户tcp监听模块
$ModLoad imtcp.so
$InputTCPServerRun 514

$EscapeControlCharactersOnReceive off

#配置日志格式，去掉前面#011和行号

$template pgFormat,"%msg:R,ERE,2,DFLT:^ \[[0-9]+-[0-9]+\] (#011)?(.*)--end%\n"
配置日志文件名

$template PostgresLogFile,"/export/postgreslog/%hostname%_%programname%/%$year%-%$month%-%$day%/postgresql-%$year%-%$month%-%$day%-%$hour%.log"
收集 local.0上的日志
local0.* ?PostgresLogFile;pgFormat

重启rsyslog服务

/etc/init.d/rsyslog restart

二、客户端部署

1、postgresql.conf修改 vim postgresql.conf

log_destination = 'syslog' #设置日志从syslog输出

syslog_facility = 'LOCAL0' #日志输出设备
syslog_ident = 'postgres-test1' #设置PG实例标识

#重启
su - postgres
/opt/pg93/bin/pg_ctl -D /export/pg930_data reload

2、rsyslog client设置

$EscapeControlCharactersOnReceive off   #禁止转义，否则会出现#011 #002等

local0.* @@l-pgtest2.s.dev.cn6:514 #添加日志输出方向

重启rsyslog服务

/etc/init.d/rsyslog restart
