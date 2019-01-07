# vultr的一次配置过程

## web可视化界面新建服务器实例
1. 选择区域
2. 选择系统
3. 开始创建

## ssh登陆进行配置
可以在管理界面通过view console进行登陆配置，但是不太方便，这里我们通过ssh客户端进行远程配置。

windows端工具有ssh client或者putty，window10已经集成了ssh client。

```
C:\Users\XXX>ssh root@149.28.12.167
root@149.28.12.167's password:
Last login: Sun Jan  6 12:24:25 2019 from 106.2.234.179

```

##查看easy_install和pip位置
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

这样就可以使用pip3命令了，否则使用的pip命令默认连接的不是3.x安装时配套的pip。

    [root@vultr Python-3.7.2]# pip3 -V
    pip 18.1 from /usr/local/python3/lib/python3.7/site-packages/pip (python 3.7)

## pip3 install shadowsocks提示ssl错误

    cd Python-3.7.2
    ./configure --with-ssl
    make
    sudo make install
    .....
    .....
    Installing collected packages: setuptools, pip
    Successfully installed pip-18.1 setuptools-40.6.2

configure的时候提示参数不识别?什么鬼，网上都是这个写法？
换另一种./configure --enable-optimizations

make && make install后出现了如下提示

    Could not build the ssl module!
    Python requires an OpenSSL 1.0.2 or 1.1 compatible libssl with X509_VERIFY_PARAM_set1_host().
    LibreSSL 2.6.4 and earlier do not provide the necessary APIs, https://github.com/libressl-portable/portable/issues/381
    
3.7要求更高版本的ssl,然而yum自动升级只能到1.0.1，所以需要下载源码编译生成高版本，根据网上记录，依然会有坑，暂时放弃3.7.2版本，换个稍微低点版本的python。

## 查看openssl version

    [root@vultr Python-3.7.2]# openssl version
    OpenSSL 1.0.1e-fips 11 Feb 2013

## 更换3.6.2

    wget http://www.python.org/ftp/python/3.6.2/Python-3.6.2.tgz
    tar -xvzf Python-3.6.2.tgz
    cd 进入目录
    ./configure(这里依然不支持--with-ssl，请问网上的帖子都是怎么来的？)

## 查看bin目录下的python

    [root@vultr ~]# ls -al /usr/bin | grep python
    lrwxrwxrwx   1 root root         27 Jan  7 07:22 pip3 -> /usr/local/python3/bin/pip3
    lrwxrwxrwx   1 root root         29 Jan  7 07:35 pip3.7 -> /usr/local/python3/bin/pip3.7
    lrwxrwxrwx   1 root root         22 Jan  7 05:31 python -> /usr/local/bin/python3
    lrwxrwxrwx   1 root root          6 Oct 16 22:46 python2 -> python
    -rwxr-xr-x   2 root root       4864 Aug 18  2016 python2.6
    -rwxr-xr-x   2 root root       4864 Aug 18  2016 python.bak

## 为pip3和python建立软连接

    [root@vultr ~]# ln -s /usr/local/bin/pip3.6 /usr/bin/pip3.6
    [root@vultr ~]# ln -s /usr/local/bin/python3.6 /usr/bin/python3
    [root@vultr ~]# python3 -V
    [root@vultr ~]# ip3.6 -V
                    pip 9.0.1 from /usr/local/lib/python3.6/site-packages (python 3.6)

## 安装ss

    [root@vultr ~]# pip3.6 install shadowsocks
    Collecting shadowsocks
    Downloading https://files.pythonhosted.org/packages/02/1e/e3a5135255d06813aca6631da31768d44f63692480af3a1621818008eb4a/shadowsocks-2.8.2.tar.gz
    Installing collected packages: shadowsocks
    Running setup.py install for shadowsocks ... done
    Successfully installed shadowsocks-2.8.2
    You are using pip version 9.0.1, however version 18.1 is available.
    You should consider upgrading via the 'pip install --upgrade pip' command.

    ssserver -p xxxx -k xxxx -m aes-256-cfb
    ssserver -p xxxx -k xxxx -m aes-256-cfb --user xxxx -d start
    ssserver -d stop
    less /var/log/shadowsocks.log

    /etc/init.d/iptables status(查看墙状态)
    /etc/init.d/iptables stop （关闭墙，这是centos6可用方法，7中有firewalld指令，但这里不识别）

    添加端口
    vi /etc/sysconfig/iptables
    进行编辑
    -A INPUT -m state --state NEW -m tcp -p tcp --dport xxxx -j ACCEPT

    重启防火墙
    /etc/init.d/iptables start