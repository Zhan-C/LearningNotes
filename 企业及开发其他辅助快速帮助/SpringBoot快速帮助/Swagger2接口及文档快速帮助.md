# 一、导语

相信无论是前端还是后端开发，都或多或少地被接口文档折磨过。前端经常抱怨后端给的接口文档与实际情况不一致。后端又觉得编写及维护接口文档会耗费不少精力，经常来不及更新。其实无论是前端调用后端，还是后端调用后端，都期望有一个好的接口文档。但是这个接口文档对于程序员来说，就跟注释一样，经常会抱怨别人写的代码没有写注释，然而自己写起代码起来，最讨厌的，也是写注释。所以仅仅只通过强制来规范大家是不够的，随着时间推移，版本迭代，接口文档往往很容易就跟不上代码了。



# 二、Swagger是什么？它能干什么？

![img](https://upload-images.jianshu.io/upload_images/813533-774318cdb25c338d.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

发现了痛点就要去找解决方案。解决方案用的人多了，就成了标准的规范，这就是Swagger的由来。通过这套规范，你只需要按照它的规范去定义接口及接口相关的信息。再通过Swagger衍生出来的一系列项目和工具，就可以做到生成各种格式的接口文档，生成多种语言的客户端和服务端的代码，以及在线接口调试页面等等。这样，如果按照新的开发模式，在开发新版本或者迭代版本的时候，只需要更新Swagger描述文件，就可以自动生成接口文档和客户端服务端代码，做到调用端代码、服务端代码以及接口文档的一致性。

但即便如此，对于许多开发来说，编写这个yml或json格式的描述文件，本身也是有一定负担的工作，特别是在后面持续迭代开发的时候，往往会忽略更新这个描述文件，直接更改代码。久而久之，这个描述文件也和实际项目渐行渐远，基于该描述文件生成的接口文档也失去了参考意义。所以作为Java届服务端的大一统框架Spring，迅速将Swagger规范纳入自身的标准，建立了Spring-swagger项目，后面改成了现在的Springfox。通过在项目中引入Springfox，可以扫描相关的代码，生成该描述文件，进而生成与代码一致的接口文档和客户端代码。这种通过代码生成接口文档的形式，在后面需求持续迭代的项目中，显得尤为重要和高效。



# 三、SpringBoot快速整合Swagger2

## 1、Maven导入依赖

官方推荐里说只需要前面<font color=red>两个依赖</font>>就可以了，**但实测只导入上面两个依赖的话，后台会报依赖，网上查询加上下面两个依赖后不报异常了**，原因未知。

```maven
		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger2</artifactId>
			<version>2.9.2</version>
		</dependency>
		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger-ui</artifactId>
			<version>2.9.2</version>
		</dependency>
		<dependency>
			<groupId>io.swagger</groupId>
			<artifactId>swagger-annotations</artifactId>
			<version>1.5.22</version>
		</dependency>
		<dependency>
			<groupId>io.swagger</groupId>
			<artifactId>swagger-models</artifactId>
			<version>1.5.22</version>
		</dependency>
```

## 2、添加配置类（选一种即可）

### 2.1 配置类方式一

**注：**<font color=red>配置方式一</font>是配置了两个分组的接口文档，我创建的是一个是给前端人员的接口，一个是后台管理项目的接口，这样方便前端人员看接口时，只需要看他们所需的接口

```java
@Configuration
@EnableSwagger2
public class SwaggerConfig {
    /****
     * 前端接口配置
     * @return
     */
    @Bean
    public Docket getClientDocket() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                //分组名称
                .groupName("ClientApi")
                .select()
                //扫描的包名称
                .apis(RequestHandlerSelectors.basePackage("com.flower.controller.client"))
                .paths(PathSelectors.any())
                .build();
    }

    /****
     * 后台管理接口配置
     * @return
     */
    @Bean
    public Docket getBackgroundDocket() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .groupName("BackgroundApi")
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.gyd.flower.background"))
                .paths(PathSelectors.any())
                .build();
    }

    /***
     * 构建 api文档的详细信息函数
     * @return
     */
    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("线上观花-api接口文档")
                .description("powered by By gyd")
                .termsOfServiceUrl("http://www.by-health.com/")
                .contact(new Contact("name", "url", "email"))
                .version("1.0")
                .build();
    }
}
```



### 2.2 配置类方式二

```java
@Configuration
@EnableSwagger2
public class SwaggerConfig {

    @Bean
    public Docket webApiConfig() {
        return new Docket(DocumentationType.SWAGGER_2)
                .groupName("webApi")
                .apiInfo(webApiInfo())
                .select()
                .paths(Predicates.not(PathSelectors.regex("/admin/.*")))
                .paths(Predicates.not(PathSelectors.regex("/error.*")))
                .build();
    }

    private ApiInfo webApiInfo() {
        return new ApiInfoBuilder()
                .title("exam-server中心API文档")
                .description("本文档描述了exam-server服务接口定义")
                .version("1.0")
                .contact(new Contact("Achuang", "www.achuang.top", "1434218274@qq.com"))
                .build();
    }
}
```

3、启动项目

通过项目路径+swagger-ui.html打开接口文档

注：比如我项目端口是8080，启动之后 localhost:8080/swagger-ui.html

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020042716203162.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDkwNjI3MQ==,size_16,color_FFFFFF,t_70)

# 四、Swagger常用注解

```
@Api()
用于类，标识这个类是swagger的资源 ，主要用在controller类上，会在接口文档上显示当前类说明
```

```
@ApiOperation()
用于方法，在接口文档上面对接口进行说明，是swagger最主要的注解
```

