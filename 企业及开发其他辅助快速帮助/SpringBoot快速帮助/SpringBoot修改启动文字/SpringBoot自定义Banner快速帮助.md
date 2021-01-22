# 一、如何自定义SpringBoot启动文字

#### 1、生成专用的txt文件，命名为<font color='red'>banner.txt</font>

banner.txt文件内容如下所示

```
     へ　　 　　　／|
 　　/ ＼　　　 ∠＿/
 　 /　│　　 ／　／             阿
 　│　 Z ＿＜　／　　　 /`ヽ     创
 　│　　  　　ヽ　　 　/　　〉    专
 　Y　　　　　  :　  　/　　/    属
 　ｲ●　､　●　　⊂⊃　〈　　/       镇
 　()　 へ　　　　|　　＼〈       楼
 　　>ｰ ､_　 ィ　 │   ／／       神
 　 / へ　　 /　ﾉ＜|   ＼＼      宠
 　 ヽ_ﾉ　　(_／　 ＼＿／／
 　＜__r￣￣`＜＿＿r＿＿／
 　
 　
     _    ____ _                             
    / \  / ___| |__  _   _  __ _ _ __   __ _ 
   / _ \| |   | '_ \| | | |/ _` | '_ \ / _` |
  / ___ \ |___| | | | |_| | (_| | | | | (_| |
 /_/   \_\____|_| |_|\__,_|\__,_|_| |_|\__, |
                                       |___/ 
```

#### 2、如何生成专属的Banner图案文字呢？

采用在线网站生成Banner文字

网站一（字体很多）：	http://patorjk.com/software/taag/

网站二（可图片）：	 https://www.bootschool.net/ascii      

网站三：						http://www.network-science.de/ascii/

生成的图案下载或者复制到banner.txt文件中

#### 3、如何使用banner.txt文件？

将banner.txt文件放到resources目录下即可，什么都不用做，如下图。

![image-20210122151302992](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210122151302992.png)

#### 4、启动服务后控制台如下

![image-20210122151235452](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210122151235452.png)

# 二、控制Banner的开关（自行尝试）

```java
   public static void main(String[] args) {
        SpringApplication application = new SpringApplication(TestbootApplication.class);
        // 关闭Banner
        application.setBannerMode(Banner.Mode.OFF);
        // 控制台输出banner
        application.setBannerMode(Banner.Mode.CONSOLE);
        // 日志输出banner
        application.setBannerMode(Banner.Mode.LOG);
        application.run(args);
    }
```

# 三、Banner进阶

#### 自定义Banner属性

我们的Banner也可以自定义属性，如下所示我们创建一个BaseApplication，启动类继承这个类，然后静态代码块初始化设置属性以及JVM信息。（当然也可以不用集成）

```java
public class BaseApplication {
    static {
        Runtime runtime = Runtime.getRuntime();
        System.setProperty("total.memory",String.valueOf(runtime.totalMemory()/1000L/1000L));
        System.setProperty("max.memory",String.valueOf(runtime.maxMemory()/1000L/1000L));
        System.setProperty("free.memory",String.valueOf(runtime.freeMemory()/1000L/1000L));
        System.setProperty("spring-cloud.version","Greenwich.SR3");
        System.setProperty("cloud-alibaba.version","2.1.1.RELEASE");
    }
}
```

启动类

```java
@SpringBootApplication
public class TestApplication extends BaseApplication {
    public static void main(String[] args) {
        SpringApplication application = new SpringApplication(TestApplication.class);
        application.run(args);
    }
}
```



#### banner文件可以打印出SpringBoot的系统数据

```
     へ　　　　　／|
 　　/ ＼　　　∠＿/
 　 /　 │　　／　／             阿
 　│　 Z ＿＜　／　　　 /`ヽ     创
 　│　　创　　ヽ　　 　/　　〉    专
 　Y　　　　　  :　  　/　　/    属
 　ｲ●　､　●　　⊂⊃　〈　　/       镇
 　()　 へ　　　　|　　＼〈       楼
 　　>ｰ ､_　 ィ　 │   ／／       神
 　 / へ　　 /　ﾉ＜|   ＼＼      宠
 　 ヽ_ﾉ　　(_／　 ＼＿／／
 　＜__r￣￣`＜＿＿r＿＿／

${AnsiColor.BRIGHT_MAGENTA}
  Application版本: ${application.version}
  Application标题: ${application.title}
  Spring Boot版本: ${spring-boot.version}
 Spring Cloud版本: ${spring-cloud.version}
Cloud AliBaBa版本: ${cloud-alibaba.version}
   初始最小化堆内存: ${total.memory}MB
   初始最大化堆内存: ${max.memory}MB
   剩余可用系统内存: ${free.memory}MB
          JDK版本: ${java.version}
           OS系统: ${os.name}${os.version}
```

