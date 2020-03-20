---
title: redis开启远程连接
author: LY
date: 2020-01-12
summary: 配置linux服务器redis的远程连接
categories:
    - 工具

---



## redis配置远程连接

1. 安装好redis后，进入安装目录

   ```
   vim redis.conf
   ```



2. 注释掉bind 127.0.0.1

   ![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200110174709.png)

   然后把这个改成yes

   ![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200110174647.png)

   把protected-mode 改成no

   ![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200110174744.png)

   通过文件的位置去重启

   ```
   redis-server  /root/redis-4.0.6/redis.conf
   ```

   ​	然后就看看能不能用密码进去

   ![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200110175424.png)

   ​	如果成功后就可以在windows上进行远程连接了

