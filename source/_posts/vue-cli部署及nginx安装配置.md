---
title: vue-cli部署及nginx安装配置
tags:
  - vue-cli
  - nginx
  - 博客
categories: 技术
abbrlink: 11240
date: 2018-09-03 00:24:09
---
#### 开始之前

以前的博客用的vue+node编写的，webpack做好打包文件之后，在项目文件夹下生成dist(webpack 3.0以上)文件，这个文件夹就是要部署的文件目录，另外，server文件需要单独上传，与dist同目录。

#### 安装Nginx

nginx是一款轻量级高性能HTTP和反向代理服务，在阿里注册了一个域名之后，可以无限的生成二级，三级，四级域名，对应不同项目目录。

在VPS服务器上需要下面三个依赖包:

1. SSL功能需要openssl库，下载地址 <http://www.openssl.org/>
2. rewrite模块需要pcre库，下载地址 <http://www.pcre.org/>
3. gzip模块需要zlib库，下载地址 <http://www.zlib.net/>
4. Nginx安装包

进入任意目录下载以上压缩包(版本号改为最新即可)：

注意各个目录下的是 configure 还是 config 

```
[root@host zlib-1.2.11]# ./configuer
[root@host zlib-1.2.11]# make
[root@host zlib-1.2.11]# make install
[root@host pcre-8.41]# ./configuer
[root@host zlib-1.2.11]# make
[root@host zlib-1.2.11]# make install
[root@host openssl-fips-2.0.16]# ./config
[root@host zlib-1.2.11]# make
[root@host zlib-1.2.11]# make install
[root@host nginx-1.13.7]# ./configure --with-pcre=../pcre-8.41/ --with-zlib=../zlib-1.2.11/ --with-openssl=../openssl-fips-2.0.16/ [root@host nginx-1.13.7]# make
[root@host zlib-1.2.11]# make install
```

安装3个依赖包，分别进入各自解压目录

<u>注意：安装 C++ 编译环境 （上面安装过程中如若有报错，可以看看是不是因为没有安装这个，可提前安装）</u>

```
yum install gcc-c++
```

```
[root@host ~]# cd /usr/local/nginx
[root@host nginx]# ls
client_body_temp  conf  fastcgi_temp  html  logs  nginx.conf  proxy_temp  sbin  scgi_temp  uwsgi_temp
```

安装好的Nginx路径在 /usr/local/nginx

配置文件路径：

```
/usr/local/nginx/conf/nginx.conf
```

#### 运行Nginx

```
[root@host~]# cd /usr/local/nginx/sbin
[root@hostsbin]# ./nginx
// 查看是否运行成功
[root@hostsbin]# netstat -ntlp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      3525/nginx: master
```

安装成功的话，浏览器输入 IP 地址或域名即可见到欢迎页面。

#### 使用server命令启动nginx命令

现在nginx启动、关闭比较麻烦，关闭要找到PID号，然后杀死进程，启动要进入到 /usr/local/nginx/sbin 目录下使用命令，为此我们通过设置System V脚本来使用server命令启动、关闭、重启nginx服务。 

1.在 /etc/init.d 目录下创建nginx启动脚本文件

```
[root@host ~]# cd /etc/init.d
[root@host init.d]# vi nginx
```

2.将以下代码复制粘贴，保存。 注意 NGINX_BIN、CONFIGFILE、PIDFILE 三个目录要对应好，默认是对应好的。在网上找了好多相关脚本代码，都有很多问题，好像是和 CentOS 版本有关，下面脚本我在 CentOS 7 下使用正常，直接从服务器下载用sublime编辑，效率比在vps上vi编译高多了

