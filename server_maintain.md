# vultr的一次配置过程

## web可视化界面新建服务器实例
1. 选择区域
2. 选择系统
3. 开始创建

## ssh登陆进行配置
可以在管理界面通过view console进行登陆配置，但是不太方便，这里我们通过ssh客户端进行远程配置。

windows端工具有ssh client或者putty，window10已经集成了ssh client。

`如果ssh登陆不上可能是ssh服务未开启，或者是tcp被墙，ping通当时ssh或者ss连接失败，通过telnet进行端口测试，如果都连接不上，说明目标地址tcp不可用，地址被墙了`
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

## 登陆服务器后配置ss
