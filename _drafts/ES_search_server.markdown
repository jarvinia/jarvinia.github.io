---
layout: post
title:  "搭建ElasticSearch"
date:   2015-07-09 14:46:34
categories: ElasticSearch Mongodb
comments: true
facebook: true
tags: Chinese
---

###准备工作
- 安装ElasticSearch
  - [安装步骤参考](https://www.elastic.co/guide/en/elasticsearch/reference/1.4/setup-repositories.html)
  - 插件：
    - [Mapper attachments Apache Tika](https://github.com/elasticsearch/elasticsearch-mapper-attachments)
    `$ES_HOME/bin/plugin install elasticsearch/elasticsearch-mapper-attachments/2.4.3`
    - [ES 'river' for Mongodb](https://github.com/richardwilly98/elasticsearch-river-mongodb)
    `$ES_HOME/bin/plugin --install com.github.richardwilly98.elasticsearch/elasticsearch-river-mongodb/2.0.9`

安装完成以后，重新启动ES service。

