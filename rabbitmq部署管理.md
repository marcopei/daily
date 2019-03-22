4369 -- erlang发现口
5672 -- client端通信口
15672 -- 管理界面ui端口
25672 -- server间内部通信口

yum安装rabbitmq:
自带版本：
客户端：rabbitmq-java-client-3.6.0-1.el7.noarch
服务端：rabbitmq-server-3.3.5-34.el7.noarch
依赖的关系包：erlang-solutions-1.0-1.noarch

启动控制台插件：
启动监控管理器：rabbitmq-plugins enable rabbitmq_management
关闭监控管理器：rabbitmq-plugins disable rabbitmq_management

常用命令
启动rabbitmq：systemctl  start/restart  rabbitmq-server.service
关闭rabbitmq：systemctl  stop  rabbitmq-server.service
查看所有的队列：rabbitmqctl list_queues
清除所有的队列：rabbitmqctl reset
关闭应用：rabbitmqctl stop_app
启动应用：rabbitmqctl start_app


5、管理
Rabbitmq服务器的主要通过rabbitmqctl和rabbimq-plugins两个工具来管理，以下是一些常用功能。

1）. 服务器启动与关闭
      启动: rabbitmq-server –detached
      关闭:rabbitmqctl stop
      若单机有多个实例，则在rabbitmqctlh后加–n 指定名称

2）. 插件管理
      开启某个插件：rabbitmq-plugins enable xxx
      关闭某个插件：rabbitmq-plugins disable xxx
      注意：重启服务器后生效。

3）.virtual_host管理
      新建virtual_host: rabbitmqctl add_vhost  xxx
      撤销virtual_host:rabbitmqctl  delete_vhost xxx

4）. 用户管理
      新建用户：rabbitmqctl add_user xxxpwd
      删除用户: rabbitmqctl delete_user xxx
      改密码: rabbimqctlchange_password {username} {newpassword}
      设置用户角色：rabbitmqctlset_user_tags {username} {tag ...}
              Tag可以为 administrator,monitoring, management

5）. 权限管理
      权限设置：set_permissions [-pvhostpath] {user} {conf} {write} {read}
               Vhostpath
               Vhost路径
               user
      用户名
              Conf
      一个正则表达式match哪些配置资源能够被该用户访问。
              Write
      一个正则表达式match哪些配置资源能够被该用户读。
               Read
      一个正则表达式match哪些配置资源能够被该用户访问。
      
      


编译安装：
1,安装编译工具
	yum -y install make gcc gcc-c++ kernel-devel m4 ncurses-devel openssl-devel

#解压
	tar -zxvf otp_src_18.3.tar.gz
	cd otp_src_18.3

#配置 '--prefix'指定的安装目录
	./configure --prefix=/data/soft/erlang --with-ssl -enable-threads -enable-smmp-support -enable-kernel-poll --enable-hipe --without-javac

#安装
	make && make install   #耐心等下完成即可.
	
#配置erlang环境变量
	vim /etc/profile
	#在文件末尾添加下面代码, 'ERLANG_HOME'等于上一步'--prefix'指定的目录
	ERLANG_HOME=/usr/local/erlang
	PATH=$ERLANG_HOME/bin:$PATH
	export ERLANG_HOME
	export PATH
	#使环境变量生效
	source /etc/profile
	#输入命令检验是否安装成功
	erl
	#如下输出表示安装成功
	Erlang/OTP 18 [erts-7.3] [source] [64-bit] [async-threads:10] [hipe] [kernel-poll:false]
	Eshell V7.3  (abort with ^G)

3.2 安装 
	RabbitMQ3.6版本无需make、make install 解压就可以用
	#解压rabbitmq，官方给的包是xz压缩包，所以需要使用xz命令
	xz -d rabbitmq-server-generic-unix-3.6.1.tar.xz
	#xz解压后得到.tar包，再用tar命令解压
	tar -xvf rabbitmq-server-generic-unix-3.6.1.tar
	#移动目录
	cp -rf ./rabbitmq_server-3.6.1 /usr/local/
	cd /usr/local/
	#修改文件夹名
	mv rabbitmq_server-3.6.1 rabbitmq-3.6.1
	#开启管理页面插件
	cd ./rabbitmq-3.6.1/sbin/
	./rabbitmq-plugins enable rabbitmq_management

3.3  启动
	#启动命令，该命令ctrl+c后会关闭服务
	./rabbitmq-server
	#在后台启动Rabbit
	./rabbitmq-server -detached
	#关闭服务
	./rabbitmqctl stop
	
3.4 添加管理员账号
	#进入RabbitMQ安装目录
	cd /usr/local/rabbitmq-3.6.1/sbin
	#添加用户
	#rabbitmqctl add_user Username Password
	./rabbitmqctl add_user admin 123456
	#设置用户标签
	#rabbitmqctl set_user_tags User Tag
	#[administrator]:管理员标签
	./rabbitmqctl set_user_tags admin administrator	
	#设置用户权限
	./rabbitmqctl set_permissions -p "/" admin ".*" ".*" ".*"	
	#设置完成后可以查看当前用户和角色(需要开启服务)
	./rabbitmqctl list_users




自定义编写rabbitmq.config配置文件修改默认端口：
[
 {rabbit,  [{tcp_listeners,  [6672]},
            {collect_statistics_interval,  10000 }]},

 {rabbitmq_management,  [{listener, [{port, 16672}]}]}

].



正式服的mq账号和密码：
./sbin/rabbitmqctl   add_user  admin  sureadmin