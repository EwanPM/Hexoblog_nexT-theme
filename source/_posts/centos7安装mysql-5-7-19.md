---
title: centos7安装mysql 5.7.19
tags:
  - mysql
  - Linux
  - server
categories: 基础
abbrlink: 49555
date: 2018-09-02 22:55:36
---
#### 开始之前
这个是用来记录在vps装mysql的过程，前前后后折腾了两天，比较痛苦，中途安装失败重装了几次，因为用的是国外的官网下载，因此加了代理服务，下载速度5MB/s，妥妥的
#### 准备工作
安装之前需要检查当前服务器环境是否安装了Mariadb，一般情况下，系统会默认装了Mariadb

```
rpm -qa | grep mariadb
```

得到如下结果

```
mariadb-5.5.56-2.el7.x86_64 
mariadb-devel-5.5.56-2.el7.x86_64 
mariadb-libs-5.5.56-2.el7.x86_64 
mariadb-server-5.5.56-2.el7.x86_64 
```

输入一下代码，一一卸载，不同版本启动服务容易产生冲突

```
rpm -e --nodeps mariadb-5.5.56-2.el7.x86_64 mariadb-devel-5.5.56-2.el7.x86_64 mariadb-libs-5.5.56-2.el7.x86_64 mariadb-server-5.5.56-2.el7.x86_64
```

进入root目录（也可以其他目录，下载存放的文件夹）

```
cd /root
```

#### 下载安装

```
wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.19-linux-glibc2.12-x86_64.tar.gz
```

解压至/usr/local/目录下

```
tar -xzvf mysql-5.7.19-linux-glibc2.12-x86_64.tar.gz -C /usr/local/
```

cd到/usr/local目录，把解压的数据库默认名字换一换

```
cd /usr/local
mv mysql-5.7.19-linux-glibc2.12-x86_64 mysql
```

检查库文件是否有删除，若有便删除（linux系统自带的）

```
[root@host mysql]# rpm -qa | grep mysql
```

检查mysql组和用户是否存在，如无创建

```
[root@host~]# cat /etc/group | grep mysql
[root@dbserver~]# cat /etc/passwd |grep mysql
```

建立一个mysql的组

```
[root@host~]# groupadd mysql
```

建立mysql用户，并且把用户放到mysql组

```
[root@host~]# useradd -r -g mysql mysql
```

