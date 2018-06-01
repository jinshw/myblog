---
title: kafka环境搭建(windows)
date: 2018-06-01 15:56:06
tags: [kafka,zookeeper]
---

# kafka环境搭建(windows)

原文引用：https://www.jianshu.com/p/f7037105db46

## zookeeper

* zookeeper

  下载地址：http://apache.fayea.com/zookeeper/current/

  ![1527839918487](\images\kafka\1527839918487.png)

<!--more-->

* 解压E:\study\kafka_work\zookeeper-3.4.12，创建`data`文件夹

  ![1527840168103](\images\kafka\1527840168103.png)

* 进入到conf目录，然后复制zoo_sample.cfg为zoo.cfg

  ![1527840259527](\images\kafka\1527840259527.png)

修改zoo.cfg中的`dataDir`

![1527840343846](\images\kafka\1527840343846.png)

* 启动Zookeeper，进入 **bin** 目录，执行 **zkServer.cmd**

  ![1527840452421](\images\kafka\1527840452421.png)



## Kafka

* 下载

  地址： http://kafka.apache.org/downloads.html

  ![1527840547143](\images\kafka\1527840547143.png)


* 解压，建立一个空文件夹 **logs**

  ![1527840597418](\images\kafka\1527840597418.png)

### 

* 进入config目录，编辑 **server.properties** 文件

  ![1527840638873](\images\kafka\1527840638873.png)



修改**log.dirs**

![1527840699576](\images\kafka\1527840699576.png)



## 运行

* cmd进入kafka文件夹，我的是E:\study\kafka_work\kafka_2.12-1.1.0，执行下面命令

  `.\bin\windows\kafka-server-start.bat .\config\server.properties`

![1527840858400](\images\kafka\1527840858400.png)

**注意**：如果遇到上图`无法加载主类`问题，请执行SET CLASSPATH=.;

## 测试

* cmd 进入**E:\study\kafka_work\kafka_2.12-1.1.0\bin\windows**目录，创建一个topic

  ~~~
  kafka-topics.bat --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test.topic
  ~~~

打开一个Producer
~~~
kafka-console-producer.bat --broker-list localhost:9092 --topic test.topic
~~~


  ![1527841299963](\images\kafka\1527841299963.png)



* 执行命令，打开生产者

  ~~~
  kafka-console-consumer.bat --zookeeper localhost:2181 --topic test.topic
  ~~~

  ![1527841450736](\images\kafka\1527841450736.png)



* 现在有4个cmd窗口，
  1. zookeeper
  2. kafka
  3. producer
  4. consumer
* 下面，我们在3号窗口(Producer)输入一些字符，就会看到4号窗口马上就收到消息

![1527841688385](\images\kafka\1527841688385.png)



## 注意事项

* 在搭建时如果提示: **错误: 找不到或无法加载主类 Files\Java\jdk1.8.0_141\lib\dt.jar;D:\Program**,请执行命令 **set classpath=.;**
* zookeeper 默认端口号为 **2181**
* kafka 默认端口号为 **9092**
* **produce**（发送消息者）启动时参数使用的是kafka的端口号（默认9092），而**consumer**（接收消息者）启动时使用的是zookeeper端口号（默认2181）

