# 一、视频地址

Bilibili网站小刘讲源码：https://www.bilibili.com/video/BV1KE411K7Ts

# 二、LongAdder简要介绍

**主要是解决在高并发下实现统计的一个类。（之前使用AtomicLong类，但是在高并发下性能会不好）**。

高并发情况下 LongAdder的性能要远高于 AtomicLong的性能。

LongAdder工作原理

![image-20210207145925967](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210207145925967.png)