---
title: Elasticsearch 安装
date: 2017-12-08 15:58:00
tags: [elasticsearch]
---
# Elasticsearch 安装

## 下载
    https://www.elastic.co/cn/downloads
<!-- more -->

# kibana安装
## 下载
    https://www.elastic.co/cn/downloads
    保存与elasticsearch版本一致
## 配置
    编辑config/kibana.yml文件，添加以下内容：
    elasticsearch.url: "http://localhost:9200"

## 访问
    查看启动小黑屏，http://localhost:5601


## 阮一峰教程
http://www.ruanyifeng.com/blog/2017/08/elasticsearch.html





# elasticsearch-head 插件安装(可选)
* 下载：
    git clone https://github.com/mobz/elasticsearch-head.git
* 安装：npm install
* 运行：npm run start

浏览器访问 http://127.0.0.1:9100/

```
elasticsearch-head 不能连接到elasticsearch服务，因为跨域
```

## 配置
编辑elasticsearch/config/elasticsearch.yml文件，添加以下内容：  
    http.cors.enabled: true
    http.cors.allow-origin: "*"

![upload successful](/images/elasticsearch/es.png)
![upload successful](/images/elasticsearch/es-head.png)