![](https://ws3.sinaimg.cn/large/0069RVTdgy1fuvgz5npjnj30ay02bdfw.jpg)

如果有输入删除原来用户和组

```
userdel mysql
```

这里的mysql是用户名字，当初建立组的时候设定 

cd到mysql文件夹 

创建mysql组和mysql用户

```
[root@host~]#groupadd mysql
[root@host~]#useradd -r -g mysql mysql
```

在 mysql 文件下添加 data 目录

```
[root@host mysql]# mkdir data
```

更改mysql目录下所有的目录及文件夹所属组合用户

```
cd /usr/local/
chown -R mysql mysql/
chgrp -R mysql mysql/
cd mysql/
ls -l
```

![](https://ws3.sinaimg.cn/large/0069RVTdgy1fuvjsqrtdvj30db05vjra.jpg)

#### 初始化

进入/mysql/bin文件夹，初始化数据库

```
[root@host bin]# ./mysqld --initialize --user=mysql --basedir=/usr/local/mysql/--datadir=/usr/local/mysql/data/
```

初始化中出现 error while loading shared libraries: libaio.so.1: cannot open shared object file: No such file or directory的错误，如图所示

![](https://ws4.sinaimg.cn/large/0069RVTdgy1fuvjt197xgj30ll04eq2y.jpg)

这里需要libais包，可以解决办法

```
[root@host bin]# yum install -y libaio
```

注意，为了预防其他事件出现，建议把环境改成英文，以后系统最好也用原版英文不会有语言兼容而报错，输入下面代码

```
[root@dbserver bin]# ./mysqld --initialize --user=mysql --basedir=/usr/local/mysql/ --datadir=/usr/local/mysql/data/ --lc_messages_dir=/usr/local/mysql/share --lc_messages=en_US
```

在执行一次，出现下面结果，注意，记住最后数据库的初始密码

```
2018-08-21T14:56:56.679269Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).

2018-08-21T14:56:56.974223Z 0 [Warning] InnoDB: New log files created, LSN=45790

2018-08-21T14:56:57.017598Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.

2018-08-21T14:56:57.074003Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: 730a0cba-a552-11e8-92c5-aaaa000f87e0.

2018-08-21T14:56:57.075679Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.

2018-08-21T14:56:57.076236Z 1 [Note] A temporary password is generated for root@localhost: **-/RrM;0k1jdo**
```

#### 配置my.cnf

进入 /usr/local/mysql/support-files/ 目录下，查看是否存在my-default.cnf 文件，如果存在直接 copy 到 /etc/my.cnf 文件中

```
cp -a ./support-files/my-default.cnf /etc/my.cnf
```

如果不存在 my-default.cnf 文件, 则在 /etc/ 目录下创建 my.cnf

```
cd /etc/
touch my.cnf
vi my.cnf
```

输入以下内容

```
\#[mysql]
\#basedir=/usr/local/mysql/
\#datadir=/usr/local/mysql/data/
```

#### 启动服务

```
[root@host mysql]# cd /bin
[root@host bin]# ./mysqld_safe --user=mysql &
```

如图所示

![](https://ws4.sinaimg.cn/large/0069RVTdgy1fuvjyjeap5j30lp03rmx2.jpg)


#### 将mysqld服务加入VPS开机自启动项

将{mysql}/ support-files/mysql.server 拷贝为/etc/init.d/mysql并设置运行权限，这样就可以使用service mysql命令启动/停止服务， 

否则就只能使用{mysql}/bin/mysqld_safe &命令来启动服务 

还需要把mysql.server中basedir的相关路径，改为自定义的路径，默认路径是/usr/local/mysql

```
[root@host bin]# cd ../support-files [root@host support-files]# cp mysql.server /etc/init.d/mysql [root@host support-files]# chmod +x /etc/init.d/mysql 
-- 把mysql注册为开机启动的服务 
[root@host support-files]# chkconfig --add mysql
-- 查看是否添加成功
[root@dbserver support-files]#  chkconfig --list mysql
Note: This output shows SysV services only and does not include native
systemd services. SysV configuration data might be overridden bynative systemd configuration.
If you want to list systemd services use 'systemctl list-unit-files'.
To see services enabled on particular target use 'systemctl list-dependencies [target]'.
mysql           0:off   1:off   2:on    3:on    4:on    5:on    6:off
```

启动服务

```
cd /usr/local/mysql/bin
[root@host bin]# service mysql start
```
![](https://ws4.sinaimg.cn/large/0069RVTdgy1fuvkbyk10tj30mf018q2w.jpg)
终于搞定了，/(ㄒoㄒ)/~~哭一下

也有可能会出现报错，

![](https://ws1.sinaimg.cn/large/0069RVTdgy1fuvl316kl6j30l300yglf.jpg)

出现这样情况解决办法

```
[root@host mysql]# rm /etc/my.cnf
rm:remove regular file'/etc/my.cnf'? y 
[root@host mysql]# /etc/init.d/mysql startStartingMySQL.Loggingto'/usr/local/mysql/data/dbserver.err’.
SUCCESS! 
[root@host mysql]# service mysql start
Starting MySQL SUCCESS!
```

登录mysql

```
[root@host bin]# ./mysql -u root -p
密码是-/RrM;0k1jdo，之前随机生成的
```

设置密码，更改为root

```
mysql> set password=password("root"); 
```

```
Query OK, 0 rows affected, 1 warning(0.00sec)
```

如果忘记了密码还可以这样操作

```
[root@host bin]# /etc/init.d/mysql stop
[root@host bin]# mysqld_safe --user=mysql --skip-grant-tables --skip-networking &
[root@host bin]# mysql -u root mysql
mysql> UPDATE user SET Password=PASSWORD('newpassword') where USER='root';
// 上面语句若出错，换为
update mysql.user set authentication_string=password('newpassword') where user='root'
mysql> FLUSH PRIVILEGES;
mysql> quit
[root@host bin]# /etc/init.d/mysqld restart
[root@host bin]# mysql -uroot -p
Enter password:
mysql>
```

#### 设置远程登录权限

```
mysql>  grant all privileges on *.* to'root' @'%' identified by 'root';
Query OK, 0 rows affected, 1 warning (0.00 sec)
mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
mysql> quit
Bye
```

#### 进程关闭

若以上步骤中出现其他错误，可以看看 mysql 是否关闭了，先关闭端口，然后在试试

```
[root@host ~]# netstat -tpln
```

![](https://ws2.sinaimg.cn/large/0069RVTdgy1fuvl41jrc4j30nq06taa2.jpg)

```
[root@host ~]# kill -9 17072
```

如果netstat功能无法实现，需要执行下面的语句

```
yum install net-tools
```

就能解决问题

写在后面的话

Navicat连接vps mysql

Navicat在v12以下是不能连上服务器的mysql，本地的可以，解决办法，只要升级Navicat premium版本到v12.0以上就可以连接成功

参考：

https://blog.csdn.net/zhou920786312/article/details/77750604