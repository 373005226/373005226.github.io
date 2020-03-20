---
title: mysql配置
author: LY
date: 2020-01-12
summary: 重置mysql密码和设置远程连接
categories:
    - 工具
---



## linux服务器重置mysql命令：

1. 查看mysql运行状态

   ```
   ps -ef | grep -i mysql
   ```

2. vi /etc/my.cnf

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200110172922.png)

在这个地方添加

```
skip-grant-tables
```



3. 重启mysql服务器

```
systemctl restart mysqld.service
```

4. 执行mysql进去服务器

```
mysql
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200110173028.png)

5. 重置密码

```
update mysql.user set authentication_string=password('root_password') where user='root';
```

root_password替换成你想要的密码

6. vi /etc/my.cnf删除掉刚刚的那一行skip-grant-tables代码
7. 重启mysql服务器

```
systemctl restart mysqld.service
```



## mysql开启远程连接

1. 进入到服务器

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200110173028.png)

2. 执行命令

```
GRANT ALL PRIVILEGES ON *.* TO 'username'@'%' IDENTIFIED BY 'password' WITH GRANT OPTION;
```

username为用户名，password是密码（密码要特殊字符）

3. 执行命令

```
FLUSH PRIVILEGES;
```

就可以开启远程服务了

