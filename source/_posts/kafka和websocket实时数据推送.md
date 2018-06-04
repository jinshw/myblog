---
title: kafka和websocket实时数据推送
date: 2018-06-04 14:45:19
tags: [kafka,websocket]
---

# kafka和websocket实时数据推送

## 需求

​	已有Kafka服务，通过kafka服务数据（GPS）落地到本地磁盘（以文本文件存储）。现要根据echarts实现一个实时车辆的地图。

## 分析

1. 前端实时展现：使用websocket技术，实现服务器端数据推送到前端展现
2. 通过Java的kafka client端获取数据，并且通过websock推送到前端。

<!--more-->

## websocket

### 简介

​	websocket是HTML5开始提供的一种在单位TCP连接上进行全双工通讯的协议。在websocket api中，浏览器和服务器只需要做一次握手的动作，然后浏览器和服务器之间就形成了一条快速通道。两者之间就可以数据互相传送。

### 开发

* 服务器端

~~~java
  package com.ykkj.weiyi.socket;
  import org.springframework.stereotype.Component;
  import org.springframework.web.socket.server.standard.SpringConfigurator;
  import javax.websocket.*;
  import javax.websocket.server.ServerEndpoint;
  import java.io.IOException;
  import java.util.concurrent.CopyOnWriteArraySet;

  /**
   * @ServerEndpoint 注解是一个类层次的注解，它的功能主要是将目前的类定义成一个websocket服务器端,
   * 注解的值将被用于监听用户连接的终端访问URL地址,客户端可以通过这个URL来连接到WebSocket服务器端
   */
  @ServerEndpoint(value = "/websocket")
  public class CommodityServer {
      //静态变量，用来记录当前在线连接数。应该把它设计成线程安全的。
      private static int onlineCount = 0;

      //concurrent包的线程安全Set，用来存放每个客户端对应的MyWebSocket对象。若要实现服务端与单一客户端通信的话，可以使用Map来存放，其中Key可以为用户标识
      public static CopyOnWriteArraySet<CommodityServer> webSocketSet = new CopyOnWriteArraySet<CommodityServer>();

      //与某个客户端的连接会话，需要通过它来给客户端发送数据
      private Session session;

      /**
       * 连接建立成功调用的方法
       *
       * @param session 可选的参数。session为与某个客户端的连接会话，需要通过它来给客户端发送数据
       */
      @OnOpen
      public void onOpen(Session session) {
          this.session = session;
          webSocketSet.add(this);     //加入set中
          addOnlineCount();           //在线数加1
          System.out.println("有新连接加入！当前在线人数为" + getOnlineCount());
      }

      /**
       * 连接关闭调用的方法
       */
      @OnClose
      public void onClose() {
          webSocketSet.remove(this);  //从set中删除
          subOnlineCount();           //在线数减1
          System.out.println("有一连接关闭！当前在线人数为" + getOnlineCount());
      }

      /**
       * 收到客户端消息后调用的方法
       *
       * @param message 客户端发送过来的消息
       * @param session 可选的参数
       */
      @OnMessage
      public void onMessage(String message, Session session) {
          System.out.println("来自客户端的消息:" + message);
          //群发消息
          for (CommodityServer item : webSocketSet) {
              try {
                  item.sendMessage(message);
              } catch (IOException e) {
                  e.printStackTrace();
                  continue;
              }
          }
      }

      /**
       * 发生错误时调用
       *
       * @param session
       * @param error
       */
      @OnError
      public void onError(Session session, Throwable error) {
          System.out.println("发生错误");
          error.printStackTrace();
      }

      /**
       * 这个方法与上面几个方法不一样。没有用注解，是根据自己需要添加的方法。
       *
       * @param message
       * @throws IOException
       */
      public void sendMessage(String message) throws IOException {
          this.session.getBasicRemote().sendText(message);
          //this.session.getAsyncRemote().sendText(message);
      }

      public static synchronized int getOnlineCount() {
          return onlineCount;
      }

      public static synchronized void addOnlineCount() {
          CommodityServer.onlineCount++;
      }

      public static synchronized void subOnlineCount() {
          CommodityServer.onlineCount--;
      }
  }
~~~

  ​

