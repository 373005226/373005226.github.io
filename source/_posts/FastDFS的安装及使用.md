---
title: fastDFS安装及在Django中的部署
author: LY
date: 2019-09-25
summary: FastDFS是一个开源的轻量级分布式文件系统，可以解决大容量存储和负载均衡的问题。这里讲解FastDFS的安装和使用
categories:
    - 后端
tags:
    - 分布式
    - Django
---



## FastDFS在阿里云服务器的安装和配置

FastDFS安装在这篇[博客](https://blog.csdn.net/busishenren/article/details/83584885)讲解的很清楚了，这里我就不再赘述，只作为补充

如果你是在轻量云服务器安装的话，方法跟esc服务器一样的，直接在控制台开启防火墙规则端口

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/15.png)

开启了防火墙端口还不够

在进入服务器下，使用这条命令查看防火墙的状态

```
firewall-cmd --state
```

如果防火墙已开启，则看开放的所有端口

```
firewall-cmd --zone=public --list-ports
```

如果防火墙关闭，那么开启防火墙

```
systemctl start firewalld.service
```

如果嫌麻烦就把防火墙关了也行，但是不建议这么做



查看开放的所有端口后，如果没有开启22122和23000端口，那么开启他

```
firewall-cmd --zone=public --add-port=22122/tcp --permanent #开启22122端口
firewall-cmd --zone=public --add-port=23000/tcp --permanent #开启23000端口
firewall-cmd --reload   # 配置立即生效
```

再一次查看端口是否开启

```
firewall-cmd --zone=public --list-ports
```



在部署完FastDFS并且开启storage和tracker服务后，看一下端口是否被监听

```
netstat -lnpt
```

能看到23000和22122端口被监听那么就说明成功

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/16.png)



## FastDFS在Django中的使用

在Django中，所有资源都是来自于后台的部署，Django后台数据库采用的是ORM模式，Django默认文件的上传是上传到指定的文件夹下面的，如果要修改的话要在一个文件中自定义文件的存储类，也就是继承django.core.files.storage.Storage

关于文件存储官网文档有很清楚的描述

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/17.png)

在setting.py配置你服务器的fastDFS的ip和端口号

```
# 设置Django的文件存储类
DEFAULT_FILE_STORAGE = 'utils.fdfs.storage.FDFSStorage'

# 设置fdfs使用的client.conf文件路径
FDFS_CLIENT_CONF = './utils/fdfs/client.conf'

# 设置fdfs存储服务器上nginx的IP和端口号
FDFS_URL = ''
```

然后在一个单独的文件夹(比如utils)里面加入以下两个文件

storage.py

```
from django.core.files.storage import Storage
from django.conf import settings
from fdfs_client.client import *


class FDFSStorage(Storage):
    def __init__(self, client_conf=None, base_url=None):
        """初始化"""
        if client_conf is None:
            client_conf = settings.FDFS_CLIENT_CONF
        self.client_conf = client_conf

        if base_url is None:
            base_url = settings.FDFS_URL
        self.base_url = base_url

    def _open(self, name, mode='rb'):
        # 打开文件时使用
        pass
    def _save(self, name, content):
        client=Fdfs_client('./utils/fdfs/client.conf')

        res = client.upload_by_buffer(content.read())
        if res.get('Status') != 'Upload successed.':
            raise Exception('上传文件到FastDFS失败')

        filename = res.get('Remote file_id')

        return filename

    def exists(self, name):
        """Django判断文件名是否可用"""
        return False

    def url(self, name):
        """返回访问文件url路径，不然后台保存的时候会报错"""
        return self.base_url + name
```

client.conf(修改相应的字段)

```
# connect timeout in seconds
# default value is 30s
connect_timeout=30

# network timeout in seconds
# default value is 30s
network_timeout=60

# the base path to store log files
# 你的个人电脑的存储日志，必须要有这个路径
base_path=/Users/LY/Python_log

# tracker_server can ocur more than once, and tracker_server format is
#  "host:port", host can be hostname or ip address
tracker_server=你服务器的IP:22122

#standard log level as syslog, case insensitive, value list:
### emerg for emergency
### alert
### crit for critical
### error
### warn for warning
### notice
### info
### debug
log_level=info

# if use connection pool
# default value is false
# since V4.05
use_connection_pool = false

# connections whose the idle time exceeds this time will be closed
# unit: second
# default value is 3600
# since V4.05
connection_pool_max_idle_time = 3600

# if load FastDFS parameters from tracker server
# since V4.05
# default value is false
load_fdfs_parameters_from_tracker=false

# if use storage ID instead of IP address
# same as tracker.conf
# valid only when load_fdfs_parameters_from_tracker is false
# default value is false
# since V4.05
use_storage_id = false

# specify storage ids filename, can use relative or absolute path
# same as tracker.conf
# valid only when load_fdfs_parameters_from_tracker is false
# since V4.05
storage_ids_filename = storage_ids.conf


#HTTP settings
http.tracker_server_port=80

#use "#include" directive to include HTTP other settiongs
##include http.conf
```



文件上传的路径可以随便写，因为这个都没用，上传后Django会自动按照你自己写的自定义存储方法去上传文件