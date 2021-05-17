# 一、服务器性能排查（机器慢了怎么处理）

### 1、使用**top**查看整机性能

![image-20210225231457152](images\image-20210225231457152.png)

注意 %CPU 和 %MEM

### 2、进入之后按<font color=red>**1**</font>查看所有的cpu（查看具体是哪一个CPU慢）

![image-20210225231808749](images\image-20210225231808749.png)

**注意：**红框中<font color=red>id</font>是否过低（越高越好，这个代表空闲率，id=idle）

**注意：**红框上方有一个<font color=red> load average </font> 这个代表的服务器的系统负载率，后面三个参数分别代表 1分钟/5分钟/15分钟 ；

# 二、低配版查看性能命令

### 1、uptime（查看负载均衡率）

![image-20210225232603138](images\image-20210225232603138.png)

### 2、free（查看内存）

注：推荐 free -m

![image-20210225232658030](images\image-20210225232658030.png)

### 3、df（查看硬盘）

注：推荐 df -h

![image-20210225232844027](images\image-20210225232844027.png)

![image-20210225232937997](images\image-20210225232937997.png)

### 4、vmstat （查看CPU）

注：包含但不限于

![image-20210225233252547](images\image-20210225233252547.png)

### 5、iostat （磁盘IO）

![image-20210225233536483](images\image-20210225233536483.png)