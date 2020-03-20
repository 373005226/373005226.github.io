---
title: linux服务器安装node环境
date: 2020-01-12
author: LY
categories:
    - 工具
---

### centos7安装node环境

1. 下载源码安装包

   ```shell
      wget https://nodejs.org/dist/v10.13.0/node-v10.13.0-linux-x64.tar.xz
   ```

2. 解压文件夹

   ```shell
    tar -xvf node-v10.13.0-linux-x64.tar.xz
   ```

3. 移动文件夹目录

   ```shell
    mv node-v10.13.0-linux-x64 /usr/local/nodejs
   ```

4. 设置环境变量

   ```shell
   vim /etc/profile
   ```


   在最后面添加这两行代码

   ```shell
   export NODE_HOME=/usr/local/nodejs
   export PATH=$NODE_HOME/bin:$PATH
   ```

5. `:wq`保存后`source /etc/profile`

6. 配置成功，最后添加个cnpm 

   ```shell
    npm install -g cnpm --registry=https://registry.npm.taobao.org
   ```