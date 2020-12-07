---
title: Celery 笔记
tags:
  - Celery
categories:
  - Celery
top: false
date: 2020-08-10 19:16:05
---

# Celery 笔记

## 一、什么是Celery

### 1.1、celery是什么

Celery是一个简单、灵活且可靠的，处理大量消息的分布式系统，专注于实时处理的异步任务队列，同时也支持任务调度。

![celery](https://i.loli.net/2020/08/13/UBcQ6INAKHyZolS.png)

Celery的架构由三部分组成，消息中间件（message broker），任务执行单元（worker）和任务执行结果存储（task result store）组成。

**消息中间件**

Celery本身不提供消息服务，但是可以方便的和第三方提供的消息中间件集成。包括，RabbitMQ, Redis等等

**任务执行单元**

Worker是Celery提供的任务执行的单元，worker并发的运行在分布式的系统节点中。

**任务结果存储**

Task result store用来存储Worker执行的任务的结果，Celery支持以不同方式存储任务的结果，包括AMQP, redis等

另外， Celery还支持不同的并发和序列化的手段

- 并发：Prefork, Eventlet, gevent, threads/single threaded
- 序列化：pickle, json, yaml, msgpack. zlib, bzip2 compression， Cryptographic message signing 等等

### 1.2、使用场景

celery是一个强大的 ***分布式任务队列的异步处理框架***，它可以让任务的执行完全脱离主程序，甚至可以被分配到其他主机上运行。我们通常使用它来实现**异步任务**（async task）和**定时任务**（crontab)。

异步任务：将耗时操作任务提交给Celery去异步执行，比如发送短信/邮件、消息推送、音视频处理等等

定时任务：定时执行某件事情，比如每天数据统计

### 1.3、Celery具有以下优点

```python
Simple(简单)
Celery 使用和维护都非常简单，并且不需要配置文件。

Highly Available（高可用）
woker和client会在网络连接丢失或者失败时，自动进行重试。并且有的brokers 也支持“双主”或者“主／从”的方式实现高可用。

Fast（快速）
单个的Celery进程每分钟可以处理百万级的任务，并且只需要毫秒级的往返延迟（使用 RabbitMQ, librabbitmq, 和优化设置时）

Flexible（灵活）
Celery几乎每个部分都可以扩展使用，自定义池实现、序列化、压缩方案、日志记录、调度器、消费者、生产者、broker传输等等。
```

## 二、Celery执行异步任务

### 2.1、基本使用

```python
# 创建异步任务执行文件celery_task:
import celery
import time
backend='redis://127.0.0.1:6379/1'
broker='redis://127.0.0.1:6379/2'
cel=celery.Celery('test',backend=backend,broker=broker)
@cel.task
def send_email(name):
    print("向%s发送邮件..."%name)
    time.sleep(5)
    print("向%s发送邮件完成"%name)
    return "ok"

# 创建执行任务文件,produce_task.py:
from celery_task import send_email
result = send_email.delay("lei")
print(result.id)
result2 = send_email.delay("xxml")
print(result2.id)


# 查看执行结果
from celery.result import AsyncResult
from celery_task import cel

async_result=AsyncResult(id="08fde85b-f335-4b5c-9cbe-cc7c808d7a9b", app=cel)

if async_result.successful():
    result = async_result.get()
    print(result)
    # result.forget() # 将结果删除
elif async_result.failed():
    print('执行失败')
elif async_result.status == 'PENDING':
    print('任务等待中被执行')
elif async_result.status == 'RETRY':
    print('任务异常后正在重试')
elif async_result.status == 'STARTED':
    print('任务已经开始被执行')
```

>  异步任务文件命令执行：`celery worker -A celery_app_task -l info`

### 2.2、多任务结构

