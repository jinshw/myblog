---
title: Mysql安装
date: 2017-08-15 17:29:26
tags: [数据库]
---


# MySQL5.7.19安装

**windows安装**

* 在D:\tools\mysql\mysql-5.7.19-winx64 下创建my.ini文件：

```
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8 
[mysqld]
#设置3306端口
port = 3306 
# 设置mysql的安装目录
basedir=D:\\tools\mysql\\mysql-5.7.19-winx64
# 设置mysql数据库的数据的存放目录
datadir=D:\\tools\\mysql\\mysql-5.7.19-winx64\\data
# 允许最大连接数
max_connections=200
# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
```
<!--more-->

* 在系统的环境变量path中添加D:\tools\mysql\mysql-5.7.19-winx64 \bin

* 使用管理员权限cmd进入 D:\tools\mysql\mysql-5.7.19-winx64 \bin 文件夹下

* 运行 mysqld -install   , 提示Service successfully installed.信息就是安装成功

  ![](/images/mysql/1.png)

* 执行 mysqld --initialize 初始化数据

* 在data/***********.err 文件中找root的初始密码, 执行 mysql -uroot -p命令，

* 执行 SET PASSWORD='root'; 修改密码


![](/images/mysql/2.png)

![](/images/mysql/3.png)

------

数据恢复：

* 把MySQL服务停止
* 拷贝data文件夹中相应的数据库文件夹和ibtmp1文件，覆盖已经安装好的data文件夹中


