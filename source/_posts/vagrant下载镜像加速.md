---
title: vagrant
summary: 因为vagrant官网是在国外的，所以下载十分的慢，这里介绍怎么下载镜像来快速开发
date: 2020-4-26
author: LY
categories:
    - 工具
tags:
    - docker
    - 运维工具
---

> 为什么要采用Oracle VM VirtualBox+vagrant

> 因为Oracle VM VirtualBox+vagrant两个都是是免费的，而VMware本身是收费的，再加上vagrant的VMware也是收费的，不只是要支付一个费用，还要把组合的费用也给出了

## 下载镜像

首先是进入vagrant的官网

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200426095524.png)

搜索到自己的镜像之后，点击这个有镜像的版本

得到如下界面后

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200426095607.png)

看这个地址栏https://app.vagrantup.com/centos/boxes/7/versions/1905.1

然后对地址栏进行拼接，在后面加上`/providers/virtualbox.box`

即完整的地址栏是https://app.vagrantup.com/centos/boxes/7/versions/1905.1/providers/virtualbox.box

就可以下载到镜像了

此外也可以直接赋值`Vagrantfile`框框里面的配置，然后在本地取名为`Vagrantfile`，然后`vagrant up`也可以加载

当然还可以下载别人的`vagrant`的文件进行加载，比如

```shell
$ git clone https://github.com/coreos/coreos-vagrant.git
```

## 安装镜像

### 在线下载

即如果你的网络环境恰好是能访问国外的网站

那么直接关联到他的地址即可

比如：

```shell
$ vagrant box add centos/7 https://app.vagrantup.com/centos/boxes/7
```

然后就会出现

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200426100109.png)

然后选择自己需要方式的即可

反正我这里访问不了

### 本地加载

我是下载到本地进行加载

按照如上介绍的方法下载好镜像后

加载本地镜像：

--name 后面接的是镜像的名字，再后面接的是镜像的地址

```shell
vagrant box add --name centos/7 D:\software\CentOS-7-x86_64-Vagrant-1905_01.VirtualBox.box
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200426100421.png)

再输入

```shell
vagrant box list
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200426100617.png)

就可以看到镜像已经加载了

然后再进行初始化

```shell
vagrant init centos/7
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200426100854.png)

然后在这个文件下加载镜像即可

```
vagrant up
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200426101526.png)

然后执行命令ssh即可运行虚拟机了

```
vagrant ssh
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200426101703.png)

退出的命令如下

```
exit
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200426102120.png)

## vagrant其余命令

查看状态

```
vagrant status
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200426102224.png)

停止命令

```
vagrant halt
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200426102320.png)

删除命令

```
vagrant destroy
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200426102533.png)



```ruby
    $ vagrant init # 初始化
    $ vagrant up # 启动虚拟机
    $ vagrant halt # 关闭虚拟机
    $ vagrant reload # 重启虚拟机
    $ vagrant ssh # SSH 至虚拟机
    $ vagrant status # 查看虚拟机运行状态
    $ vagrant destroy # 销毁当前虚拟机
    $ vagrant suspend # 挂起虚拟机
    $ vagrant resume # 恢复挂起状态
    $ vagrant ssh-config # 输出ssh连接信息
    $ vagrant package # 打包当前运行的虚拟机的环境
    $ vagrant plugin # 用于安装卸载插件
    $ vagrant box list # 查看已添加镜像
    $ vagrant box remove centos/7 # 删除指定镜像
    $ vagrant box add "boxIdentity" # remoteUrlorLocalFile 添加box
```

> 此外推荐博客：
>
> https://www.jianshu.com/p/a6457bc5436b
>
> http://www.54php.cn/default/26.html
>
> [vagrant常见错误](https://blog.csdn.net/nie773546179/article/details/77964407)

最后在安装好docker之后执行如下命令，否则docker不能正常使用

```

su root # 先切换到root用户, 再执行以下命令
systemctl enable docker # 开机自动启动docker

systemctl start docker # 启动docker
systemctl restart docker # 重启dokcer
```

## 给用户添加使用docker权限

因为每个权限之前都要加上一个sudo ，这样比较麻烦

如这里还建议把当前的用户添加使用docker权限

### vagrant

```
sudo groupadd docker
sudo gpasswd -a vagrant docker
sudo service docker restart
exit	#这里需要先退出去
vagrant ssh	#然后重新登录才可以生效
```

### 云服务器

```
sudo su

sudo usermod -aG docker ${USER}
sudo service docker restart
```



## Vagrantfile添加命令

在Vagrantfile中可以添加ssh命令，比如

```shell
  config.vm.provision "shell", inline: <<-SHELL
    sudo yum install -y yum-utils
    sudo yum-config-manager  --add-repo  https://download.docker.com/linux/centos/docker-ce.repo
	sudo yum-config-manager --enable docker-ce-nightly
	sudo yum install docker-ce docker-ce-cli containerd.io
  SHELL
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200426210134.png)

这里的命令就是`docker`的`centos`版本的安装命令，只需要`Vagrant up`启动就会自动执行

## 结论

其实`vagrant`和`docker-machine`都是创建虚拟机的方式，只是`docker-machine`能快速创建一个带着`docker`的环境

此外如果是`vagrant`创建虚拟机，执行到这一步的时候就卡住的话

```shell
default: SSH auth method: private key 
```

这个不一定是什么虚拟化的问题，很可能只是硬盘虚拟化错误....多试几次就可以了