![多任务目录结构](https://i.loli.net/2020/08/14/hwbyvHMC7IkgmFt.png)

```python
# celery.py:
from celery import Celery

cel = Celery('celery_demo',
             broker='redis://127.0.0.1:6379/1',
             backend='redis://127.0.0.1:6379/2',
             # 包含以下两个任务文件，去相应的py文件中找任务，对多个任务做分类
             include=['celery_tasks.task01',
                      'celery_tasks.task02'
                      ])

# 时区
cel.conf.timezone = 'Asia/Shanghai'
# 是否使用UTC
cel.conf.enable_utc = False



#task01
import time
from celery_tasks.celery import cel

@cel.task
def send_email(res):
    time.sleep(5)
    return "完成向%s发送邮件任务"%res



#task02
import time
from celery_tasks.celery import cel
@cel.task
def send_msg(name):
    time.sleep(5)
    return "完成向%s发送短信任务"%name



# produce_task.py
from celery_tasks.task01 import send_email
from celery_tasks.task02 import send_msg

# 立即告知celery去执行test_celery任务，并传入一个参数
result = send_email.delay('yuan')
print(result.id)
result = send_msg.delay('yuan')
print(result.id)



#　check_result.py
from celery.result import AsyncResult
from celery_tasks.celery import cel

async_result = AsyncResult(id="562834c6-e4be-46d2-908a-b102adbbf390", app=cel)

if async_result.successful():
    result = async_result.get()
    print(result)
    # result.forget() # 将结果删除,执行完成，结果不会自动删除
    # async.revoke(terminate=True)  # 无论现在是什么时候，都要终止
    # async.revoke(terminate=False) # 如果任务还没有开始执行呢，那么就可以终止。
elif async_result.failed():
    print('执行失败')
elif async_result.status == 'PENDING':
    print('任务等待中被执行')
elif async_result.status == 'RETRY':
    print('任务异常后正在重试')
elif async_result.status == 'STARTED':
    print('任务已经开始被执行')
    
    
###
# 开启work：celery worker -A celery_task -l info -P eventlet，添加任务（执行produce_task.py)，检查任务执行结果（执行check_result.py）
###

```

## 三、Celery执行定时任务

 设定时间让celery执行一个定时任务，produce_task.py:

```python

from celery_task import send_email
from datetime import datetime

# 方式一
# v1 = datetime(2020, 3, 11, 16, 19, 00)
# print(v1)
# v2 = datetime.utcfromtimestamp(v1.timestamp())
# print(v2)
# result = send_email.apply_async(args=["egon",], eta=v2)
# print(result.id)

# 方式二
ctime = datetime.now()
# 默认用utc时间
utc_ctime = datetime.utcfromtimestamp(ctime.timestamp())
from datetime import timedelta
time_delay = timedelta(seconds=10)
task_time = utc_ctime + time_delay

# 使用apply_async并设定时间
result = send_email.apply_async(args=["egon"], eta=task_time)
print(result.id)
```

多任务结构 修改celery.py：

```python
from datetime import timedelta
from celery import Celery
from celery.schedules import crontab

cel = Celery('tasks', broker='redis://127.0.0.1:6379/1', 		backend='redis://127.0.0.1:6379/2', include=[
    'celery_tasks.task01',
    'celery_tasks.task02',
])
cel.conf.timezone = 'Asia/Shanghai'
cel.conf.enable_utc = False

cel.conf.beat_schedule = {
    # 名字随意命名
    'add-every-10-seconds': {
        # 执行tasks1下的test_celery函数
        'task': 'celery_tasks.task01.send_email',
        # 每隔2秒执行一次
        # 'schedule': 1.0,
        # 'schedule': crontab(minute="*/1"),
        'schedule': timedelta(seconds=6),
        # 传递参数
        'args': ('张三',)
    },
    # 'add-every-12-seconds': {
    #     'task': 'celery_tasks.task01.send_email',
    #     每年4月11号，8点42分执行
    #     'schedule': crontab(minute=42, hour=8, day_of_month=11, month_of_year=4),
    #     'args': ('张三',)
    # },
}

'''
# 启动 Beat 程序$ celery beat -A proj<br># Celery Beat进程会读取配置文件的内容，周期性的将配置中到期需要执行的任务发送给任务队列
 
# 之后启动 worker 进程.$ celery -A proj worker -l info 或者$ celery -B -A proj worker -l info
'''
```



## 四、Django中使用celery

[django_celery代码笔记](https://github.com/lei025/leetcode/tree/master/djcelery)

[celery笔记](https://github.com/lei025/leetcode/tree/master/Celery)