```
@ApiParam()
用于方法，参数，字段说明，在方法上面对参数进行说明，会在接口文档上面显示参数的说明
```

```
@ApiModel()
用于类，表示对类进行说明，用于参数用实体类接收时，在接口文档上面会显示这个类里所有字段的说明 
```

```
@ApiIgnore()
用于类，方法，方法参数，表示这个方法或者类被忽略，即不会显示在接口文档里面
```

```
@ApiImplicitParam()
用于方法，表示单独的请求参数，多数时候可以用@ApiParm替代
```

```
@ApiImplicitParams() 
用于方法，包含多个 @ApiImplicitParam
```

```
@ApiResponses
同@ApiImplicitParams() ，用于方法，会在接口文档里面对当前接口返回的信息进行说明
```

# 五、注解用途测试图示

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200427164416289.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDkwNjI3MQ==,size_16,color_FFFFFF,t_70)

**这是对实体类说明的注解，这样会在接口文档中显示当前类所有字段的说明**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200427164907194.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDkwNjI3MQ==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200427165047532.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDkwNjI3MQ==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200427164805601.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDkwNjI3MQ==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200427165156717.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDkwNjI3MQ==,size_16,color_FFFFFF,t_70)

# 六、配置全局code返回状态码

swagger默认显示的状态码可能不是我们项目设定的，那么可以根据自己的项目需求设定所有接口的返回码，如下例

```java
    @Bean
    public Docket createRestApi() {
    	//设定全局code为0表示成功，200表示失败
        List<ResponseMessage> responseMessageList = new ArrayList<>();
        responseMessageList.add(new ResponseMessageBuilder().code(0).message("成功").build());
        responseMessageList.add(new ResponseMessageBuilder().code(200).message("失败").build());

        return new Docket(DocumentationType.SWAGGER_2)
                .globalResponseMessage(RequestMethod.GET, responseMessageList)
                .globalResponseMessage(RequestMethod.POST, responseMessageList)
                .enable(swaggerShow)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.any())
                .paths(PathSelectors.any())
                .build();
    }
```

# 七、用实体类接收参数或返回数据配置

当我们用实体类返回数据时，需要我们的VO对象为泛型类型，这样才会在接口文档里面展示这个实体类的各个字段意思，VO对象如下：

```java
@AllArgsConstructor
@NoArgsConstructor
@Data
@ApiModel("Result")
public class Result<T> {
    /**
     * 1.status状态值：代表本次请求response的状态结果。
     */
    @ApiModelProperty("状态码")
    private Integer status;
    /**
     * 2.response描述：对本次状态码的描述。
     */
    @ApiModelProperty("描述")
    private String msg;
    /**
     * 3.data数据：本次返回的数据。
     */
    @ApiModelProperty("数据")
    private T data;

    /**
     * 成功，创建ResResult：没data数据
     */
    public static Result suc() {
        Result result = new Result();
        result.setResultCode(ResultCode.SUCCESS);
        return result;
    }

    /**
     * 成功，创建ResResult：有data数据
     */
    public static Result suc(Object data) {
        Result result = new Result();
        result.setResultCode(ResultCode.SUCCESS);
        result.setData(data);
        return result;
    }

    /**
     * 失败，指定status、desc
     */
    public static Result fail(Integer status, String desc) {
        Result result = new Result();
        result.setStatus(status);
        result.setMsg(desc);
        return result;
    }

    /**
     * 失败，指定ResultCode枚举
     */
    public static Result fail(ResultCode resultCode) {
        Result result = new Result();
        result.setResultCode(resultCode);
        return result;
    }

    /**
     * 把ResultCode枚举转换为ResResult
     */
    private void setResultCode(ResultCode code) {
        this.status = code.code();
        this.msg = code.message();
    }
}
```

ResultCode是枚举类，这样可以灵活的设置的各种状态对应的状态码及提示，代码如下：

```java
public enum ResultCode {

	/* 成功状态码 */
	SUCCESS(0, "成功"),

	/* 失败状态码 */
	FAIL(200, "失败"),

	/* 系统500错误*/
	SYSTEM_ERROR(10000, "系统异常，请稍后重试"),
	UNAUTHORIZED(10401, "签名验证失败"),
	TEST(500, "测试异常"),

	/* 参数错误：10001-19999 */
	PARAM_IS_INVALID(10001, "参数无效"),

	/* 用户错误：20001-29999*/
	USER_HAS_EXISTED(20001, "用户名已存在"),
	USER_NOT_FIND(20002, "用户名不存在");
	private Integer code;

	private String message;

	ResultCode(Integer code, String message) {
		this.code = code;
		this.message = message;
	}

	public Integer code() {
		return this.code;
	}

	public String message() {
		return this.message;
	}

}
```

这样在返回数据时，直接用泛型类返回，在接口文档中才会显示实体类中的各个字段的意思，如果是返回的map的话，暂时还没找到处理办法

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200427165748434.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDkwNjI3MQ==,size_16,color_FFFFFF,t_70)

当我们返回实体类时，比如实体类里有的字段是前端人员不需要的字段，而且返回的也是null，这时可以在实体类里面加入注解，让此实体类中某个字段为null时，自动不返回

```java
//为null则不返回
@JsonInclude(JsonInclude.Include.NON_NULL)
//为空不返回
@JsonInclude(JsonInclude.Include.NON_EMPTY)  
//此注解放在字段上面，则永远不会返回
@JsonIgnore
```

properties文件里面也可以设置全局返回规则

```properties
#为null不返回
spring.jackson.default-property-inclusion=non_null
```



参考文章：

https://blog.csdn.net/weixin_44906271/article/details/105792809