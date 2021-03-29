# 一、MQ介绍

![image-20210321123329647](images\image-20210321123329647.png)

# 二、主流MQ对比

![image-20210321123446948](images\image-20210321123446948.png)

![image-20210321123500886](images\image-20210321123500886.png)

# 三、主流MQ特点对比

![在这里插入图片描述](images\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p5eDEyNjAxNjgzOTU=,size_16,color_FFFFFF,t_70)

# 四、消息核心组件图

RocketMQ是开源的消息中间件，它主要由NameServer，Producer，Broker，Consumer四部分构成。

![img](images\format,png)

- NameServer：NameServer主要负责Topic和路由信息的管理，功能类似Dubbo的zookeeper。
- Producer：消息生产者，负责产生消息，一般由业务系统负责产生消息。
- Broker：消息中转角色，负责存储消息，转发消息。
- Consumer：消息消费者，负责消息消费，一般是后台系统负责异步消费。



