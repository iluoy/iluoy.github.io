---
title: filebeat之kafka配置
date: 2020-12-30 14:06:15
tags: ["EFK","filebeat","日志收集"]
categories: ["EFK","filebeat"]
---

### 简介
`filebeat`支持将日志输出到`kafka`高性能消息中间件中

### kafka output配置
- enabled： 为`true`表示启用此`output`，为`false`表示禁用此`output`
- hosts：kafka broker列表，例如：["kafka-0:9092","kafka-1:9092","kafka-2:9092"]
- username：连接`kafka`的用户名，如果配置了此参数，则`password`也是必须的
- password：连接`kafka`的密码，与`username`一起
- topic：发送消息的`topic`名字，需要注意`kafka`在与`filebeat`使用时候不要关闭自动创建`topic`的功能
- topics：topic数组，支持条件匹配，也就是可以将满足条件的日志输出到对应到topic中
- key：设置消息key
- partition：设置消息传入的partition，默认是传入所有partition
- client\_id：设置客户端id
- worker：设置output worker个数
- codec：codec配置
- metadata：kafaka元数据配置，例如配置最大消息大小：max\_message\_bytes

> 参考：https://www.elastic.co/guide/en/beats/filebeat/master/kafka-output.html#_configuration_options_19

### 动态topic名字
动态topic可以通过所有output输出字段中获取，其中自定义output字段可以通过fields来进行配置，如下所示，配置的自定义fields为log\_topic，那么动态topic名字可以使用'%{[host.name]}'、'%{[fields.log\_topic]}'等


    {
        "@metadata": {
            "beat": "filebeat",
            "type": "_doc",
            "version": "7.10.1"
        },
        "@timestamp": "2020-12-30T06:36:20.922Z",
        "agent": {
            "ephemeral_id": "3b070191-4c60-4bca-b4e4-793276a0f621",
            "hostname": "kafka-0",
            "id": "a20616d8-9c87-4bc9-bc85-3825a9e3145f",
            "name": "kafka-0",
            "type": "filebeat",
            "version": "7.10.1"
        },
        "ecs": {
            "version": "1.6.0"
        },
        "fields": {
            "log_topic": "a"
        },
        "host": {
            "name": "kafka-0"
        },
        "input": {
            "type": "log"
        },
        "log": {
            "file": {
                "path": "/root/test.log"
            },
            "offset": 16
        },
        "message": "d"
    }
