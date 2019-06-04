<!-- TOC -->

- [vultr + ss 的配置过程](#vultr--ss-的配置过程)
    - [web可视化界面新建服务器实例](#web可视化界面新建服务器实例)
    - [ssh登陆](#ssh登陆)
    - [登陆服务器后配置ss](#登陆服务器后配置ss)
    - [查看easy_install和pip位置](#查看easy_install和pip位置)
    - [为pip3命令建立连接](#为pip3命令建立连接)
    - [pip3 install shadowsocks提示ssl错误](#pip3-install-shadowsocks提示ssl错误)
    - [查看openssl version的命令](#查看openssl-version的命令)
    - [更换3.6.2,下载python源码进行编译安装](#更换362下载python源码进行编译安装)
    - [查看bin目录下的python](#查看bin目录下的python)
    - [为pip3和python建立软连接](#为pip3和python建立软连接)
    - [测试python3和对应pip](#测试python3和对应pip)
    - [安装ss](#安装ss)
    - [总结](#总结)
- [ubuntu 代理服务器搭建(squid)](#ubuntu-代理服务器搭建squid)
    - [系统和工具说明](#系统和工具说明)
    - [安装squid](#安装squid)
    - [查看安装和运行状态](#查看安装和运行状态)
    - [配置授权认证](#配置授权认证)
    - [编辑配置文件](#编辑配置文件)
    - [重新启动](#重新启动)

<!-- /TOC -->

# vultr + ss 的配置过程
## web可视化界面新建服务器实例
1. 选择区域
2. 选择系统
3. 开始创建

## ssh登陆
可以在管理界面通过view console进行登陆配置，但是不太方便，这里我们通过ssh客户端进行远程配置。

windows端工具有ssh client或者putty，window10已经集成了ssh client。

`如果ssh登陆不上可能是ssh服务未开启，或者是tcp被墙，ping通但是ssh或者ss连接失败，通过telnet进行端口测试，如果都连接不上，说明目标地址tcp不可用，地址被墙了`
```
C:\Users\XXX>ssh root@149.28.12.167
root@149.28.12.167's password:
Last login: Sun Jan  6 12:24:25 2019 from 106.2.234.179

```

一般服务器默认开启了ssh服务，如果连接不上，则需要通过view console进行登陆然后配置开启ssh

查看是否安装ssh

    [root@vultr ~]# rpm -qa | grep ssh
    openssh-server-5.3p1-123.el6_9.x86_64
    libssh2-1.4.2-2.el6_7.1.x86_64
    openssh-5.3p1-123.el6_9.x86_64
    openssh-clients-5.3p1-123.el6_9.x86_64 

上述显示已经安装，如果没有安装通过如下指令进行安装

    yum install openssh-server

重启服务

    service sshd restart

其他相关指令

    service sshd start 启动服务
    service sshd stop 停止服务 
    netstat -antp | grep sshd 查看是否启动22端口

    yum install net-tools(如果没有netstat命令，通过此命令可以工具包安装)

## 登陆服务器后配置ss

github上还能找到的配置说明如下

    CentOS:
    yum install python-setuptools && easy_install pip
    pip install shadowsocks

看似简单，配置起来很大概率会失败，主要是系统和python版本等因素。我是用的centos6.x，内置有2.6的python，但是pip的高版本使用时要求python版本要在2.7以后。这里我直接安装python3.x，安装后自带pip,然后使用对应版本的pip进行shadowsocks的安装。我最先使用了当前最新版本3.7.2，下载源码，解压，编译，建立软连接，执行对应的pip install shadowsocks，但是报错，缺少ssl模块，查阅了资料最新版本ssl模块有一定修改，最后使用3.6.2的python,下述先说明了3.7.2安装过程发现的问题，从
`更换3.6.2,下载python源码进行编译安装`章节开始是一个完整的成功的安装配置过程。

## 查看easy_install和pip位置
安装完python3.x后自带了 setuptools和pip,不需要` yum install python-setuptools && easy_install pip`,如下可以查看相关位置。
```s
    [root@vultr ~]# find / -name easy_install*

    /usr/bin/easy_install-2.6
    /usr/bin/easy_install
    /usr/share/doc/python-setuptools-0.6.10/docs/easy_install.txt
    /usr/share/doc/python-setuptools-0.6.10/docs/build/html/_sources/easy_install.txt
    /usr/local/python3/bin/easy_install-3.7
    /usr/local/python3/lib/python3.7/site-packages/__pycache__/easy_install.cpython-37.opt-2.pyc
    /usr/local/python3/lib/python3.7/site-packages/__pycache__/easy_install.cpython-37.opt-1.pyc
    /usr/local/python3/lib/python3.7/site-packages/__pycache__/easy_install.cpython-37.pyc
    /usr/local/python3/lib/python3.7/site-packages/easy_install.py
    /usr/local/python3/lib/python3.7/site-packages/setuptools/command/__pycache__/easy_install.cpython-37.opt-2.pyc
    /usr/local/python3/lib/python3.7/site-packages/setuptools/command/__pycache__/easy_install.cpython-37.opt-1.pyc
    /usr/local/python3/lib/python3.7/site-packages/setuptools/command/__pycache__/easy_install.cpython-37.pyc
    /usr/local/python3/lib/python3.7/site-packages/setuptools/command/easy_install.py
    /usr/lib/python2.6/site-packages/easy_install.py
    /usr/lib/python2.6/site-packages/easy_install.pyo
    /usr/lib/python2.6/site-packages/easy_install.pyc
    /usr/lib/python2.6/site-packages/setuptools/command/easy_install.py
    /usr/lib/python2.6/site-packages/setuptools/command/easy_install.pyo
    /usr/lib/python2.6/site-packages/setuptools/command/easy_install.pyc

    [root@vultr ~]# find / -name pip3*
    /usr/local/python3/bin/pip3
    /usr/local/python3/bin/pip3.7
```

## 为pip3命令建立连接

    ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3

这样就可以使用pip3命令了，否则使用的pip命令默认连接的不是3.x，而是安装时配套的pip。

    [root@vultr Python-3.7.2]# pip3 -V
    pip 18.1 from /usr/local/python3/lib/python3.7/site-packages/pip (python 3.7)

## pip3 install shadowsocks提示ssl错误
python3和pip都有了，就使用如下指令安装

    pip3 install shadowsocks

这里如果没有错误提示，你就安装成功了(可以直接去`安装ss`章节进行参考配置)，这里是会报一个ssl相关错误，网上有很多帖子进行如下指导：

    cd Python-3.7.2
    ./configure --with-ssl
    make
    sudo make install
    .....
    .....
    Installing collected packages: setuptools, pip
    Successfully installed pip-18.1 setuptools-40.6.2

但如果细心观察输出会发现提示configure的时候，--with-ssl不识别 ?什么鬼，网上都是这个写法？
make && make install后也有如下提示

    Could not build the ssl module!
    Python requires an OpenSSL 1.0.2 or 1.1 compatible libssl with X509_VERIFY_PARAM_set1_host().
    LibreSSL 2.6.4 and earlier do not provide the necessary APIs, https://github.com/libressl-portable/portable/issues/381
    
3.7要求更高版本的ssl,然而yum自动升级只能到1.0.1，所以需要下载源码编译生成高版本，根据网上记录，依然会有坑，暂时放弃3.7.2版本，换个稍微低点版本的python。最终选择3.6.2进行重装。

## 查看openssl version的命令

    [root@vultr Python-3.7.2]# openssl version
    OpenSSL 1.0.1e-fips 11 Feb 2013

## 更换3.6.2,下载python源码进行编译安装

wget下载源码

    wget http://www.python.org/ftp/python/3.6.2/Python-3.6.2.tgz

解压

    tar -xvzf Python-3.6.2.tgz

cd 进入源码目录开始配置、编译

    ./configure(这里依然不支持--with-ssl，请问网上的帖子都是怎么来的？)
    make
    make install

上述三条指令成功的执行需要一些依赖，比如编译环境，依赖包等等，根据提示可以找到解决方案，一般在confiure之前先yum install一些依赖包，比如gcc,ssl等模块。

## 查看bin目录下的python

    [root@vultr ~]# ls -al /usr/bin | grep python
    lrwxrwxrwx   1 root root         27 Jan  7 07:22 pip3 -> /usr/local/python3/bin/pip3
    lrwxrwxrwx   1 root root         29 Jan  7 07:35 pip3.7 -> /usr/local/python3/bin/pip3.7
    lrwxrwxrwx   1 root root         22 Jan  7 05:31 python -> /usr/local/bin/python3
    lrwxrwxrwx   1 root root          6 Oct 16 22:46 python2 -> python
    -rwxr-xr-x   2 root root       4864 Aug 18  2016 python2.6
    -rwxr-xr-x   2 root root       4864 Aug 18  2016 python.bak

## 为pip3和python建立软连接
这里相当于给我们的python3.6.2和对应的pip建立一个自定义名称的快捷方式，我们可以在终端调用，并且不和系统原有的python2.x产生分歧。

    [root@vultr ~]# ln -s /usr/local/bin/pip3.6 /usr/bin/pip3.6
    [root@vultr ~]# ln -s /usr/local/bin/python3.6 /usr/bin/python3

这样我们可以在控制台使用python3和pip3.6两个命令。

## 测试python3和对应pip
    

    [root@vultr ~]# python3 -V
    
    [root@vultr ~]# pip3.6 -V
                    pip 9.0.1 from /usr/local/lib/python3.6/site-packages (python 3.6)


    
    [root@vultr ~]# ls -al /usr/bin/ |grep -E 'python|pip'
    -rwxr-xr-x   1 root root       2804 Jun  4  2014 lesspipe.sh
    -rwxr-xr-x   1 root root        278 Jan  7 02:32 pip
    -rwxr-xr-x   1 root root        280 Jan  7 02:32 pip2
    -rwxr-xr-x   1 root root        284 Jan  7 02:32 pip2.6
    lrwxrwxrwx   1 root root         21 Jan  7 08:56 pip3.6 -> /usr/local/bin/pip3.6            //我们使用的3.6版本
    lrwxrwxrwx   1 root root         29 Jan  7 07:35 pip3.7 -> /usr/local/python3/bin/pip3.7
    lrwxrwxrwx   1 root root          6 Oct 16 22:46 python2 -> python
    -rwxr-xr-x   2 root root       4864 Aug 18  2016 python2.6
    lrwxrwxrwx   1 root root         24 Jan  7 09:02 python3 -> /usr/local/bin/python3.6       //我们使用的3.6版本
    -rwxr-xr-x   2 root root       4864 Aug 18  2016 python.bak


## 安装ss
通过如下指令安装shadowsocks

    [root@vultr ~]# pip3.6 install shadowsocks

提示如下

    Collecting shadowsocks
    Downloading https://files.pythonhosted.org/packages/02/1e/e3a5135255d06813aca6631da31768d44f63692480af3a1621818008eb4a/shadowsocks-2.8.2.tar.gz
    Installing collected packages: shadowsocks
    Running setup.py install for shadowsocks ... done
    Successfully installed shadowsocks-2.8.2

    You are using pip version 9.0.1, however version 18.1 is available.
    You should consider upgrading via the 'pip install --upgrade pip' command.

如下若干种方式启动ss

    ssserver -p xxxx -k xxxx -m aes-256-cfb(前台启动)
    ssserver -p xxxx -k xxxx -m aes-256-cfb -d start(后台启动)
    
关闭ss服务的命令    

    ssserver -d stop

查看日志

    less /var/log/shadowsocks.log

通过配置文件配置参数并启动ss
```json
    vi /etc/shadowsocks.json

    {
    "server":"my_server_ip",
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"mypassword",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false
    }
```
    ssserver -c /etc/shadowsocks.json(前台启动)
    ssserver -c /etc/shadowsocks.json -d start(后台启动)
    ssserver -c /etc/shadowsocks.json -d stop(停止服务)

查看ssserver是否启动

    [root@vultr ~]# ps -ef | grep sserver
    root      6499     1  0 03:39 ?        00:00:00 /usr/local/bin/python3.6 /usr/local/bin/ssserver -c /etc/shadowsocks.json -d start
    root      6517  6431  0 03:40 pts/1    00:00:00 grep sserver


查看墙状态命令

    /etc/init.d/iptables status

关闭墙，这是centos6可用方法，7中有firewalld指令，但这里不识别

    /etc/init.d/iptables stop 

添加端口
    
    vi /etc/sysconfig/iptables

进行编辑，添加下面这一行
    
    -A INPUT -m state --state NEW -m tcp -p tcp --dport xxxx -j ACCEPT

重启防火墙
    
    /etc/init.d/iptables start



## 总结

1. 安装python，先不使用当前最新3.7，如选择3.6.2。python安装过程需要各种依赖，比如下载的包要解压，直接tar可能解压不了，缺少另一个工具xz;编译过程需要其他模块，这里有一系列的模块，网上查阅基本可以解决。
2. 使用pip install shadowsocks。注意对make install后得到的python3进行一定的处理，3.x大部分已经集成了pip，找到pip3.x的目录，对python3.x和pip3.x分别建立软连接，这样可以避免和内置的低版本的python或者pip冲突，直接执行即可安装。如果提示错误可能就是python的安装过程缺少模块，根据提示排查，重新configure,make，makeinstall。
3. ssserver -p xxxx -k xxxx -m aes-256-cfb -d start开启ss（也可以通过配置文件实现更为丰富的管理）
4. 端口进行防火墙白名单处理，具体防火墙操作根据系统会有差别。

</br></br>

# ubuntu 代理服务器搭建(squid)

## 系统和工具说明
1. 环境：Ubuntu Server 16.04.1 LTS 64位
2. 代理工具:安装squid，提供代理server的功能
3. 添加用户认证:通过htpasswd命令进行用户名密码配置

## 安装squid

    sudo apt-get install squid //安装squid
    sudo vi /etc/squid/squid.conf //编辑配置文件
    service squid restart  //重启服务

配置文件的修改如下:

    找到"http_access allow localhost"
    改为"http_access allow all",这样外网才能够访问到。

默认端口3128,配置文件中某一行指定，可以进行修改：

    http_port 3128

## 查看安装和运行状态

    xxx@VM-0-14-ubuntu:~$ which squid
    /usr/sbin/squid

    ubuntu@VM-0-14-ubuntu:~$ ps -aux | grep squid
    root      7752  0.0  0.7 109280  6312 ?        Ss   Jan16   0:00 /usr/sbin/squid -YC -f /etc/squid/squid.conf
    proxy     7754  0.0  3.1 151928 27852 ?        S    Jan16   0:16 (squid-1) -YC -f /etc/squid/squid.conf
    proxy     7755  0.0  0.1  13280  1476 ?        S    Jan16   0:00 (logfile-daemon) /var/log/squid/access.log
    ubuntu   10313  0.0  0.1  13232  1032 pts/1    S+   11:37   0:00 grep --color=auto squid

## 配置授权认证
上述操作后，可以通过ip和port进行代理使用，没有授权认证，不需要用户名和密码。
接下来进行相关配置，可以输入htpasswd查看是否有相关工具，没有的话根据提示安装。
这里控制台提示我用下述指令进行安装(关于这个安装指令，网上有很多版本，有些会提示找不到对应安装包)

    #安装工具包
    sudo apt install apache2-utils

    #添加一个账号 -c表示创建文件
    ubuntu@VM-0-14-ubuntu:~$ sudo htpasswd -b -c /etc/squid/passwd username1xxx user1pwd
    Adding password for user user1

    #添加另一个账号 前面已有文件，不在创建新文件，不使用-c
    ubuntu@VM-0-14-ubuntu:~$ sudo htpasswd -b /etc/squid/passwd username2xxx user2pwd
    Adding password for user user2

## 编辑配置文件

    #配置认证方式和认证文件
    auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
    
    #认证规则
    acl aclauth1 proxy_auth REQUIRED
    
    #允许对应规则(之前的http_access allow all不再使用)
    http_access allow aclauth1

## 重新启动

    sudo service squid stop
    sudo service squid start
