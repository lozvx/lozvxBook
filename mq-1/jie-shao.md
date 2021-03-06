# kafka

官网：[https://kafka.apache.org/](https://kafka.apache.org/)

中文教程：[http://www.orchome.com/kafka/index](http://www.orchome.com/kafka/index)

我们认为，一个流处理平台具有三个关键能力：

1. 发布和订阅消息（流），在这方面，它类似于一个消息队列或企业消息系统。
2. 以

   `容错`

   的方式存储消息（流）。

3. 在消息流发生时处理它们。

## 什么是kakfa的优势？

它应用于2大类应用：

1. 构建实时的流数据管道，可靠地获取系统和应用程序之间的数据。
2. 构建实时流的应用程序，对数据流进行转换或反应。

要了解kafka是如何做这些事情的，让我们从下到上深入探讨kafka的能力。

## 首先几个概念：

1. kafka作为一个集群运行在一个或多个服务器上。
2. kafka集群存储的消息是以topic为类别记录的。
3. 每个消息（也叫记录record，我习惯叫消息）是由一个key，一个value和时间戳构成。

## kafka有四个核心API：

* 应用程序使用 

  `Producer API`

   发布消息到1个或多个topic（主题）。

* 应用程序使用 

  `Consumer API`

   来订阅一个或多个topic，并处理产生的消息。

* 应用程序使用 

  `Streams API`

   充当一个流处理器，从1个或多个topic消费输入流，并生产一个输出流到1个或多个输出topic，有效地将输入流转换到输出流。

* `Connector API`

  允许构建或运行可重复使用的生产者或消费者，将topic连接到现有的应用程序或数据系统。例如，一个关系数据库的连接器可捕获每一个变化。

