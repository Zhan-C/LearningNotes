# 一、SpringBoot特点

## 1、依赖管理

- ### 父项目做依赖管理

  ```xml
  依赖管理    
  <parent>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-parent</artifactId>
          <version>2.3.4.RELEASE</version>
  </parent>
  
  他(spring-boot-starter-parent)的父项目
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-dependencies</artifactId>
      <version>2.3.4.RELEASE</version>
    </parent>
  
  几乎声明了所有开发中常用的依赖的版本号,自动版本仲裁机制
  ```

- ### 开发导入starter场景启动器

  - 见到很多 <font color=red>spring-boot-starter-* </font>： *就某种场景（官方自己做的starter，官方starter不需要自己写version）

  - 见到的  <font color=red>*-spring-boot-starter</font>： 第三方为我们提供的简化开发的场景启动器。

  - 只要引入starter，这个场景的所有常规需要的依赖我们都自动引入

  - pringBoot所有支持的场景https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-starter

  - 所有场景启动器最底层的依赖

    ```xml
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter</artifactId>
      <version>2.3.4.RELEASE</version>
      <scope>compile</scope>
    </dependency>
    ```

- ### 无需关注版本号，自动版本仲裁（仲裁：意思是版本控制）

  - 引入依赖默认都可以不写版本
  - 引入非版本仲裁的jar，要写版本号。

- ### 可以修改默认版本号

  - 查看spring-boot-dependencies里面规定当前依赖的版本 用的 key。

  - 在当前项目里面重写配置

    ```xml
    <properties>
            <mysql.version>5.1.43</mysql.version>
    </properties>
    ```

## 2、自动配置

- 自动配好Tomcat
- 自动配好SpringMVC
- 自动配好Web常见功能
- 默认包结构
- 各种配置拥有默认值
- 按需加载所有自动配置项
- ......