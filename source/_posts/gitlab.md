---
title: gitlab部署与使用
author: LY
date: 2019-09-21
summary: Gitlab是一个类似于Github的分布式仓库管理系统，Gitlab一般用于企业开发，这里介绍Gitlab的安装、使用和优化
categories:
    - 自动化运维
tags:
    - gitlab
---



## gitlab的安装

gitlab和github的区别想必大家都懂，这里我就不再赘述

这里我来用阿里云学生服务器centos7来演示gitlab的安装过程

首先打开你的防火墙控制台，开启防火墙规则

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/%E6%88%AA%E5%9B%BE6.png)

**注(这里我用的是服务器安装，一般的服务器都默认安装了ssh的，服务器ssh这里可以输入命令也可以不用，如果是用虚拟机的就要一定要安装ssh，并且在命令前加上sudo)**

首先安装ssh，

```
yum install -y curl policycoreutils-pythonopenssh-server
```

将SSH服务设置成开机自启动，安装命令：

```
systemctl enable sshd
```

启动SSH服务，安装命令：

```
systemctl start sshd
```

安装防火墙：

```
yum install firewalld systemd -y
```

开启防火墙：

```
service firewalld  start
```

添加http服务到防火墙：

```
firewall-cmd --permanent --add-service=http
```

然后重启防火墙：

```
systemctl reload firewalld
```

安装Postfix以发送通知邮件：

```
yum install postfix
```

将postfix服务设置成开机自启动，安装命令：

```
systemctl enable postfix
```

启动postfix

```
systemctl start postfix
```

**注意：这里如果成功那么没啥，可能会出现以下这个问题**

`Job for postfix.service failed because the control process exited with error code. See "systemctl st`

这个时候不要慌

修改/etc/postfix/main.cf这个文件

```
vim /etc/postfix/main.cf
```

修改这两个字段为：

```
inet_interfaces = all
inet_protocols = ipv4
```



接下来添加gitlab镜像：(这里的我用的是centos7的，centos6的话另外找资料)

```
wget https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el7/gitlab-ce-10.0.0-ce.0.el7.x86_64.rpm
```

安装gitlab 安装命令：

```
rpm -i gitlab-ce-10.0.0-ce.0.el7.x86_64.rpm
```

**注意，这里可能出现如下错误**

`warning: gitlab-ce-10.0.0-ce.0.el7.x86_64.rpm: Header V4 RSA/SHA1 Signature, key ID f27eab47: NOKEY` 

这个是缺少gitlab 的依赖

下载依赖：

```
yum -y install policycoreutils openssh-server openssh-clients postfix
```

然后再执行：

```
rpm -i gitlab-ce-10.0.0-ce.0.el7.x86_64.rpm
```

然后修改gitlab的配置文件

```
vim  /etc/gitlab/gitlab.rb
```

找到external_url这个字段，把里面的值修改为`http://你的服务器ip:你的端口`，比如`http://36.102.3.26:8899`，然后保存退出

**这里注意啦！！！**

这里我用的是开启防火墙配置的，所以要先看看防火墙开启没有

```
firewall-cmd --state
```

如果没开启就开启防火墙：`systemctl stop firewalld.service`

然后看一下你有没有开启你配置的端口

```
firewall-cmd --zone=public --list-ports
```

如果没有就添加端口，并且重启

```
firewall-cmd --zone=public --add-port=8899/tcp --permanent   # 开放8899端口
firewall-cmd --reload   # 配置立即生效
```

然后执行命令

```
gitlab-ctl reconfigure
```

然后

```
gitlab-ctl restart
```

这样就可以通过你的服务器ip:你的端口就可以访问gitlab了，这个是成功画面

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/%E6%88%AA%E5%9B%BE7.png)

这个是要你重新配置你的密码，默认的账号是root



## 502错误解决和优化

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/%E6%88%AA%E5%9B%BE9.png)

**注意，第一时间是看错误日志，而不是立马去谷歌百度，因为每一个人的错误都是不同的**

