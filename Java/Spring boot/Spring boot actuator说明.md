[TOC]

# Spring boot actuator

## 配置

|                   参数                    |                             说明                             |    默认值    |
| :---------------------------------------: | :----------------------------------------------------------: | :----------: |
| management.endpoints.web.exposure.include | 选择暴露端口名(*为暴露所有端口)<br />输入端口名1,端口名2,...,端口名n即可选择性暴露多个端口 | Health, info |
| management.endpoints.web.exposure.exclude | 隐藏端口<br />输入端口名1,端口名2,...,端口名n即可选择性隐藏多个端口 |      无      |
|          management.server.port           | actuator开放的端口，它用于提供应用程序的健康状况、性能指标等管理信息。通常情况下，它的默认值为 8080，用于Spring boot 2.x |     8080     |
|              management.port              |    与management.server.port作用相同，用于Spring boot 1.x     |     8080     |
|          management.context-path          | 通过更改此属性的值，您可以更改管理访问actuator的 URL 路径。例如，如果您将 management.context-path 设置为 /management，则应用程序的管理端点 URL 将变为 http://localhost:8080/management/health，用于Spring boot 1.x |  /actuator   |
|    management.endpoints.web.base-path     |    与management.context-path作用相同，用于Spring boot 2.x    |  /actuator   |
|        management.security.enabled        | 如果将其设置为 true，则需要进行身份验证才能访问管理端点。如果将其设置为 false，则可以无需身份验证即可访问管理端点。默认情况下，此属性的值为 false。启用此属性可以帮助保护管理端点免受未经授权的访问。 |    false     |

## 端口

