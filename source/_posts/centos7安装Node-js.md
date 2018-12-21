---
title: centos7安装Node.js
tags:
  - Node.js
  - Linux
  - server
categories: 技术
abbrlink: 38165
date: 2018-09-01 16:23:15
---
服务器用的搬瓦工最低配，速度和性能都不错，还有优惠券折扣6%（自己Google找优惠券），支持支付宝付款，适合学习研究，满足大部分需求，这个博客系统的后台用的node.js来做接口，抓取数据；这次安装node的版本为v8.9.1，本地测试的版本v8.11.3



#### 之前的尝试

这个方法相对最简单，效率最高。这里还是先要吐槽一下，当我还没有找到这种方法，给折腾了两三天，又是安装gcc, gcc-c++,rpm，结果装完在安装node，又是报错C++ compiler too old, need g++ 4.9.4 or clang++ 3.4.2 (CXX=g++)，还说我的gcc版本太旧（rpm装的是gcc-5.1.1），手动下载gcc 7.3.0，编译，我哩个去还，编译的1个半小时不说，结果，还是报错了-_-|||，说我的swap太小，需要增加（当时用的是centos6），无奈之下增加swap，继续折腾，gcc版本还是不够，搞不定了，系统重装centos7，后来无意发现以下这样方法，赶紧记下来。

### 直接安装编译好的 Nodejs
### 下载对应的二进制文件

找到一个目录，存放下载安装文件，我这里是/download

```
wget -c https://nodejs.org/dist/v8.9.1/node-v8.9.1-linux-x64.tar.xz;
```
备注：如果没有找到wget命令，说明没有安装wget，终端输入`yum -y install wget`进行安装，wget依赖搞定



### 提取文件

```
tar -xvf node-v8.9.1-linux-x64.tar.xz
```
解压之后得到的文件夹就是已经安装好的 Nodejs 了，为了方便我们可以把它重命名一下,（在这里我把它放到了

用户根目录下面的 app 的文件夹里，你也可以换成其他路径）：

```
mv node-v8.9.1-linux-x64 ~/app/nodejs
```
这种方式安装的 Nodejs 并不完美，Nodejs 的命令 node 和 npm 并不能在全局使用。为了解决这个问题，我们需要建立两个软链接：

```
sudo ln -s ~/app/nodejs/bin/node /usr/local/bin/node
sudo ln -s ~/app/nodejs/bin/npm /usr/local/bin/npm
```
其中，~/app/nodejs 是我们刚才安装的 Nodejs 的路径，在建立软链接的时候要注意区别
现在，node 和 npm 可以在全局使用了，同样输入命令 node -v 来检查 Nodejs 是否安装成功

```
[root@host src]# node -v
v8.9.1
```
备注：如果遇到在 npm 下全局安装的模块无法直接在 bash 中执行。例如，我们要在 npm 中安装 yarn：

```
$ npm install -g yarn 
$ yarn 
bash: yarn: command not found...
```
为了解决这个问题，我们需要在 Linux 上手动配置环境变量。编辑 /etc/profile 文件：

```
sudo vi /etc/profile
```

在文件的底部，添加下面两行代码：

```
export NODE_HOME=~/app/nodejs/bin
export PATH=$NODE_HOME:$PATH

```
```
:wq```
```

保存退出

reboot一下系统，npm 全局安装的模块也可以使用了：

```
$ yarn yarn install v1.3.2 
info No lockfile found. 
[1/4] Resolving packages...
[2/4] Fetching packages... 
[3/4] Linking dependencies... 
[4/4] Building fresh packages... info Lockfile not saved, no dependencies. 
Done in 0.13s.```
```

输入`node -v`测试是否成功安装：

```
[root@host ~]# node -v
v8.9.1
```
至此，Nodejs 的安装已经顺利完成