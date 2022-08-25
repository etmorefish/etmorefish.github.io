---
title: InfluxDB不同版本调研2
tags:
  - k8s
  - influxdb
  - benchmark
  - prometheus
categories:
  - tsdb
top: false
date: 2022-07-28 18:33:18
---


# InfluxDB不同版本调研
一、关键概念
======

相比 V1 移除了 database 和 RP，增加了 bucket。

- 背景
  - 它是go语言开发的数据库，InfluxDB自发布至今，已经有两个版本，InfluxDB1.x系列提供一种类似SQL的查询语言InfluxQL，用于数据交互。2019年1月新推出的influxDB2.0 alpha版本，主推全新的查询语言Flux，支持TICK架构， TICK 整合成一个整体，将时序数据库、UI、仪表盘工具以及后台处理和监控代理置于一组 API 后面。在 2020 年底推出了InfluxDB 2.0 正式版本，该版本又分为InfluxDB Cloud 和 InfluxDB OSS两个系列。

  - InfluxDB 2.0还与[InfluxDB Cloud紧密集成，InfluxDB Cloud](https://www.influxdata.com/blog/influxdb-cloud-2-0-launches-as-a-serverless-platform-for-time-series-data/)是无服务器，弹性可扩展，完全托管的时间序列数据库平台。借助共享 API，您可以轻松地在 InfluxDB 2.0 和 InfluxDB Cloud 之间移动数据和工作负载，并且可以将它们作为单个时间序列平台的组件一起使用，从而为开发人员提供灵活性和工具，以满足不断变化的业务和应用程序需求。

- V2 具有以下几个概念：

timestamp、field key、field value、field set、tag key、tag value、tag set、measurement、series、point、bucket、bucket schema、organization

新增的概念：

bucket：所有 InfluxDB 数据都存储在一个存储桶中。一个桶结合了数据库的概念和存储周期（时间每个数据点仍然存在持续时间）。一个桶属于一个组织

bucket schema：具有明确的 schema-type 的存储桶需要为每个度量指定显式架构。测量包含标签、字段和时间戳。显式模式限制了可以写入该度量的数据的形状。

organization：InfluxDB 组织是一组用户的工作区。所有仪表板、任务、存储桶和用户都属于一个组织。

- v2 特性

  ```txt
  
  1,telegraf chronograf kapacitor influxdb都整合到一起了，一个二进制可执行文件执行后
  2,权限全增强，新增了token.可以使用token进行数据读写操作
  3,DSL改了,FluxQL的目的是为了通过这一个DSL来解决定时、流任务，数据查询等多种操作，并且相比tick.能执行的操作更多。
  4,接口返回数据的格式改为csv,并且可以使用 FluxQL 来自定义返回数据的格式与字段
  5,增加了类似prometheus的scrape功能，不过似乎默认是1O秒的采集间隔，这样可以直接采集prometheus exporter.上的数据，如果你使
  用1.7或更低版本，可能需要在prometheus中采集数据时使用remote write的功能，将致据点写入influxdb。现在influxdb v2直接可以实现这个采集的操作
  6,流任务更加直观。现在你可以查询数据时，把查询操作直接保存成定时任务，然后将生成的数据backfill进influxdb中。并且这个定时任务的管理器功能更加强大，你不仅可以设置定时执行，还能立刻执行
  7,OSS 2.x包括1.x兼容的`/query`和`/write`端点
  8,它自带有一个包含 Settings、Dashboards、Tasks、Alerts 等功能的 web 后台
  
  ```
  
  

二、系统结构
======



**TICK架构分析与各组件功能介绍**

TICK架构 是 InfluxData 平台的组件的集合首字母缩写，该集合包括Telegraf、InfluxDB、Chronograf和 Kapacitor。TICK架构以及各组件分工情况如图所示：

![tick](https://s2.loli.net/2022/08/24/j8tpD4FCQhUuqMB.png)



**数据模式**：InfluxDB 数据元素存储在时间结构合并树 (TSM) 和时间序列索引 (TSI) 文件中，以有效压缩存储的数据。

除了上图可视化管理工具Chronograf外，还有一种可视化工具Grafana，它也是用于大规模指标数据的可视化展示，提供包括折线图，饼图，仪表盘等多种监控数据可视化UI，若应用过程中考虑到扩展性问题，也会使用Grafana代替Chronograf。



**文件系统布局**

> InfluxDB Dockerhub镜像使用/var/lib/influxdb2而不是/var/lib/influxdb，因此在升级过程中，你可以很容易地为InfluxDB 1.x和2.x的数据装载不同的卷。

**默认路径：**

| Path                                                         | Default                             |
| :----------------------------------------------------------- | :---------------------------------- |
| [Engine path](https://docs.influxdata.com/influxdb/v2.3/reference/internals/file-system-layout/?t=Docker#engine-path) | `/var/lib/influxdb2/engine/`        |
| [Bolt path](https://docs.influxdata.com/influxdb/v2.3/reference/internals/file-system-layout/?t=Docker#bolt-path) | `/var/lib/influxdb2/influxd.bolt`   |
| [SQLite path](https://docs.influxdata.com/influxdb/v2.3/reference/internals/file-system-layout/?t=Docker#sqlite-path) | `/var/lib/influxdb2/influxd.sqlite` |
| [Configs path](https://docs.influxdata.com/influxdb/v2.3/reference/internals/file-system-layout/?t=Docker#configs-path) | `/etc/influxdb2/configs`            |

**[文件目录结构](https://docs.influxdata.com/influxdb/v2.3/reference/internals/file-system-layout/?t=Docker#)：**

- /var/lib/influxdb2/
  - engine/
    - data/
      - *TSM directories and files*
    - wal/
      - *WAL directories and files*
  - influxd.bolt
  - influxd.sqlite
- /etc/influxdb2/
  - configs

**Influxdb 分片和分片组**

InfluxDB 在将数据存储到磁盘时将时间序列数据组织成分片。分片被分组到分片组中

表示具有 4d 保留期 和 1d 分片组持续时间的存储桶：

![](https://s2.loli.net/2022/08/24/laJFv6ofSKBuCwT.png)

分片删除：InfluxDB 保留强制执行服务会例行检查早于其存储桶保留期的分片组。一旦分片组的开始时间超过存储桶的保留期，InfluxDB 将删除该分片组以及关联的分片和 TSM 文件 (在具有无限保留期的存储桶中，分片无限期地保留在磁盘上)。

**系统存储桶**

**_monitoring system bucket :** 该_monitoring 系统桶存储 InfluxDB 数据用于 监控数据并发送警报。数据保留：7 天

**_tasks system bucket:** 该_tasks 系统桶存储与数据 InfluxDB 任务的执行。数据保留：1 天

标签和字段描述详见：[https://docs.influxdata.com/influxdb/v2.3/reference/internals/system-buckets/](https://docs.influxdata.com/influxdb/v2.3/reference/internals/system-buckets/)

三、配置文件
======

当 influxd 启动时，它会在当前工作目录检查一个名为 config.* 的文件。

支持以下语法：

*   YAML (.yaml, .yml)
*   TOML (.toml)
*   JSON (.json)

配置选项 (日志、并发压缩...)：[https://docs.influxdata.com/influxdb/v2.3/reference/config-options/](https://docs.influxdata.com/influxdb/v2.3/reference/config-options/)

四、Flux 查询语句
===========

Flux 是 InfluxData 的功能性数据脚本语言，设计用于查询、分析和处理数据，它是 InfluxQL 和其他类似 SQL 的查询语言的替代品。

设计原则：受 Javascript 启发，旨在设计出可用、可读、灵活、可组合、可测试、可贡献和可共享的语言。

示例查询：近一小时存储的数据，按`cpu`度量和`cpu=cpu-total`标签过滤，以 1 分钟为间隔对数据进行窗口化，并计算每个窗口的平均值

```javascript
from(bucket:"example-bucket")
  |> range(start:-1h)
  |> filter(fn:(r) =>
    r._measurement == "cpu" and
    r.cpu == "cpu-total"
  )
  |> aggregateWindow(every: 1m, fn: mean)
```

**1 > 关键概念**

**Pipe-forward operator**（管道转发操作符 "|>"）：Flux 广泛使用管道转发运算符 "|>" 将操作链接在一起。在每个函数或操作之后，Flux 返回一个包含数据的表或表的集合。管道转发运算符将这些表通过管道输送到下一个函数或操作中，在那里它们将被进一步处理或操作。

**Tables**：Flux 构造表格中的所有数据。当数据从数据源流式传输时，Flux 将其格式化为带注释的逗号分隔值 (CSV)，表示表格。然后函数操作或处理它们并输出新表。

**Group keys**：每个表都有一个组键 (Group keys)，用于描述表的内容。它是一个列列表，表中的每一行都具有相同的值。每行中具有唯一值的列不是组键的一部分。

示例 group key

```
 
Group key: [_start, _stop, _field]
                   _start:time                      _stop:time           _field:string                      _time:time                  _value:float
------------------------------  ------------------------------  ----------------------  ------------------------------  ----------------------------
2019-04-25T17:33:55.196959000Z  2019-04-25T17:34:55.196959000Z            used_percent  2019-04-25T17:33:56.000000000Z             65.55318832397461
2019-04-25T17:33:55.196959000Z  2019-04-25T17:34:55.196959000Z            used_percent  2019-04-25T17:34:06.000000000Z             65.52391052246094
2019-04-25T17:33:55.196959000Z  2019-04-25T17:34:55.196959000Z            used_percent  2019-04-25T17:34:36.000000000Z              65.536737442016
```

注意：_time 和_value 被排除在示例组键之外，它们对于每一行都是唯一的。

**2 > 查询语法**

[https://docs.influxdata.com/influxdb/v2.3/query-data/get-started/query-influxdb/](https://docs.influxdata.com/influxdb/v2.3/query-data/get-started/query-influxdb/)

指定数据源：`from(bucket:"example-bucket")`

指定时间范围：

使用管道转发运算符 (|>) 将数据从数据源通过管道传输到 range() 函数，该函数指定查询的时间范围。它接受两个参数：start 和 stop。范围可以是使用相对负持续时间 或使用绝对时间

```javascript
// Relative time range with start only. Stop defaults to now.
from(bucket:"example-bucket")
    |> range(start: -1h)

// Relative time range with start and stop
from(bucket:"example-bucket")
    |> range(start: -1h, stop: -10m)
 
//使用绝对时间
from(bucket:"example-bucket")
    |> range(start: 2021-01-01T00:00:00Z, stop: 2021-01-01T12:00:00Z)
 
//过去十五分钟的数据
from(bucket:"example-bucket")
    |> range(start: -15m)
```

**3 > 数据过滤：**

将范围数据传递到 filter() 函数中，以根据数据属性或列缩小结果范围。该 filter() 函数有一个参数 ，fn 它需要一个匿名函数，该函数具有基于列或属性过滤数据的逻

```javascript
// Pattern
(r) => (r.recordProperty comparisonOperator comparisonExpression)

// Example with single filter
(r) => (r._measurement == "cpu")

// Example with multiple filters
(r) => r._measurement == "cpu" and r._field != "usage_system")
 
//按cpu度量、usage_system字段和cpu-total标记值过滤
from(bucket: "example-bucket")
    |> range(start: -15m)
    |> filter(fn: (r) => r._measurement == "cpu" and r._field == "usage_system" and r.cpu == "cpu-total")
```

**4 > 生成指定查询数据**

Flux 的 yield() 函数将过滤后的表作为查询结果输出。

Flux 会 yield() 在每个脚本的末尾自动假设一个函数，以便输出和可视化数据。yield() 只有在同一个 Flux 查询中包含多个查询时，才需要显式调用。每组返回的数据都需要使用该 yield() 函数命

```js
from(bucket: "example-bucket")
    |> range(start: -15m)
    |> filter(fn: (r) => r._measurement == "cpu" and r._field == "usage_system" and r.cpu == "cpu-total")
    |> yield()
```

**5 > 数据转换**

使用函数，将数据聚合为平均值、下采样数据等

```javascript
 
//更新范围从最后一小时拉取数据
from(bucket: "example-bucket")
    |> range(start: -1h)
    |> filter(fn: (r) => r._measurement == "cpu" and r._field == "usage_system" and r.cpu == "cpu-total")
 
//以五分钟为间隔的窗口化数据
from(bucket: "example-bucket")
    |> range(start: -1h)
    |> filter(fn: (r) => r._measurement == "cpu" and r._field == "usage_system" and r.cpu == "cpu-total")
 
//聚合窗口数据
from(bucket: "example-bucket")
    |> range(start: -1h)
    |> filter(fn: (r) => r._measurement == "cpu" and r._field == "usage_system" and r.cpu == "cpu-total")
    |> window(every: 5m)
    |> mean()
 
//添加时间列到聚合函数
from(bucket: "example-bucket")
    |> range(start: -1h)
    |> filter(fn: (r) => r._measurement == "cpu" and r._field == "usage_system" and r.cpu == "cpu-total")
    |> window(every: 5m)
    |> mean()
    |> duplicate(column: "_stop", as: "_time")
 
//取消窗口聚合表，将所有点收集到一个无限的窗口中
from(bucket:"example-bucket")
  |> range(start: -1h)
  |> filter(fn: (r) =>
    r._measurement == "cpu" and
    r._field == "usage_system" and
    r.cpu == "cpu-total"
  )
  |> window(every: 5m)
  |> mean()
  |> duplicate(column: "_stop", as: "_time")
  |> window(every: inf)
 
//辅助函数(将聚合或选择器函数应用于固定的时间窗口,通过every指定窗口的持续时间)
from(bucket: "example-bucket")
    |> range(start: -1h)
    |> filter(fn: (r) => r._measurement == "cpu" and r._field == "usage_system" and r.cpu == "cpu-total")
    |> window(every: 5m)
    |> mean()
    |> duplicate(column: "_stop", as: "_time")
```

**6 > 语法基础与通量函数**

语法基础：[https://docs.influxdata.com/flux/v0.x/get-started/syntax-basics/](https://docs.influxdata.com/flux/v0.x/get-started/syntax-basics/)

通量函数包：[https://docs.influxdata.com/flux/v0.x/stdlib/](https://docs.influxdata.com/flux/v0.x/stdlib/)

比如: filter() 函数 筛选数据 ([https://docs.influxdata.com/flux/v0.x/stdlib/universe/filter/](https://docs.influxdata.com/flux/v0.x/stdlib/universe/filter/)

五、可视化数据
=======

[https://docs.influxdata.com/influxdb/v2.3/visualize-data/visualization-types/](https://docs.influxdata.com/influxdb/v2.3/visualize-data/visualization-types/)

**支持的可视化类型：**

Band （乐队）：显示随时间变化的数据组的上限和下限

![](https://s2.loli.net/2022/08/24/wl3GkiUWRCapEDn.png)

guage（仪表盘）：仪表视图中显示时间序列的单个值最新值

![](https://s2.loli.net/2022/08/24/Js5RNkhtiAD1jcS.png)

graph（图形）：折线图

![](https://s2.loli.net/2022/08/24/jHdI7zC5JbpNA1q.png)

Graph + Single Stat（图表 + 单一统计）：以折线图显示指定的时间序列，并将最近的单个值叠加为一个大数值

![](https://s2.loli.net/2022/08/24/pxkolJKtwRAn1jh.png)

heatmap（热图）：显示 x 和 y 轴上的数据分布，其中颜色代表不同的数据点浓度

![](https://s2.loli.net/2022/08/24/aOkSMDyiz2bwfRI.png)

histogram（直方图）：一种查看数据分布的方法。y 轴专用于计数，x 轴分为 bin

![](https://s2.loli.net/2022/08/24/ivagJkyHX1zqeE9.png)

mosaic（马赛克）：化显示时间序列数据中的状态变化

![](https://s2.loli.net/2022/08/24/n6G4NXkImWyUpda.png)

scatter（散点图）：视图使用散点图来显示时间序列数据

![](https://s2.loli.net/2022/08/24/iHmI2W6Ct39VwLu.png)

single stat（单项统计）： 将指定时间序列的最新值显示为数值

![](https://s2.loli.net/2022/08/24/VfpXCTugheW5H1Q.png)

table（表格视图）：表格视图中显示查询结果  
![](https://s2.loli.net/2022/08/24/N3Y5RnkUZOCgTlF.png)

**本地制造数据，通过图表数据展示案例：**

![](https://s2.loli.net/2022/08/24/9gwDhEf1JNdmG3z.png)



遗留问题
====

**问：V1 版本的 RP+CQ 在 V2 版本应该怎样使用？**

答：

RP 存储周期，完全由 bucket 决定，在创建 bucket 时需要指定 bucket 的数据保留时间。

需要注意的是：bucket 的数据存储时间并不能随意更换，可以将永久存储的数据改成 Retention 存储，但是也存在限制；无法将有 Retention 的数据改成永久存储。

![](https://s2.loli.net/2022/08/24/tlMXa2DyxPYv51j.png)

CQ 需要使用 task 代替，InfluxQL 需要使用 Flux 代替。 

task 示例：每小时执行一次，收集一个小时内的数据，输出到另一个 bucket 中 (可使用内置函数，本案例为了验证功能使用直接输出)

```
option task = {name: "tast2", every: 1h}
 
from(bucket: "bucket1")
	|> range(start: -task.every)
	|> filter(fn: (r) =>
		(r._measurement == "mem2"))
	|> filter(fn: (r) =>
		(r._field == "used_percent"))
	|> set(key: "_measurement", value: "average3-mem2")
	|> to(org: "test", bucket: "bucket1/mem2-rp")
```

![](https://s2.loli.net/2022/08/24/a87tnCN9JzjpxZi.png)

![](https://s2.loli.net/2022/08/24/qsEHiImy1X3xWwn.png)

CQ 转 Task 官方描述：[https://docs.influxdata.com/influxdb/v2.3/upgrade/v1-to-v2/migrate-cqs/](https://docs.influxdata.com/influxdb/v2.3/upgrade/v1-to-v2/migrate-cqs/)

Task 使用介绍：[https://docs.influxdata.com/influxdb/v2.3/process-data/get-started/](https://docs.influxdata.com/influxdb/v2.3/process-data/get-started/)

**问：V2 版本数据如何备份？文件拷贝是否可行？**

答：

官方提供的备份方式：

```
//将所有数据备份到一个目录
influx backup /path/to/backup/dir/
 
//将特定存储桶备份到目录
influx backup --bucket example-bucket /path/to/backup/dir/
```

文件拷贝方式（/var/lib/influxdb）：与 V1 一致，服务重启时生效。

相比 V1 :  (1) 它在文件层次使用 ID 区分具体存储桶  (2) 它无法在插入过程中更换数据文件，即使更换了也会失效，不过成功启动之后删了它的数据文件，它也不会报错

结论：(1) 可以使用官方提供的命令进行冷备份，服务器上备份前需要进行 token 配置  (2) 文件拷贝是可行的，但是必须要重启服务，否则数据将会不准确。

官方描述：[https://docs.influxdata.com/influxdb/v2.3/reference/cli/influx/backup/](https://docs.influxdata.com/influxdb/v2.3/reference/cli/influx/backup/)

