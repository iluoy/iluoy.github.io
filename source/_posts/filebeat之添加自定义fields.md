---
title: filebeat之添加自定义fields
date: 2020-12-30 14:29:15
tags: ["EFK","filebeat","日志"]
categories: ["EFK","filebeat"]
---

### filebeat fields简介
`filebeat`支持自定义额外日志字段，例如给所有日志添加一个`app: jenkins`的属性，添加完成后可以通过`%{[]}`形式使用此自定义字段
### filebeat input中定义
在`filebeat input`中可以添加`fields`字段，例如添加`app: jenkins`的`fields`，此时添加output之后输出内容包含`"fields": {"app": "jenkins"}`，如下

    filebeat.inputs:
      - type: log
        paths:
          - /root/test.log
        fields:
          app: jenkins


    output.kafka:
      hosts: ["10.0.9.60:9092","10.0.9.30:9092","10.0.9.24:9092"]
      topic: ["test_log"]

此时获取`output`如下：

    {
        "@metadata": {
            "beat": "filebeat",
            "type": "_doc",
            "version": "7.10.1"
        },
        "@timestamp": "2020-12-30T06:51:09.479Z",
        "agent": {
            "ephemeral_id": "198069ae-ce06-4051-9ee9-8326596b150f",
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
            "app": "jenkins"
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
            "offset": 20
        },
        "message": "d"
    }

可以发现output中有：

    "fields": {
        "app": "jenkins"
    }

还有一个额外的`fields`参数可以控制自定义字段是否在`output`顶层：`fields\_under\_root`，如果为`true`表示为`output`顶层，为`false`则为`fields`字目录下，什么意思？加入添加以下配置

    filebeat.inputs:
      - type: log
        paths:
          - /root/test.log
        fields:
          app: jenkins
        fields_under_root: true

    output.kafka:
      hosts: ["10.0.9.60:9092","10.0.9.30:9092","10.0.9.24:9092"]
      topic: "test_log"

此时`output`如下

    {
        "@metadata": {
            "beat": "filebeat",
            "type": "_doc",
            "version": "7.10.1"
        },
        "@timestamp": "2020-12-30T07:01:26.558Z",
        "agent": {
            "ephemeral_id": "64269eb1-e03c-4ba9-abba-c8d573b73223",
            "hostname": "kafka-0",
            "id": "a20616d8-9c87-4bc9-bc85-3825a9e3145f",
            "name": "kafka-0",
            "type": "filebeat",
            "version": "7.10.1"
        },
        "app": "jenkins",
        "ecs": {
            "version": "1.6.0"
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
            "offset": 28
        },
        "message": "a"
    }
          
> 经过测试fields不能是数组结构，但是官方指出可以是标量、数组、字典以及其组合，找时间看看是否是测试方式不对

### 通用配置fields
在`filebeat.inputs`中配置`fields`只能在其配置的`input`中生效，使用通用配置可以全局生效，配置方式没有什么区别，只是位置不同，而且不支持`fields\_under\_root`参数，其值只能在`fields`子目录中

    fields:
      app: jenkins

    filebeat.inputs:
      - type: log
        paths:
          - /root/test.log

    output.kafka:
      hosts: ["10.0.9.60:9092","10.0.9.30:9092","10.0.9.24:9092"]
      topic: "test_log"

`output`输出为：

    {
        "@metadata": {
            "beat": "filebeat",
            "type": "_doc",
            "version": "7.10.1"
        },
        "@timestamp": "2020-12-30T07:13:36.879Z",
        "agent": {
            "ephemeral_id": "451275ae-796a-4a02-bc85-f7ddd8751dc3",
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
            "app": "jenkins"
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
            "offset": 40
        },
        "message": "a"
    }

### 使用fields
`output`输出中的字段都可以被引用，例如`'%{[host.name]}'`表示`kafka-0`，`'%{[fields.app]}'`表示`jenkins`，所以是否使用顶层fields决定了输出，也决定了引用，如果为顶层输出`'{%{[app]}}'`表示`jenkins`