```
#! /bin/sh
 # chkconfig: 2345 55 25
 # Description: Startup script for nginx webserver on Debian. Place in /etc/init.d and
 # run 'update-rc.d -f nginx defaults', or use the appropriate command on your
 # distro. For CentOS/Redhat run: 'chkconfig --add nginx'
 
 ### BEGIN INIT INFO
 # Provides:          nginx
 # Required-Start:    $all
 # Required-Stop:     $all
 # Default-Start:     2 3 4 5
 # Default-Stop:      0 1 6
 # Short-Description: starts the nginx web server
 # Description:       starts nginx using start-stop-daemon
 ### END INIT INFO
 
 # Author:   licess
 # website:  http://lnmp.org
 
 PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
 NAME=nginx
 NGINX_BIN=/usr/local/nginx/sbin/$NAME
 CONFIGFILE=/usr/local/nginx/conf/$NAME.conf
 PIDFILE=/usr/local/nginx/logs/$NAME.pid
 
 case "$1" in
     start)
         echo -n "Starting $NAME... "
 
         if netstat -tnpl | grep -q nginx;then
             echo "$NAME (pid `pidof $NAME`) already running."
             exit 1
         fi
 
         $NGINX_BIN -c $CONFIGFILE
 
         if [ "$?" != 0 ] ; then
             echo " failed"
             exit 1
         else
             echo " done"
         fi
         ;;
 
     stop)
         echo -n "Stoping $NAME... "
 
         if ! netstat -tnpl | grep -q nginx; then
             echo "$NAME is not running."
             exit 1
         fi
 
         $NGINX_BIN -s stop
 
         if [ "$?" != 0 ] ; then
             echo " failed. Use force-quit"
             exit 1
         else
             echo " done"
         fi
         ;;
 
     status)
         if netstat -tnpl | grep -q nginx; then
             PID=`pidof nginx`
             echo "$NAME (pid $PID) is running..."
         else
             echo "$NAME is stopped"
             exit 0
         fi
         ;;
 
     force-quit)
         echo -n "Terminating $NAME... "
 
         if ! netstat -tnpl | grep -q nginx; then
             echo "$NAME is not running."
             exit 1
         fi
 
         kill `pidof $NAME`
 
         if [ "$?" != 0 ] ; then
             echo " failed"
             exit 1
         else
             echo " done"
         fi
         ;;
 
     restart)
         $0 stop
         sleep 1
         $0 start
         ;;
 
     reload)
         echo -n "Reload service $NAME... "
 
         if netstat -tnpl | grep -q nginx; then
             $NGINX_BIN -s reload
             echo " done"
         else
             echo "$NAME is not running, can't reload."
             exit 1
         fi
         ;;
 
     configtest)
         echo -n "Test $NAME configure files... "
 
         $NGINX_BIN -t
         ;;
 
     *)
         echo "Usage: $0 {start|stop|force-quit|restart|reload|status|configtest}"
         exit 1
         ;;
 
 esac
```

3.修改脚本权限 

```
chmod a+x /etc/init.d/nginx
```

4.注册成服务

```
chkconfig --add nginx
```

5.设置开机启动

```
chkconfig nginx on
```

这样就可以在任意目录通过service启动、关闭nginx

```
[root@host ~]# service nginx start
[root@host ~]# service nginx stop
[root@host ~]# service nginx restart
```

#### 配置nginx

进入nginx下的conf，用编译器打开配置文件nginx.conf，这里下载到本地用sublime打开，方便修改，一般都是修改server里面的内容

```
cd /usr/local/nginx/conf
```

前后端分离，Ajax api接口跨域，node代理和nginx代理

这个博客数据请求服务是用node.js写的，会出现跨域，本地测试的时候在build/config/index.js加入以下语句实现代理 

node服务器，在本地测试的时候用的跨域设置 

