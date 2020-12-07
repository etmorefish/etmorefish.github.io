---
title: 消息队列-RabbitMQ
tags:
  - RabbitMQ
  - Python
categories:
  - 消息队列
top: false
date: 2020-07-25 19:16:05
---

# 一、什么是消息队列

​		MQ全称为Message Queue 消息队列（MQ）是一种应用程序对应用程序的通信方法。MQ是消费-生产者模型的一个典型的代表，一端往消息队列中不断写入消息，而另一端则可以读取队列中的消息。这样发布者和使用者都不用知道对方的存在。


> 生产者消费者模式是通过一个容器来解决生产者和消费者的强耦合问题。生产者和消费者彼此之间不直接通讯，而通过阻塞队列来进行通讯，所以生产者生产完数据之后不用等待消费者处理，直接扔给阻塞队列，消费者不找生产者要数据，而是直接从阻塞队列里取，阻塞队列就相当于一个缓冲区，平衡了生产者和消费者的处理能力。

![img](https://i.loli.net/2020/08/12/aCX1Zi5bmFQxU86.png)
> 队列是一种先进先出的数据结构。**FIFO**
>
> 消息队列可以简单理解为：把要传输的数据放在队列中。
>
> 低类聚，高耦合。

# 二、为什么要用消息队列

消息队列中间件是分布式系统中重要的组件，主要解决应用**<u>解耦，异步消息，流量削锋</u>**等问题，实现高性能，高可用，可伸缩和最终一致性架构。目前使用较多的消息队列有，ZeroMQ，Kafka，MetaMQ，RocketMQ。eg：

![外卖系统](https://i.loli.net/2020/08/12/m7D6FRaAGzqYbnl.png)

# 三、 RabbitMQ

[技术文档](https://www.rabbitmq.com/tutorials/tutorial-one-python.html)   

[RabbitMQ 安装和配置]([https://blog.csdn.net/haeasringnar/article/details/82715823?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522159715947119195162519670%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=159715947119195162519670&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v3~pc_rank_v3-1-82715823.pc_ecpm_v3_pc_rank_v3&utm_term=ubuntu+rabbitmq%E5%AE%89%E8%A3%85&spm=1018.2118.3001.4187](https://blog.csdn.net/haeasringnar/article/details/82715823?ops_request_misc=%7B%22request%5Fid%22%3A%22159715947119195162519670%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=159715947119195162519670&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v3~pc_rank_v3-1-82715823.pc_ecpm_v3_pc_rank_v3&utm_term=ubuntu+rabbitmq安装&spm=1018.2118.3001.4187))

```python
简单模式
	生产者：
		1、连接rabbitmq
		2、创建队列
		3、向指定队列插入数据
	消费者：
		1、连接rabbitmq
        2、监听模式
        3、确定回调函数

参数使用
	1、应答参数
    2、持久化参数
    3、分发参数
    	- 轮询分发
        - 公平分发

交换机模式
	- 发布订阅模式  	 exchange_type='fanout'
	- 关键字模式		 exchange_type='direct'
	- 模糊匹配模式	exchange_type='topic'
```

## 1、简单模式

```python
###### 生产者 producer.py
import pika

# 连接rabbitmq
connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()

# 创建队列
channel.queue_declare(queue='hello')

# 向指定队列插入数据
channel.basic_publish(exchange='',    # 简单模式
                      routing_key='hello',  # 指定队列
                      body='Hello World!')  # 插入数据

print(" [x] Sent 'Hello World!'")


###### 消费者 consumer.py
import pika
	
connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()

# 创建队列
channel.queue_declare(queue='hello')

# 确定回调函数
def callback(ch, method, properties, body):
    print(" [x] Received %r" % body)

# 确定监听队列
channel.basic_consume(queue='hello',
                      auto_ack=True,    # 默认应答
                      on_message_callback=callback)


print(' [*] Waiting for messages. To exit press CTRL+C')
# 正式监听
channel.start_consuming()
```

## 2、参数使用

```python
1、应答参数
auto_ack=False
ch.basic_ack(delivery_tag=method.delivery_tag)

2、持久化参数
#声明queue
channel.queue_declare(queue='hello2', durable=True)  # 若声明过，则换一个名字
 
channel.basic_publish(exchange='',
                      routing_key='hello2',
                      body='Hello World!',
                      properties=pika.BasicProperties(
                          delivery_mode=2,  # make message persistent
                          )
                      )

3、分发参数
"""
有两个消费者同时监听一个的队列。其中一个线程sleep2秒，另一个消费者线程sleep1秒，但是处理的消息是一样多。这种方式叫轮询分发（round-robin）不管谁忙，都不会多给消息，总是你一个我一个。
想要做到公平分发（fair dispatch），必须关闭自动应答ack，改成手动应答。使用basicQos(perfetch=1)限制每次只发送不超过1条消息到同一个消费者，消费者必须手动反馈告知队列，才会发送下一个。
"""
channel.basic_qos(prefetch_count=1)
```

## 3、交换机模式

* 发布订阅模式  	 exchange_type='fanout'
  ![发布订阅模式](https://i.loli.net/2020/08/12/q6C2o1iysAfcra8.png)
  
* 关键字模式		 exchange_type='direct'
  ![关键字模式](https://i.loli.net/2020/08/12/2ajVSAnkNUleKmr.png)

* 模糊匹配模式	exchange_type='topic'

  > 通配符交换机”与之前的路由模式相比，它将信息的传输类型的key更加细化，以“key1.key2.keyN....”的模式来指定信息传输的key的大类型和大类型下面的小类型，让消费者可以更加精细的确认自己想要获取的信息类型。而在消费者一段，不用精确的指定具体到哪一个大类型下的小类型的key，而是可以使用类似正则表达式(但与正则表达式规则完全不同)的通配符在指定一定范围或符合某一个字符串匹配规则的key，来获取想要的信息。
  >
  > “通配符交换机”（Topic Exchange）将路由键和某模式进行匹配。此时队列需要绑定在一个模式上。符号“#”匹配一个或多个词，符号“*”仅匹配一个词。因此“audit.#”能够匹配到“audit.irs.corporate”，但是“audit.*”只会匹配到“audit.irs”。（这里与我们一般的正则表达式的“*”和“#”刚好相反，这里我们需要注意一下。）

 ![模糊匹配模式](https://i.loli.net/2020/08/12/oPBMjlCpHbaqNgf.png)



> 源码笔记：[https://github.com/lei025/leetcode/tree/master/%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97](https://github.com/lei025/leetcode/tree/master/消息队列)

https://www.sojson.com/blog/48.html