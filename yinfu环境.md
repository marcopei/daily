内部开发环境：
ssh 192.168.101.210
用户/密码：root/test@168
ssh  192.168.101.200
用户/密码：root/svn@sure168

ssh://134.175.14.8:22
ssh://134.175.151.120:22
用户名/密码：root/Yinfu2019.
ssh://192.168.101.211/YFTD/Yinfu2019.


http://134.175.14.8:14001
用户名/密码：admin/Yinfu2019.portainer



腾讯云控制台
qq用户名/密码：703423561/Yftd2019    子账号登录：Yinfu@2019/Yinfu@2019.admin主ID:100008762118
管理域名：shiweiwei@yinfutiaodong.com/yingfutiaodong@123



文档打开密：sure2019
文档编辑密码：sure2019.



svn用户名：dengshaopei
svn密码：168@dengshaopei

正式服MySql只读账号：sure_read/Yinfu2019@mysql
正式服mongodb只读账号：sure_read/Yinfu2019@sure


jumserver跳板机账号信息
跳板机地址： http://129.204.198.147 用户名：marco 密码：YGENZ9tcQDIBptzp 密钥密码：JHslrIijZW85Lj0f 
密钥下载url: http://129.204.198.147/juser/key/down/?uuid=5baa1e41888049c39e6703c3bfc633e9 该账号密码可以登陆web和跳板机。

跳板机地址： http://129.204.198.147 用户名：sure 密码：BIvtlN357Otmhxqn 密钥密码：apl6gmEdEKEG2pYU 
密钥下载url: http://129.204.198.147/juser/key/down/?uuid=c6aba3ded58f4a3b88927713ed4079df 该账号密码可以登陆web和跳板机。




```mysql
#主库用户和密码
GRANT REPLICATION SLAVE ON *.* TO 'app_sync'@'172.16.16.13' IDENTIFIED BY 'admin';
GRANT ALL ON *.* TO 'app_root'@'%' IDENTIFIED BY 'yinfuadmin';
GRANT SELECT ON *.* TO 'app_read'@'%' IDENTIFIED BY 'admin';
FLUSH PRIVILEGES;
#从库用户和密码
change master to master_host='172.16.16.8',master_port=14036,master_user='app_sync',master_password='admin',master_log_file='mysql-bin.000005',master_log_pos=361;
# 创建查询用户
GRANT SELECT ON *.* TO 'app_read'@'%' IDENTIFIED BY 'admin';
```

数据清理后的启动顺序：
初始化：mysql
先启动：res1 --> webcs --> api1 --> log1 --> stat1 --> appall1

外测appallTest是appall1的从，所以发版时，先停test再停appall1，启动就先启appall1再启test


每天上班要做：
1.查看所有web服务的syserr的日志，将错误汇总发出来。
2.检查所有web服务日志备份情况，并执行删除时间太久的日志，保持磁盘空间；
3.查看服务器的各项指标（因为目前没有布置监控）；
4.检查所有web服务和组件的运行情况；
5.完成上一天未完成的工作；
6.学习新知识；