第一时间要查看错误日志，而不是查百度，因为每个人的错误都不同

```
# 看看unicorn启动日志
cat /var/log/gitlab/unicorn/unicorn_stderr.log
# 然后再看看gitlab日志，看看是哪里出错
tail -100 /var/log/gitlab/gitlab-rails/production.log
```

**每个人的错误都不同，根据错误日志的问题再去百度谷歌才是正道理**

### 端口错误问题

**端口错误都是端口被占用，当然按照我上面的方法是不会出现这个情况的**

此时继续修改配置文件

```
vim  /etc/gitlab/gitlab.rb
```

**看看你的错误日志，我的是8080端口被占用，改端口就行了**

找到以下字段取消注释并且修改：

```
# unicorn['port'] = 8090   #千万不要是原来的8899端口啊，8090也是我打开过的端口，一样的话好像会报404的
```

### 内存爆满和内存消耗过大的问题

但是最大的问题就是内存爆满，用命令看看`free -h`,或者用`htop`没有htop命令就下载一下`yum install htop`

看一下结果我相信大部分人都跟我一样，物理内存爆满，然后发现swap居然是0有点奇怪

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/%E6%88%AA%E5%9B%BE10.png)

然后我去去谷歌找了一下原因，发现阿里云默认情况下是不启用swap的，所以我们要自己添加swap来解决

#### 使用swap分区来减少内存消耗

1. 查看当前系统中是否已经启用swap分区

```
cat /proc/swaps
```

2. 如果没有启用swap分区功能，则新建一个专门的文件用于swap分区

```
dd if=/dev/zero of=/data/swap bs=512 count=8388616
```

**swap的大小是count的大小乘以bs大小，上面命令的大小是4294971392，即4GB**

3. 通过mkswap命令将上面新建出的文件做成swap分区

```
mkswap /data/swap
```

4. 查看内核参数vm.swappiness中的数值是否为0，如果为0则根据实际需要调整成30或者60

```
cat /proc/sys/vm/swappiness
sysctl -w vm.swappiness=60
#永久修改
若想永久修改，则编辑/etc/sysctl.conf文件，改文件中有vm.swappiness变量配置，默认为0
```

**注：若想永久修改，则编辑/etc/sysctl.conf文件**

5. 启用此交换分区的交换功能

```
swapon /data/swap
echo “/data/swap swap swap defaults 0 0” >> /etc/fstab
```

6. 然后再看看swap分区是否启动

```
cat /proc/swaps 
```

7. 然后重启gitlab看看

```
gitlab-ctl restart
```



![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/%E6%88%AA%E5%9B%BE11.png)

哦吼，这不就，好起来了吗

再运行gitlab看看

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/%E6%88%AA%E5%9B%BE12.png)

运行速度如丝般顺滑，刚部署好不知道多少倍，除此之外可能还有其他的bug，但是这个看错误日志修改就好了

**注意：如果修改了配置文件，一定要执行以下命令**

```
gitlab-ctl reconfigure
gitlab-ctl restart
```



![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/%E6%88%AA%E5%9B%BE8.png)

**如果配置了还是502，耐心等一等，等那么一分钟左右就好了**



## 问题总结

​	这个运行速度慢折腾了我一下午，问题归根结底就是.....服务器配置太烂了，性能差就会经常出现502，因为官方推荐的服务器性能是最低 双核 + 8GB RAM，所以这里只能使用swap才能正常使用

​	gitlab的使用方法是和github差不多的，可以用git命令行执行，也可以用sourcetree直接clone下来



**解决问题使用到的博客**

> [gitlab服务器经常出现502(阿里云搭建)](https://blog.csdn.net/qq_25283709/article/details/78207881)

> [阿里云云主机添加swap分区与swap性能优化](https://blog.51cto.com/dgd2010/1762907?spm=a2c4e.10696291.0.0.2d4919a42dBKR8)

> [gitlab占用内存过大](https://blog.csdn.net/wanchaopeng/article/details/84771195)