* 前端

  ~~~html
  <!DOCTYPE html>
  <html>
  <head>
      <title>Java后端WebSocket的Tomcat实现</title>
  </head>
  <body>
  Welcome<br/><input id="text" type="text"/>
  <button onclick="send()">发送消息</button>
  <hr/>
  <button onclick="closeWebSocket()">关闭WebSocket连接</button>
  <hr/>
  <div id="message"></div>
  </body>

  <script type="text/javascript">
      var websocket = null;
      //判断当前浏览器是否支持WebSocket
      if ('WebSocket' in window) {
          websocket = new WebSocket("ws://localhost:8081/onepic/websocket");
      }
      else {
          alert('当前浏览器 Not support websocket')
      }

      //连接发生错误的回调方法
      websocket.onerror = function () {
          setMessageInnerHTML("WebSocket连接发生错误");
      };

      //连接成功建立的回调方法
      websocket.onopen = function () {
          setMessageInnerHTML("WebSocket连接成功");
      }

      //接收到消息的回调方法
      websocket.onmessage = function (event) {
          setMessageInnerHTML(event.data);
      }

      //连接关闭的回调方法
      websocket.onclose = function () {
          setMessageInnerHTML("WebSocket连接关闭");
      }

      //监听窗口关闭事件，当窗口关闭时，主动去关闭websocket连接，防止连接还没断开就关闭窗口，server端会抛异常。
      window.onbeforeunload = function () {
          closeWebSocket();
      }

      //将消息显示在网页上
      function setMessageInnerHTML(innerHTML) {
          document.getElementById('message').innerHTML += innerHTML + '<br/>';
      }

      //关闭WebSocket连接
      function closeWebSocket() {
          websocket.close();
      }

      //发送消息
      function send() {
          var message = document.getElementById('text').value;
          websocket.send(message);
      }
  </script>
  </html>
  ~~~




### 测试

  ![](\images\kafka\websocket.gif)



### 注意点

1. `webSocketSet`设置为全局静态变量，为其他类提供调用

   ```java
   public static CopyOnWriteArraySet<CommodityServer> webSocketSet = new CopyOnWriteArraySet<CommodityServer>();
   ```

2. 服务端都是用注解实现的 **@ServerEndpoint @OnOpen @OnClose @OnMessage @OnError**

3. **Tomcat7.0.47**以上版本支持websocket1.0

4. pom中添加jar支持

   ~~~xml
   <dependency>
       <groupId>javax</groupId>
       <artifactId>javaee-api</artifactId>
       <version>7.0</version>
       <scope>provided</scope>
   </dependency>
   ~~~




## Kafka 

### 简介

​	Kafka是一个分布式的、可分区的、可复制的**消息系统**。

### 开发

​	window系统搭建kafka环境，请参照我的《 [kafka环境搭建(windows)](https://jinshw.github.io/2018/06/01/kafka%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA-windows/#more) 》笔记

* kafka client for java

~~~java
package com.ykkj.weiyi.socket;

import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.KafkaConsumer;

import java.io.IOException;
import java.util.Arrays;
import java.util.Properties;
import static com.ykkj.weiyi.socket.CommodityServer.webSocketSet;

public class ConsumerKafka extends Thread {
    private KafkaConsumer<String, String> consumer;
    private String topic = "test.topic";
    public ConsumerKafka() {
    }

    @Override
    public void run() {
        //加载kafka消费者参数
        Properties props = new Properties();
        props.put("bootstrap.servers", "localhost:9092");
        props.put("group.id", "ytna");
        props.put("enable.auto.commit", "true");
        props.put("auto.commit.interval.ms", "1000");
        props.put("session.timeout.ms", "15000");
        props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        //创建消费者对象
        consumer = new KafkaConsumer<String, String>(props);
        consumer.subscribe(Arrays.asList(this.topic));
        //死循环，持续消费kafka
        while (true) {
            try {
                //消费数据，并设置超时时间
                ConsumerRecords<String, String> records = consumer.poll(100);
                //Consumer message
                for (ConsumerRecord<String, String> record : records) {
                    //Send message to every client
                    for (CommodityServer webSocket : webSocketSet) {
                        webSocket.sendMessage(record.value());
                    }

                }
            } catch (IOException e) {
                System.out.println(e.getMessage());
                continue;
            }
        }
    }

    public void close() {
        try {
            consumer.close();
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }

    //供测试用，若通过tomcat启动需通过其他方法启动线程
    public static void main(String[] args) {
        ConsumerKafka consumerKafka = new ConsumerKafka();
        consumerKafka.start();
    }
}

~~~

注意**topic** 和 **bootstrap.servers**配置

* 调用类

~~~java
  package com.ykkj.weiyi.socket;

  public class RunThread {
      public RunThread() {
          ConsumerKafka kafka = new ConsumerKafka();
          kafka.start();
      }
  }
~~~

* web.xml 配置

~~~xml
 <listener>
	<listener-class>com.ykkj.weiyi.socket.RunThread</listener-class>
 </listener>
~~~



### 测试

![](\images\kafka\kafkawebsocket.gif)

### 注意点

* ConsumerKafka 需要在web.xml中配置监听，不然在ConsumerKafka 类中不能获取**webSocketSet**变量
* 引用webSocketSet变量方式`import static com.ykkj.weiyi.socket.CommodityServer.webSocketSet;`
* 注意**topic** 和 **bootstrap.servers**配置



## 结束语

这里只是做了技术验证，还没有真正实现。真实场景下，kafka获取到的数据需要进行数据清洗，把不符合当前规范的数据清洗掉，并且按照前端展示需要的格式组装数据。

## 参考网上资料

https://blog.csdn.net/lw_ghy/article/details/73252904

https://blog.csdn.net/liu857279611/article/details/70157012