![](https://ws3.sinaimg.cn/large/0069RVTdgy1fuvn0yzxwyj30fm0b73yj.jpg)

1.`比如``ajax``接口"/getArticle"``，本地”localhost：8080/getArticle"``，有接口跨域的问题。`

2.localhost:8080 `=> http:`//localhost:3000/api，本地开启服务器实现代理。

在nginx服务器配置文件nginx.conf：

```
http {
    server {
        listen  80; #本地端口
        server_name localhost;
        location  ~*/get*|post* { #nginx正则匹配(可高度自定义)
            proxy_pass http://127.0.0.1:3000; #反向代理         
        }
    }
}
```

比如ajax接口"/getArticle"，本地"localhost：80/getArticle"，有跨域的问题。

localhost:80 => http://127.0.0.1:3000 本地开启服务器实现代理

nginx反向代理更多配置查询http://www.nginx.cn/115.html

注意nginx上写发与本地测试不一样，本地代理服务失效，如果直接location 下写，还是无法通过api获取数据库数据，因为找不到地址，

![](https://ws4.sinaimg.cn/large/0069RVTdgy1fuvn6q9cvoj30dj041745.jpg)

![](https://ws2.sinaimg.cn/large/0069RVTdgy1fuvn6zfcskj30pn02pjrg.jpg)

因此要对location匹配正则，获得正确地址

```
location  ~*/get*|post* {
    proxy_pass http://127.0.0.1:3000;
}
//也可以这么写
location /api
```

向后端express, api请求数据

#### HTML 5 History模式

`vue-router` 默认 hash 模式 —— 使用 URL 的 hash 来模拟一个完整的 URL，于是当 URL 改变时，页面不会重新加载。

相当于请求的是虚拟地址，需要后台设置nginx支持，即就是将所有的前端目录下的请求，统统传到前端index文件上，做路由分发。

这个vue项目使用了HTML 5 History 模式，为了把url的”#"去掉，即在路由设置history模式 

![](https://ws3.sinaimg.cn/large/0069RVTdgy1fuvn7lcgmij308y0350sk.jpg)

因此会遇到问题，进入首页的时候可以正常访问，一旦在浏览器地址栏输入其他地址或者刷新就会出现404问题，因为vue路由编译的dist并没有真正把资源提供给nginx或者其他服务器，所以，需要转交vue-router来做前端路由 

所以还需要向nginx添加 

```
location / {
    try_files $uri $uri/ /index.html
}
```

这个也是来自VUE官方文档，对nginx的配置，其他类型服务器也有对应设置

[VUE H5 History配置](https://router.vuejs.org/zh/guide/essentials/history-mode.html#%E5%90%8E%E7%AB%AF%E9%85%8D%E7%BD%AE%E4%BE%8B%E5%AD%90)

至此整个部署已经完毕，nginx.conf设置server部分如下

```
server {
        listen 80;
        server_name  ewanzheng.top;
        charset utf-8;
        root /usr/local/nginx/html/yublog/dist;#上传后放在服务器上的项目地址
        index index.html index.htm;

        location  ^~/api {
            proxy_pass http://127.0.0.1:3000;
        }
        location / {
            try_files $uri $uri/ /index.html;
            proxy_redirect    off;
            proxy_set_header  Host              $http_host;
            proxy_set_header  X-Real-IP         $remote_addr;
            proxy_set_header  X-Forwarded-For   $proxy_add_x_forwarded_for;
            #proxy_set_header  X-Forwarded-Proto $scheme;
            #proxy_set_header X-Nginx-Proxt true;
            error_page 404 /index.html;
        }
        error_page   500 502 503 504 /50x.html;
    }
```

重启服务器，页面就可以正常访问了，API也能正常拿到

参考资料

[vue+node部署上线](http://cl8023.com/#/blog/article/2/vue+node%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E4%BB%8E%E9%9B%B6%E5%BC%80%E5%A7%8B%E5%88%B0%E9%83%A8%E7%BD%B2%E4%B8%8A%E7%BA%BF%EF%BC%88%E9%83%A8%E7%BD%B2%E4%B8%8A%E7%BA%BF%EF%BC%89)

[前后端分离，ajax api接口跨域，node代理和nginx代理](https://segmentfault.com/a/1190000012960641)