| 路径                       | 说明                                                  | 详情                                                         |
| -------------------------- | ----------------------------------------------------- | ------------------------------------------------------------ |
| `/actuator`                | actuator服务访问上下文                                |                                                              |
| `/actuator/beans`          | beans 端点提供有关应用程序 bean 的信息                | 详见beans                                                    |
| `/actuator/caches`         | 查看应用程序的缓存                                    | 详见caches                                                   |
| `/actuator/health`         | 查看应用程序的健康状态                                | 详见health                                                   |
| `/actuator/info`           | 提供有关应用程序的一般信息（可自定义）                |                                                              |
| `/actuator/conditions`     | 提供有关配置和自动配置类条件评估的信息                | 详见conditions                                               |
| `/actuator/configprops`    | 提供有关应用程序`@ConfigurationProperties`Bean 的信息 | 详见configprops                                              |
| `/actuator/env`            | 提供有关应用程序的整个环境                            | 详见env                                                      |
| `/actuator/loggers`        | 提供对应用程序记录器及其级别配置的访问                | 详见loggers                                                  |
| `/actuator/heapdump`       | 提供来自应用程序 JVM 的heap dump                      | 响应是[HPROF](https://docs.oracle.com/javase/8/docs/technotes/samples/hprof.html)格式的二进制数据 |
| `/actuator/threaddump`     | 提供来自应用程序 JVM 的thread dump                    | 详见threaddump                                               |
| `/actuator/metrics`        | 提供对应用程序指标的访问                              | 详见metrics                                                  |
| `/actuator/scheduledtasks` | 提供有关应用程序计划任务的信息                        | 详见scheduledtasks                                           |
| `/actuator/mappings`       | 提供有关应用程序请求映射的信息                        | 详见mappings                                                 |

### beans

自定义一个bean，然后运行程序后，进入`/actuator/beans`搜索test即可查看到test的相关信息。

```java
@Configuration
public class HelloWorld {
    @Bean
    public void test() {
        System.out.println("hello, world!");
    }
}
```

```json
{
"context":{
  "application":{
    "beans":{
      ....
      "test": {
                    "aliases": [],
                    "scope": "singleton",
                    "type": "void",
                    "resource": "class path resource [com/apusic/demo/bean/HelloWorld.class]",
                    "dependencies": []
                },
      ....
    }
  }
}
}
```



![64ded791666ff400012d7fc8](https://raw.githubusercontent.com/Zengwenliang0416/md-pic/main/202308181029446.png)

详细参数介绍如下：

| 路径                              | 类型     | 说明                               |
| :-------------------------------- | :------- | :--------------------------------- |
| `contexts`                        | `Object` | 由 id 键控的应用程序上下文         |
| `contexts.*.parentId`             | `String` | 父应用程序上下文的 ID（如果有）    |
| `contexts.*.beans`                | `Object` | 应用程序上下文中的 Bean 按名称键入 |
| `contexts.*.beans.*.aliases`      | `Array`  | 任何别名的名称                     |
| `contexts.*.beans.*.scope`        | `String` | Bean 的范围                        |
| `contexts.*.beans.*.type`         | `String` | 完全合格的 bean 类型               |
| `contexts.*.beans.*.resource`     | `String` | 定义 bean 的资源（如果有）         |
| `contexts.*.beans.*.dependencies` | `Array`  | 任何依赖项的名称                   |



### caches



### health

修改配置信息显示health端口的详细内容。

```properties
management.endpoint.health.show-details=always	# 默认为never
```

此时再次访问/actuator/health端口查看详细内容，只有当components中所有组件均为UP，应用程序的健康状态才为UP。（eg. 当添加数据源但是数据库服务未开启时组件中有关服务会显示DOWN，此时应用程序的健康状态为DOWN）

```json
{
    "status": "UP",
    "components": {
        "diskSpace": {
            "status": "UP",
            "details": {
                "total": 107371032576,
                "free": 97080827904,
                "threshold": 10485760,
                "exists": true
            }
        },
        "ping": {
            "status": "UP"
        }
    }
}
```

### conditions

进入`/actuator/conditions`即可看到类似以下信息的响应

```json
{
  "contexts" : {
    "application" : {
      "positiveMatches" : {
        ...
        "EndpointAutoConfiguration#endpointOperationParameterMapper" : [ {
          "condition" : ...,
          "message" : ...
        } ],
        "GsonHttpMessageConvertersConfiguration.GsonHttpMessageConverterConfiguration" : {
          "notMatched" : [ {
            "condition" : ...,
            "message" : ...
          } ],
          "matched" : [ ]
        },
        ...
      "unconditionalClasses" : [ 
        ...
    }
  }
}
```

响应结构如下：

| 路径                                                   | 类型     | 描述                               |
| :----------------------------------------------------- | :------- | :--------------------------------- |
| `contexts`                                             | `Object` | 由 id 键控的应用程序上下文。       |
| `contexts.*.positiveMatches`                           | `Object` | 具有匹配条件的类和方法。           |
| `contexts.*.positiveMatches.*.[].condition`            | `String` | 条件名称。                         |
| `contexts.*.positiveMatches.*.[].message`              | `String` | 条件匹配的原因的详细信息。         |
| `contexts.*.negativeMatches`                           | `Object` | 条件不匹配的类和方法。             |
| `contexts.*.negativeMatches.*.notMatched`              | `Array`  | 符合的条件。                       |
| `contexts.*.negativeMatches.*.notMatched.[].condition` | `String` | 条件名称。                         |
| `contexts.*.negativeMatches.*.notMatched.[].message`   | `String` | 条件不匹配的详细信息。             |
| `contexts.*.negativeMatches.*.matched`                 | `Array`  | 符合的条件。                       |
| `contexts.*.negativeMatches.*.matched.[].condition`    | `String` | 条件名称。                         |
| `contexts.*.negativeMatches.*.matched.[].message`      | `String` | 条件匹配的原因的详细信息。         |
| `contexts.*.unconditionalClasses`                      | `Array`  | 无条件自动配置类的名称（如果有）。 |
| `contexts.*.parentId`                                  | `String` | 父应用程序上下文的 ID（如果有）。  |

### configprops

进入`/actuator/configprops`即可看到类似以下信息的响应。

![64dedfeae032030001be3873](https://raw.githubusercontent.com/Zengwenliang0416/md-pic/main/202308181105047.png)

结合我们的配置文件，从红框中可以看到我们在配置文件中对应用程序的相关配置。

```properties
management.endpoints.web.exposure.include=*
```

响应详情如下：

| 路径                            | 类型     | 描述                                                   |
| :------------------------------ | :------- | :----------------------------------------------------- |
| `contexts`                      | `Object` | 由 id 键控的应用程序上下文。                           |
| `contexts.*.beans.*`            | `Object` | `@ConfigurationProperties`通过 bean 名称键入的 beans。 |
| `contexts.*.beans.*.prefix`     | `String` | 应用于 bean 属性名称的前缀。                           |
| `contexts.*.beans.*.properties` | `Object` | bean 的属性作为名称-值对。                             |
| `contexts.*.beans.*.inputs`     | `Object` | 绑定到此 bean 时使用的配置属性的来源和值。             |
| `contexts.*.parentId`           | `String` | 父应用程序上下文的 ID（如果有）。                      |

### env

进入`/actuator/env`路径下，即可看到如下页面：

![64deeba5431b4b000142beb8](https://raw.githubusercontent.com/Zengwenliang0416/md-pic/main/202308181201249.png)



从图中可以看到我们一些系统配置以及服务端口值的类型

| 路径                                 | 类型     | 描述                         |
| :----------------------------------- | :------- | :--------------------------- |
| `property`                           | `Object` | 来自环境的属性（如果发现）   |
| `property.source`                    | `String` | 属性来源的名称               |
| `property.value`                     | `String` | 属性的价值                   |
| `activeProfiles`                     | `Array`  | 活动配置文件的名称（如果有） |
| `propertySources`                    | `Array`  | 属性来源按优先顺序排列       |
| `propertySources.[].name`            | `String` | 属性来源的名称               |
| `propertySources.[].property`        | `Object` | 属性来源中的属性             |
| `propertySources.[].property.value`  | `Varies` | 属性价值                     |
| `propertySources.[].property.origin` | `String` | 属性来源                     |

### loggers

进入`actuator/loggers`路径可以看到以下信息。

![64deefc9d49eb80001e7408b](https://raw.githubusercontent.com/Zengwenliang0416/md-pic/main/202308181213068.png)

从图中可以看到日志的相关等级，不同名称的记录器的有效级别，响应格式如下。

| 路径                        | 类型     | 描述                         |
| :-------------------------- | :------- | :--------------------------- |
| `levels`                    | `Array`  | 日志系统支持的级别           |
| `loggers`                   | `Object` | 按名称键入的记录器           |
| `groups`                    | `Object` | 按名称键入的记录器组         |
| `loggers.*.configuredLevel` | `String` | 记录器的配置级别（如果有）   |
| `loggers.*.effectiveLevel`  | `String` | 记录器的有效级别             |
| `groups.*.configuredLevel`  | `String` | 记录器组的配置级别（如果有） |
| `groups.*.members`          | `Array`  | 属于该组的记录器             |

可以通过`/actuator/loggers/{logger.name}`访问单个记录器的相关信息

该端口下还有以下接口

| 路径                              | 描述                           |      |
| --------------------------------- | ------------------------------ | ---- |
| `/actuator/loggers/{logger.name}` | 访问单个记录器的相关信息       |      |
| `/actuator/loggers/{group.name}`  | 访问某个组下的记录器的相关信息 |      |
|                                   |                                |      |

#### 设置日志级别

##### 设置单个记录器

示例：我们选择`com.apusic`这个记录器进行设置，在设置前进入http://localhost:9999/actuator/loggers/com.apusic可以查看当前记录器的相关信息。

![64def4c7d4d90d0001651b3a](https://raw.githubusercontent.com/Zengwenliang0416/md-pic/main/202308181235090.png)

然后需改请求为`POST`，在Headers和Body中分别加入Content-Type: application/json和{"configuredLevel":"debug"}，当Body中为空时即为清除日志级别。

![64def5af79c0dd0001b9282f](https://raw.githubusercontent.com/Zengwenliang0416/md-pic/main/202308181238144.png)

![64def5afffde4500019dad97](https://raw.githubusercontent.com/Zengwenliang0416/md-pic/main/202308181238171.png)

然后发送请求后，再次发送GET请求可以看到日志级别已经变更为DEBUG。

![64def62c4c460a0001f1c0c8](https://raw.githubusercontent.com/Zengwenliang0416/md-pic/main/202308181240057.png)

##### 设置组的日志级别

与设置单个记录器同理，只需要将路径中的`{logger.name}`替换为`{group.name}`即可。

### threaddump



进入`/actuator/threaddump`之后可以看到以下页面

![64df082ad4d90d000165379c](https://raw.githubusercontent.com/Zengwenliang0416/md-pic/main/202308181357617.png)

[响应信息的相关格式](https://docs.spring.io/spring-boot/docs/2.4.0/actuator-api/htmlsingle/#threaddump-retrieving-json:~:text=%7D%0A%20%20%7D%20%5D%0A%7D-,24.1.1.%20Response%20Structure,-The%20response%20contains)。

### [metrics](https://docs.spring.io/spring-boot/docs/2.4.0/actuator-api/htmlsingle/#metrics:~:text=of%20the%20function.-,18.%20Metrics%20(metrics),-The%20metrics%20endpoint)

进入`/actuator/metrics`之后可以看到以下页面

```json
{
    "names": [
        "http.server.requests",
        "jvm.buffer.count",
        "jvm.buffer.memory.used",
        "jvm.buffer.total.capacity",
        "jvm.classes.loaded",
        "jvm.classes.unloaded",
        "jvm.gc.live.data.size",
        "jvm.gc.max.data.size",
        "jvm.gc.memory.allocated",
        "jvm.gc.memory.promoted",
        "jvm.gc.pause",
        "jvm.memory.committed",
        "jvm.memory.max",
        "jvm.memory.used",
        "jvm.threads.daemon",
        "jvm.threads.live",
        "jvm.threads.peak",
        "jvm.threads.states",
        "logback.events",
        "process.cpu.usage",
        "process.start.time",
        "process.uptime",
        "system.cpu.count",
        "system.cpu.usage"
    ]
}
```

以`/actuator/metrics/{metric.name}`方式发送GET请求，可以得到具体的指标的信息，以jvm.gc.max.data.size为例，浏览器会输出以下响应信息。

```json
{
    "name": "jvm.gc.max.data.size",
    "description": "Max size of long-lived heap memory pool",
    "baseUnit": "bytes",
    "measurements": [
        {
            "statistic": "VALUE",
            "value": 1.431830528E9
        }
    ],
    "availableTags": []
}
```

响应结构如下：

| 路径                       | 类型     | 描述                                                         |
| :------------------------- | :------- | :----------------------------------------------------------- |
| `name`                     | `String` | 指标名称                                                     |
| `description`              | `String` | 指标描述                                                     |
| `baseUnit`                 | `String` | 公制的基本单位                                               |
| `measurements`             | `Array`  | 指标的测量                                                   |
| `measurements[].statistic` | `String` | 测量统计。（`TOTAL`、、、、、、、、）。`TOTAL_TIME`_ `COUNT`_ _ _ _`MAX``VALUE``UNKNOWN``ACTIVE_TASKS``DURATION` |
| `measurements[].value`     | `Number` | 测量值                                                       |
| `availableTags`            | `Array`  | 可用于向下钻取的标签                                         |
| `availableTags[].tag`      | `String` | 标签的名称                                                   |
| `availableTags[].values`   | `Array`  | 标签的可能值                                                 |

#### 所有指标相关说明

| 指标                      | 说明                                                         |
| ------------------------- | ------------------------------------------------------------ |
| http.server.requests      | 提供了有关应用程序处理的每个HTTP请求的信息，如请求计数、请求持续时间等 |
| jvm.buffer.count          | 提供了有关JVM缓冲区使用情况的信息，如缓冲区数量、缓冲区使用率等 |
| jvm.buffer.memory.used    | 已从JVM中卸载的类的数量，可以帮助开发人员了解应用程序的类加载器的性能 |
| jvm.buffer.total.capacity | 活跃对象的大小，可以帮助开发人员识别内存泄漏问题和调整JVM堆大小。 |
| jvm.classes.loaded        | 堆的最大大小，可以帮助开发人员了解JVM堆的大小限制，并根据需要进行调整 |
| jvm.classes.unloaded      | 已分配内存的总量，可以帮助开发人员了解应用程序的内存使用情况，并进行优化 |
| jvm.gc.live.data.size     | 活跃对象的大小，可以帮助开发人员识别内存泄漏问题和调整JVM堆大小 |
| jvm.gc.max.data.size      | 堆的最大大小，可以帮助开发人员了解JVM堆的大小限制，并根据需要进行调整 |
| jvm.gc.memory.allocated   | 已分配内存的总量，这可以帮助开发人员了解应用程序的内存使用情况，并进行优化 |
| jvm.gc.memory.promoted    | 通过垃圾回收已提升的对象大小，可以帮助开发人员了解垃圾回收器的性能，并调整堆大小和垃圾回收策略 |
| jvm.gc.pause              | 垃圾回收暂停的次数和持续时间，可以帮助开发人员了解垃圾回收器的性能，并调整堆大小和垃圾回收策略 |
| jvm.memory.committed      | 已提交的内存量，可以帮助开发人员了解应用程序的内存使用情况，并进行优化 |
| jvm.memory.max            | 最大可用内存量，可以帮助开发人员了解JVM堆的大小限制，并根据需要进行调整 |
| jvm.memory.used           | 已使用的内存量，可以帮助开发人员了解应用程序的内存使用情况，并进行优化 |
| jvm.threads.daemon        | 守护线程的数量，可以帮助开发人员了解应用程序中守护线程的使用情况，并进行优化 |
| jvm.threads.live          | 活动线程的数量，以帮助开发人员了解应用程序中线程的使用情况，并进行优化 |
| jvm.threads.peak          | 峰值线程数，可以帮助开发人员了解应用程序中线程使用的最高峰值，并进行优化 |
| jvm.threads.states        | 每种线程状态的数量，可以帮助开发人员了解应用程序中线程状态的分布情况，并进行优化 |
| logback.events            | Logback日志事件的数量，可以帮助开发人员了解应用程序中日志事件的数量和类型，并进行优化 |
| process.cpu.usage         | 进程CPU使用率，可以帮助开发人员了解应用程序在CPU方面的性能，并进行优化 |
| process.start.time        | 进程启动时间，可以帮助开发人员了解应用程序运行时间和稳定性   |
| process.uptime            | 进程运行时间，可以帮助开发人员了解应用程序运行时间和稳定性   |
| system.cpu.count          | 系统CPU核心数，可以帮助开发人员了解系统硬件规格，并优化应用程序性能 |
| system.cpu.usage          | 系统CPU使用率，可以帮助开发人员了解系统在CPU方面的性能，并进行优化 |

### scheduledtasks

给`/actuator/scheduledtasks`发送GET请求，可以看到如下界面

![64df17f5d49eb80001e7961a](https://raw.githubusercontent.com/Zengwenliang0416/md-pic/main/202308181504397.png)

此时我们没有设置定时任务，响应的结构如下：

| 路径                            | 类型     | 描述                                                         |
| :------------------------------ | :------- | :----------------------------------------------------------- |
| `cron`                          | `Array`  | Cron任务（如果有）。                                         |
| `cron.[].runnable.target`       | `String` | 将要执行的目标。                                             |
| `cron.[].expression`            | `String` | Cron表达式。                                                 |
| `fixedDelay`                    | `Array`  | 修复延迟任务（如果有）。                                     |
| `fixedDelay.[].runnable.target` | `String` | 将要执行的目标。                                             |
| `fixedDelay.[].initialDelay`    | `Number` | 第一次执行之前的延迟（以毫秒为单位）。                       |
| `fixedDelay.[].interval`        | `Number` | 上次执行结束与下一次执行开始之间的时间间隔（以毫秒为单位）。 |
| `fixedRate`                     | `Array`  | 固定时间任务（如果有）。                                     |
| `fixedRate.[].runnable.target`  | `String` | 将要执行的目标。                                             |
| `fixedRate.[].interval`         | `Number` | 每次执行开始之间的时间间隔（以毫秒为单位）。                 |
| `fixedRate.[].initialDelay`     | `Number` | 第一次执行之前的延迟（以毫秒为单位）。                       |
| `custom`                        | `Array`  | 具有自定义触发器的任务（如果有）。                           |
| `custom.[].runnable.target`     | `String` | 将要执行的目标。                                             |
| `custom.[].trigger`             | `String` | 触发任务。                                                   |

### mappings

给`/actuator/mappings`发送GET请求，可以看到如下界面

![64df1be1e032030001bec000](https://raw.githubusercontent.com/Zengwenliang0416/md-pic/main/202308181521447.png)

[响应结构](https://docs.spring.io/spring-boot/docs/2.4.0/actuator-api/htmlsingle/#mappings:~:text=%7D%0A%20%20%7D%0A%7D-,17.1.1.%20%E5%93%8D%E5%BA%94%E7%BB%93%E6%9E%84,-%E5%93%8D%E5%BA%94%E5%8C%85%E5%90%AB%E5%BA%94%E7%94%A8)。