---
title: Oracle11g远程访问配置笔记
date: 2018-06-20 11:04:44
tags: [oracle]
---
# 安装ORACLE服务
    参照网上安装教程，例如：
    https://jingyan.baidu.com/article/a501d80c30f29eec620f5e63.html

# 修改ORACLE服务器端配置文件

* 查看监听服务位置

  ![1529464835789](\images\oracle\1529464835789.png)

<!--more-->

* 修改listener.ora和tnsnames.ora文件（安装自己安装的路径，我的路径C:\app\Administrator\product\11.2.0\dbhome_2\NETWORK\ADMIN）

  ![1529465023509](\images\oracle\1529465023509.png)

* 把上一步两个文件中的HOST改为服务器端IP，例如我修改如下：

  ![1529465286491](\images\oracle\1529465286491.png)
  ![1529465320667](\images\oracle\1529465320667.png)	

  **文件修改完毕后，需要重新启动监听服务**

  ​

* 服务器端网络防火墙关闭（本地测试可以不注重安全，生成环境是需要配置防火墙，不可以关闭）

  ​


# 客户端配置

可以不配置



# 测试连接

* 客户端连接测试（toad）

![1529465891206](\images\oracle\1529465891206.png)



**权限赋权**（把sysdba赋值给system用户）
grant sysdba to system