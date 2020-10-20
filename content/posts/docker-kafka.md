---
title: "使用Docker-compose快速搭建Kafka开发环境"
summary: "使用Docker-compose快速搭建Kafka开发环境"
date: 2020-10-20T10:18:26+08:00
categories: ["tech"]
---

### 安装
创建 docker_kafka 的目录
参考官方测试用的docker-compose.yml直接在自定义的目录位置新建docker-compose的配置文件

```yaml
version: '3'
services:
	zookeeper:
    container_name: zookeeper
    image: wurstmeister/zookeeper

  kafka:
    container_name: kafka
    image: wurstmeister/kafka
    ports:
      - 9092:9092
    depends_on:
      - "zookeeper"
    environment:
      KAFKA_ADVERTISED_HOST_NAME: kafka
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181

  kafka-manager:
    container_name: kafka-manager
    image: hlebalbau/kafka-manager
    ports:
      - 9020:9000
    environment:
      ZK_HOSTS: zookeeper:2181
```

注意： KAFKA_ADVERTISED_HOST_NAME 需要配置为宿主机的ip
docker-compose 启动kafka

```shell
root@ubuntu:~/docker/kafka# docker-compose up -d
```

###  验证
首先进入到一个kafka容器中，例如: kafka
```shell
root@ubuntu:~/docker/kafka# docker exec -it kafka /bin/bash
```

创建一个topic并查看，需要指定zookeeper的容器名(这里是docker_kafka_kafka_1)，topic的名字为test

```shell
$KAFKA_HOME/bin/kafka-topics.sh --create --topic test --zookeeper zookeeper:2181 --replication-factor 1 --partitions 1

$KAFKA_HOME/bin/kafka-topics.sh --list --zookeeper zookeeper:2181 
```

发布消息，输入几条消息后，按^C退出发布

```shell
$KAFKA_HOME/bin/kafka-console-producer.sh --topic=test --broker-list kafka:9092
```

接受消息

```shell
$KAFKA_HOME/bin/kafka-console-consumer.sh --bootstrap-server kafka:9092 --from-beginning --topic test
```

### kafka-manager 添加数据问题

`Yikes! KeeperErrorCode = Unimplemented for /Kafka-Manager/mutex Try Again` CMAK添加集群的时候报错

参考网址 https://github.com/yahoo/CMAK/issues/731

``` shell
my docker image version
zookeeper:3.4.14
wurstmeister/kafka:2.12-2.4.1
kafkamanager/kafka-manager:3.0.0.4
this worked for me
➜ docker exec -it zookeeper bash
root@98747a9eac65:/zookeeper-3.4.14# ./bin/zkCli.sh
[zk: localhost:2181(CONNECTED) 2] ls /kafka-manager
[configs, deleteClusters, clusters]
[zk: localhost:2181(CONNECTED) 3] create /kafka-manager/mutex ""
Created /kafka-manager/mutex
[zk: localhost:2181(CONNECTED) 5] create /kafka-manager/mutex/locks ""
Created /kafka-manager/mutex/locks
[zk: localhost:2181(CONNECTED) 6] create /kafka-manager/mutex/leases ""
Created /kafka-manager/mutex/leases
```





