# 一、环境配置说明

MySQL 5.7.28
SpringBoot 2.3.1.RELEASE

# 二、数据库配置示例

```mysql
create database mysqltest;

use mysqltest;

create table t_user(
	`id` int primary key auto_increment,
	`username` varchar(30) not null unique,
	`password` varchar(40) not null,
	`email` varchar(50)
);

## 插入初始化测试数据
insert into t_user(`username`,`password`,`email`) values('admin','admin','admin@qq.com');
insert into t_user(`username`,`password`,`email`) values('wzg168','123456','admin@qq.com');
insert into t_user(`username`,`password`,`email`) values('admin168','123456','admin@qq.com');
insert into t_user(`username`,`password`,`email`) values('lisi','123456','admin@qq.com');
insert into t_user(`username`,`password`,`email`) values('wangwu','123456','admin@qq.com');

create table t_book(
	`id` int primary key auto_increment,
	`name` varchar(50),
	`author` varchar(50),
	`price`	decimal(11,2),
	`sales`	int,
	`stock` int
);


## 插入初始化测试数据
insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` ) 
values(null , 'java从入门到放弃' , '国哥' , 80 , 9999 , 9);

insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` ) 
values(null , '数据结构与算法' , '严敏君' , 78.5 , 6 , 13);

insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` ) 
values(null , '怎样拐跑别人的媳妇' , '龙伍' , 68, 99999 , 52);

insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` ) 
values(null , '木虚肉盖饭' , '小胖' , 16, 1000 , 50);

insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` ) 
values(null , 'C++编程思想' , '刚哥' , 45.5 , 14 , 95);

insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` ) 
values(null , '蛋炒饭' , '周星星' , 9.9, 12 , 53);
 
insert into t_book(`id` , `name` , `author` , `price` , `sales` , `stock` ) 
values(null , '赌神' , '龙伍' , 66.5, 125 , 535);
```

# 三、SpringBoot项目结构及其内容说明

![image-20210203172321300](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210203172321300.png)

#### ①说明

主要存放Base类，包括通用的一些pojo以及mapper以及utils等。

现在想将生成的代码都生成在BaseModules下

#### ②说明

MyBatis逆向生成的module，主要是main方法、My.properties(存放链接信息)，MyBatis-Generate.xml(存放生成代码配置参数)

#### ③说明

主要是main方法，读取配置，自动生成。

#### ④说明

My.properties(存放链接信息)，MyBatis-Generate.xml(存放生成代码配置参数)

#### ⑤说明

pom.xml文件，一些依赖问题

# 四、各重要部分内容

## 1、pom.xml

```xml
   <dependencies>
        <!-- https://mvnrepository.com/artifact/org.mybatis.generator/mybatis-generator-core -->
        <dependency>
            <groupId>org.mybatis.generator</groupId>
            <artifactId>mybatis-generator-core</artifactId>
            <version>1.3.7</version>
        </dependency>
        <!-- 链接mysql服务器的包，没有会把报错 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <!-- 逆向工程的插件，没有的话会报错 -->
            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.3.2</version>
                <configuration>
                    <verbose>true</verbose>
                    <overwrite>true</overwrite>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

## 2、My.properties

```properties
# 数据库链接
mybatis.datasource.url=jdbc:mysql://{ip}:{port}/{database}?serverTimezone=UTC&useUnicode=true&characterEncoding=UTF-8&zeroDateTimeBehavior=convertToNull
# 用户名
mybatis.datasource.username=admin
# 用户密码
mybatis.datasource.password=pwd
```

## 3、MyBatis-Generate.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <properties resource="My.properties"/>
    <!--MyBatis可以生产两种版本
    豪华版：除了CRUD还有大量的查询         targetRuntime="MyBatis3"
    简单版：只有CRUD                        targetRuntime="MyBatis3Simple"
    -->
    <context id="DB2Tables" targetRuntime="MyBatis3">

        <!-- 去掉全部的注释 -->
        <commentGenerator>
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>

        <!--
            修改数据库连接的属性信息
        -->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="${mybatis.datasource.url}"
                        userId="${mybatis.datasource.username}"
                        password="${mybatis.datasource.password}">
        </jdbcConnection>

        <!--是否启用 BigDecimal-->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>

        <!--JavaBean的生成配置
                targetPackage   生成的类的包名
                targetProject   生成的类在哪个模块目录下
        -->
        <javaModelGenerator targetPackage="com.zhanc.pojo"
                            targetProject=".\BaseModule\src\main\java">
            <property name="enableSubPackages" value="true"/>
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>

        <!--mapper.xml的生成配置
                targetPackage   生成的配置文件在哪个包名下
                targetProject   生成的文件在哪个模块目录下
        -->
        <sqlMapGenerator targetPackage="com.zhanc.mapper"
                         targetProject=".\BaseModule\src\main\resources">
            <property name="enableSubPackages" value="true"/>
        </sqlMapGenerator>
        <!--mapper的生成配置
                targetPackage   包名
                targetProject   哪个模块目录下
        -->
        <javaClientGenerator type="XMLMAPPER"
                             targetPackage="com.zhanc.mapper"
                             targetProject=".\BaseModule\src\main\java">
            <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>

        <!--
            一个table标签表示一个表
                tableName表示表名
                domainObjectName表示生成的类名
        -->
        <table tableName="t_student" domainObjectName="Student"/>

        <table tableName="t_user" domainObjectName="User">
            <columnOverride column="user_name" javaType="java.lang.String" jdbcType="VARCHAR" />
        </table>

    </context>
</generatorConfiguration>
```

## 4、StartRenerate

```java
import org.mybatis.generator.api.MyBatisGenerator;
import org.mybatis.generator.config.Configuration;
import org.mybatis.generator.config.xml.ConfigurationParser;
import org.mybatis.generator.exception.InvalidConfigurationException;
import org.mybatis.generator.exception.XMLParserException;
import org.mybatis.generator.internal.DefaultShellCallback;

import java.io.File;
import java.io.IOException;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;

public class StartRenerate {
    public static void main(String[] args) throws IOException, XMLParserException, InvalidConfigurationException, SQLException, InterruptedException {
        List<String> warnings = new ArrayList<String>();
        boolean overwrite = true;
        File configFile = new File("MyBatisGenerate/src/main/resources/MyBatis-Generate.xml");
        ConfigurationParser cp = new ConfigurationParser(warnings);
        Configuration config = cp.parseConfiguration(configFile);
        DefaultShellCallback callback = new DefaultShellCallback(overwrite);
        MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
        myBatisGenerator.generate(null);
    }

}
```

# 五、执行，生成代码

执行main方法

![image-20210203173644051](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210203173644051.png)