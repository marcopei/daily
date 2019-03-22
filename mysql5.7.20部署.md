tar包配置步骤
]# mkdir  -p  mysql/{data,logs,tmp}
]# echo  'PATH=/usr/local/mysql/bin:$PATH'  >  /etc/profile.d/mysql.sh
]# source  /etc/profile
]# groupadd  -g 27 mysql
]# useradd  -g 27 -u 27 -d /usr/local/mysql  -s  /sbin/nologin  -M mysql
]# mv  mysql-5.7.20-linux-glibc2.12-x86_64  /usr/local/mysql
]# chown  mysql.mysql  /usr/local/mysql  /data/nsoft/mysql  -R    ##一定要记得给目录权限
]# vim  /etc/my.cnf

初始化并启动服务：
]# /usr/local/mysql/bin/mysqld --defaults-file=/etc/my.cnf --initialize
]# cp  /usr/local/mysql/support-files/mysql.server   /etc/init.d/mysqld
]# /etc/init.d/mysqld   start

修改初始登录密码：
mysql  -S  /tmp/mysql3307.sock  -uroot  -p"pqKPi)jx.6di"
mysql> set  password=password('admin');
mysql> alter  user  'root'@'localhost' password  expire  never;
mysql> flush  privileges;
mysql  -S  /tmp/mysql3307.sock  -uroot  -padmin



##/etc/my.cnf配置文件内容：
]# cat  /etc/my.cnf
[mysqld]
server_id=8
user=mysql
port=3306
log_bin=/data/nsoft/mysql/mysql-bin
datadir=/data/nsoft/mysql/data
socket=/tmp/mysql3306.sock
tmpdir=/data/nsoft/mysql/tmp

replicate-do-db=surelive
replicate-ignore-db=mysql
binlog-ignore-db=information_schema
binlog-ignore-db=mysql

# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
# Settings user and group are ignored when systemd is used.
# If you need to run mysqld under a different user or group,
# customize your systemd unit file for mariadb according to the
# instructions in http://fedoraproject.org/wiki/Systemd

character-set-server = utf8 
open_files_limit = 65535
max_connections = 100
max_connect_errors = 100000

[mysqld_safe]
log-error=error.log
pid-file=mysql.pid

#
# include all files from the config directory
#
!includedir /etc/my.cnf.d





root登录后台管理命令：
mysql> mysql  -S  /tmp/mysql3306.sock -uroot  -padmin


mysql
#主库用户和密码
GRANT REPLICATION SLAVE ON *.* TO 'app_sync'@'172.16.16.13' IDENTIFIED BY 'admin';
GRANT ALL ON *.* TO 'app_root'@'%' IDENTIFIED BY 'yinfuadmin';
GRANT SELECT ON *.* TO 'app_read'@'%' IDENTIFIED BY 'admin';
FLUSH PRIVILEGES;
#从库用户和密码
change master to master_host='172.16.16.8',master_port=14036,master_user='app_sync',master_password='admin',master_log_file='mysql-bin.000005',master_log_pos=361;
# 创建查询用户
GRANT SELECT ON *.* TO 'app_read'@'%' IDENTIFIED BY 'admin';

查看用户权限：mysql> show  grants  for  'app_root'@'%';
撤销用户权限：mysql> REVOKE all ON *.* 'app_root'@'%';
设置用户权限：mysql> gant select on  surelive.*  to  'app_root'@'%';

过滤查询命令：show variables like "%character%";
SHOW VARIABLES WHERE Variable_name LIKE 'character\_set\_%' OR Variable_name LIKE 'collation%';


内测数据库登录命令：
/usr/local/mysql/mysql3307/bin/mysqld --defaults-file=/usr/local/mysql/conf/3307.cnf --initialize-insecure (初始化命令)
/usr/local/mysql/mysql3306/bin/mysqld --defaults-file=/usr/local/mysql/conf/3306.cnf （启动命令）


修改数据库登录密码：
/usr/local/mysql/mysql3307/bin/mysqladmin -u root password "123456" --socket=/usr/local/mysql/sock/3307.sock
/usr/local/mysql/mysql3306/bin/mysqladmin -u root password "123456" --socket=/usr/local/mysql/sock/3306.sock

登录数据库命令：
/usr/local/mysql/mysql3306/bin/mysql -uroot -p123456 --socket=/usr/local/mysql/sock/3306.sock（内测）
/usr/local/mysql/mysql3307/bin/mysql -uroot -p123456 --socket=/usr/local/mysql/sock/3307.sock（内测）

授权用户命令：
GRANT REPLICATION SLAVE ON *.* TO 'app_sync'@'%' IDENTIFIED BY 'admin';
grant  all  on *.* to  'app_root'@'%' identified  by  'admin';       14036
grant  select  on  *.*  to  'root_read'@'%'  identified  by  'admin';   14036   14037


数据库备份命令：
mysqldump  用户名  密码   库名  >    存放路径

数据导入命令：
./bin/mysql  -uroot   -p123456   --socket=/usr/local/mysql/sock/3306.sock  surelive  <  /data/mysql/surelive.sql （需要先创建一个库）


单机版主从同步，从库添加主库时，host必须是"localhost"



外测登录数据库：
8]# mysql -uroot -padmin --socket=/tmp/mysql3306.sock   （相当于是localhost登录，不走网络）
13]# mysql -uroot -padmin --socket=/tmp/mysql3307.sock  （相当于是localhost登录，不走网络）






新的内测的配置文件：
[mysqld]
user=mysql #mysql启动用户
#character-set-server=utf8 #服务字符集
port=14036 #端口
socket=/usr/local/mysql/sock/3306.sock #用于通讯的套接字，由于是一台机多实例，所以区分开
basedir=/usr/local/mysql/mysql3306 #mysql安装目录
datadir=/usr/local/mysql/data/3306 #数据存放目录
server-id=36 #本机唯一标识
log-bin=mysql-bin  #bin-log前缀
log-bin-index=mysql-bin.index #bin-log-index前缀
binlog_format=ROW  #binlog格式 （STATEMENT,ROW,FIXED），这里主要为了后续要测试canal，所以用到了ROW，具体格式区别，自行搜索
binlog-ignore-db=mysql  #不需要同步给从库的库
binlog-ignore-db=sys  #不需要同步给从库的库
binlog-ignore-db=information_schema  #不需要同步给从库的库
binlog-ignore-db=performance_schema  #不需要同步给从库的库
character-set-client-handshake = FALSE  #新增
character-set-server = utf8mb4          #新增，修改字符集
collation-server = utf8mb4_unicode_ci   #新增，修改字符集
sql_mode = STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION  #新增加优化查询


[client]
default-character-set = utf8mb4

[mysql]
default-character-set = utf8mb4

[mysqld_safe]
log-error=/usr/local/mysql/logs/3306_err.log #启动错误日志输出地址



优化思路：
线程排队，最大连接数