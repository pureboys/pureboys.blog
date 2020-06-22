---
title: "使用Docker-compose快速搭建Kafka开发环境"
date: 2020-06-22T10:18:26+08:00
---

### 安装
创建 docker_kafka 的目录
参考官方测试用的docker-compose.yml直接在自定义的目录位置新建docker-compose的配置文件

```yaml
version: '2.1'
services:
zookeeper:
   image: wurstmeister/zookeeper
   ports:
   - "2181"
kafka:
   image: wurstmeister/kafka
   ports:
   - 9092:9092
   depends_on:
   - "zookeeper"
   environment:
   KAFKA_ADVERTISED_HOST_NAME:  172.26.201.4
   KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
   volumes:
   - /var/run/docker.sock:/var/run/docker.sock
```

注意： KAFKA_ADVERTISED_HOST_NAME 需要配置为宿主机的ip
docker-compose 启动kafka

```shell
root@ubuntu:~/docker/kafka# docker-compose up -d
```

###  验证
首先进入到一个kafka容器中，例如: kafka_kafka_1
```shell
root@ubuntu:~/docker/kafka# docker exec -it docker_kafka_kafka_1 /bin/bash
```

创建一个topic并查看，需要指定zookeeper的容器名(这里是docker_kafka_kafka_1)，topic的名字为test

```shell
$KAFKA_HOME/bin/kafka-topics.sh --create --topic test --zookeeper docker_kafka_zookeeper_1:2181 --replication-factor 1 --partitions 1

$KAFKA_HOME/bin/kafka-topics.sh --list --zookeeper docker_kafka_zookeeper_1:2181 
```

发布消息，输入几条消息后，按^C退出发布

```shell
$KAFKA_HOME/bin/kafka-console-producer.sh --topic=test --broker-list docker_kafka_kafka_1:9092
```

接受消息

```shell
$KAFKA_HOME/bin/kafka-console-consumer.sh --bootstrap-server docker_kafka_kafka_1:9092 --from-beginning --topic test
```




