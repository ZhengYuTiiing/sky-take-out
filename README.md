# 外卖项目
一款专门为餐饮企业（餐厅、饭店）定制的一款软件产品。

包含**用户端**和**管理端**
![image](https://github.com/ZhengYuTiiing/sky-take-out/assets/113531299/83517aae-65da-4e4f-8a0f-b093d05d4c7f)

本项目集中关注点是开发工程师的代码实现，在软件的需求分析、数据库设计、原型设计等已经实现的基础上进行开发。
涉及到的技术栈如下：
![image](https://github.com/ZhengYuTiiing/sky-take-out/assets/113531299/e19a26e2-6403-4e67-8c12-617e4d84a84a)

## day01 
实现开发环境的搭建，导入已有的前端页面和基础框架。熟悉项目结构、按照数据库设计创建所需的数据库及数据、完善登录功能、学习Swagger、学习jwt令牌、将提供的项目接口导入YApi。

### 前端环境搭建
首先，前端工程基于 nginx 运行，双击 nginx.exe 即可启动 nginx 服务，访问端口号为 80.

**1). nginx反向代理**

**nginx 反向代理**，就是将前端发送的动态请求由 nginx 转发到后端服务器

那为什么不直接通过浏览器直接请求后台服务端，需要通过nginx反向代理呢？


**nginx 反向代理的好处：**

- 提高访问速度

  因为nginx本身可以进行缓存，如果访问的同一接口，并且做了数据缓存，这时nginx就直接可把数据返回，不需要真正地访问服务端，从而提高访问速度。

- 进行负载均衡

  所谓负载均衡,就是把大量的请求按照我们指定的方式均衡的分配给集群中的每台服务器。系统上线之后，可能由于访问压力较大，后端会部署多台服务器来构成机器。nginx负载均衡器，前端发来的大量请求，可以均匀地分发给各个服务器。

- 保证后端服务安全

  因为一般后台服务地址不会暴露，所以使用浏览器不能直接访问，可以把nginx作为请求访问的入口，请求到达nginx后转发到具体的服务中，从而保证后端服务的安全。

**2). nginx配置**

**nginx 负载均衡的配置方式：**
```nginx
upstream webservers{
    server 192.168.100.128:8080;
    server 192.168.100.129:8080;
}
server{
    listen 80;
    server_name localhost;
    
    location /api/{
        proxy_pass http://webservers/admin;#负载均衡
    }
}
```
如上代码的含义是：监听80端口号， 然后当我们访问 http://localhost:80/api/../..这样的接口的时候，它会通过 location /api/ {} 这样的反向代理到 http://webservers/admin，根据webservers名称找到一组服务器，根据设置的负载均衡策略(默认是轮询)转发到具体的服务器。


**nginx 负载均衡策略：**

| **名称**   | **说明**                                               |
| ---------- | ------------------------------------------------------ |
| 轮询       | 默认方式                                               |
| weight     | 权重方式，默认为1，权重越高，被分配的客户端请求就越多  |
| ip_hash    | 依据ip分配方式，这样每个访客可以固定访问一个后端服务   |
| least_conn | 依据最少连接方式，把请求优先分配给连接数少的后端服务   |
| url_hash   | 依据url分配方式，这样相同的url会被分配到同一个后端服务 |
| fair       | 依据响应时间方式，响应时间短的服务将会被优先分配       |

### jwt令牌
一般都用于登录，jwt由三个部分组成
- header（报头）：通常由两部分组成: Token的类型(即 JWT)和所使用的签名算法(如 HMAC SHA256或 RSA)。使用Base64编码组成；（Base64是一种编码，不是一种加密过程，可以被翻译成原来的样子）
- payload（有效负载）：就是自定义的东西，登录的用户名密码这些，同样使用Base64编码
- signature（签名）：保证JWT没有被篡改过。JWT令牌中只要有一个字符被篡过，都会失效。不适用编码，所以不能解码出原始的数据。

类似于xxxx.xxxx.xxxx格式,例如:
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c

在EmployeeController中，登录成功后，生成jwt令牌。将令牌响应给客户端，下次再次发送请求时，要携带这个jwt令牌，如果有jwt令牌就可以进行更多的操作。没有就登录。类似于session。
### Swagger
Swagger 是一个规范和完整的框架，用于生成、描述、调用和可视化 RESTful 风格的 Web 服务(<https://swagger.io/>)。 它的主要作用是：

1. 使得前后端分离开发更加方便，有利于团队协作

2. 接口的文档在线自动生成，降低后端开发人员编写接口文档的负担

3. 功能测试 

   Spring已经将Swagger纳入自身的标准，建立了Spring-swagger项目，现在叫Springfox。通过在项目中引入Springfox ，即可非常简单快捷的使用Swagger。
knife4j是为Java MVC框架集成Swagger生成Api文档的增强解决方案,前身是swagger-bootstrap-ui,取名kni4j是希望它能像一把匕首一样小巧,轻量,并且功能强悍!
**目前，一般都使用knife4j框架。**

#### Swagger常用注解

通过注解可以控制生成的接口文档，使接口文档拥有更好的可读性，常用注解如下：

| **注解**          | **说明**                                               |
| ----------------- | ------------------------------------------------------ |
| @Api              | 用在类上，例如Controller，表示对类的说明               |
| @ApiModel         | 用在类上，例如entity、DTO、VO                          |
| @ApiModelProperty | 用在属性上，描述属性信息                               |
| @ApiOperation     | 用在方法上，例如Controller的方法，说明方法的用途、作用 |
#### Swagger Knife4j 使用步骤
都是很固定的，导入坐标，配置类写一下，然后加上注解就可以生成在线接口文档。
1. 导入 knife4j 的maven坐标

   在pom.xml中添加依赖

   ```xml
   <dependency>
      <groupId>com.github.xiaoymin</groupId>
      <artifactId>knife4j-spring-boot-starter</artifactId>
   </dependency>
   ```

2. 在配置类中加入 knife4j 相关配置

   WebMvcConfiguration.java

   ```java
   /**
        * 通过knife4j生成接口文档
        * @return
   */
       @Bean
       public Docket docket() {
           ApiInfo apiInfo = new ApiInfoBuilder()
                   .title("苍穹外卖项目接口文档")
                   .version("2.0")
                   .description("苍穹外卖项目接口文档")
                   .build();
           Docket docket = new Docket(DocumentationType.SWAGGER_2)
                   .apiInfo(apiInfo)
                   .select()
                   .apis(RequestHandlerSelectors.basePackage("com.sky.controller"))
                   .paths(PathSelectors.any())
                   .build();
           return docket;
       }
   ```

   

3. 设置静态资源映射，否则接口文档页面无法访问

   WebMvcConfiguration.java

   ```java
   /**
        * 设置静态资源映射
        * @param registry
   */
   protected void addResourceHandlers(ResourceHandlerRegistry registry) {
           registry.addResourceHandler("/doc.html").addResourceLocations("classpath:/META-INF/resources/");
           registry.addResourceHandler("/webjars/**").addResourceLocations("classpath:/META-INF/resources/webjars/");
   }
   ```

4. 访问测试

   接口文档访问路径为 http://ip:port/doc.html ---> http://localhost:8080/doc.html


**思考**:通过 Swagger 就可以生成接口文档，那么我们就不需要 Yapi 了？

1、Yapi 是设计阶段使用的工具，管理和维护接口

2、Swagger 在开发阶段使用的框架，帮助后端开发人员做后端的接口测试

### 完善登录功能——MD5加密

原本设计的员工表中的密码是明文存储，安全性太低。因此需要**使用MD5加密方式对明文密码加密**，加密后存储以提高安全性。
- MD5 是什么？
全称为 消息摘要算法版本5 （Message Digest Algorithm 5）它是一种 Hash 算法。作用是为了信息安全。

再具体点，MD5 值就是一串 128 bit 的数据。**MD5 的核心是通过算法把任意长度的原始数据映射成128 bit 的数据。**MD5 的特点：

不可逆性 --- 只能将明文进行加密得到密文，不能由密文算出原文。

Spring提供了DigestUtild工具类
```
//进行md5加密，然后再进行比对
password = DigestUtils.md5DigestAsHex(password.getBytes());
if (!password.equals(employee.getPassword())) {  //密码错误
  throw new PasswordErrorException(MessageConstant.PASSWORD_ERROR);
}
```
## day02
实现新增员工、员工分页查询、启用禁用员工账号、编辑员工、导入分类模块功能代码.其中分类模块与员工模块的内容几乎一致，因此直接导入提供好的代码。
个人认为新增、分页查询、根据id删除、修改、启用禁用、根据类型查询都是对数据库的CRUD,并没有太大的难点，今日总结在开发过程中学习到的新知识。

 ### 1.对象属性拷贝
BeanUtils.copyProperties(employeeDTO, employee);

BeanUtils提供对Java反射和自省API的包装。其主要目的是利用反射机制对JavaBean的属性进行处理。如果你有两个甚至需要很多个 具有很多相同属性的JavaBean，那创建时候不是要不断重复赋值，而我们使用 BeanUtils.copyProperties() 方法以后，代码量大大的减少，而且整体程序看着也简洁明朗。但要注意，有两个包下都有BeanUtils工具类，且方法的名称也相同都为copyProperties，但是赋值的方向不同。
org.springframework.beans中的BeanUtils.copyProperties(A,B);是A中的值赋给B
org.apache.commons.beanutils;BeanUtils.copyProperties(A,B);是B中的值赋给A

### 2.异常处理器
 Springboot对于异常的处理也做了不错的支持，集中的、统一的处理项目中出现的异常.它提供了一个 @ControllerAdvice注解以及 @ExceptionHandler注解，前者是用来开启全局的异常捕获，后者则是说明捕获哪些异常，对那些异常进行处理。
```
@ControllerAdvice
public class MyExceptionHandler {
    @ExceptionHandler(value =Exception.class)
	public String exceptionHandler(Exception e){
		System.out.println("发生了一个异常"+e);
       	return e.getMessage();
    }
}
```
@RestControllerAdvice是一个组合注解，由@ControllerAdvice、@ResponseBody组成，而@ControllerAdvice继承了@Component，因此@RestControllerAdvice本质上是个Component，用于定义@ExceptionHandler
![image](https://github.com/ZhengYuTiiing/sky-take-out/assets/113531299/6912b4fc-6845-44a8-b8e1-28fcb6081556)

### 3.ThreadLocal
**介绍：**

ThreadLocal 并不是一个Thread，而是Thread的局部变量。
ThreadLocal为每个线程提供单独一份存储空间，具有线程隔离的效果，只有在线程内才能获取到对应的值，线程外则不能访问。

**常用方法：**

- public void set(T value) 	设置当前线程的线程局部变量的值
- public T get() 		返回当前线程所对应的线程局部变量的值
- public void remove()        移除当前线程的线程局部变量

  在本项目中，可以在解析jwt令牌时获取当前登录员工id，并。用BaseContext工具类中的setCurrentId方法（底层调用了threadlocal的set方法）放入线程局部变量中，之后在修改操作时(BaseContext.getCurrentId()）取出即可。

### 4.消息转换器

在开发过程中发现，时间字段显示有问题，没有按照"yyyy-MM-dd HH:mm"的格式显示。
**解决方式：**  
**1).  方式一**  
在属性上加上注解，对日期进行格式化  
```
    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    private LocalDateTime createTime;
    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    private LocalDateTime updateTime;
```
但这种方式，需要在每个时间属性上都要加上该注解，使用较麻烦，不能全局处理。
**2).  方式二（推荐)消息转换器**  
在WebMvcConfiguration中扩展SpringMVC的消息转换器，统一对日期类型进行格式处理
```java
	/**
     * 扩展Spring MVC框架的消息转化器
     * @param converters
     */
    protected void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
        log.info("扩展消息转换器...");
        //创建一个消息转换器对象
        MappingJackson2HttpMessageConverter converter = new MappingJackson2HttpMessageConverter();
        //需要为消息转换器设置一个对象转换器，对象转换器可以将Java对象序列化为json数据
        converter.setObjectMapper(new JacksonObjectMapper());
        //将自己的消息转化器加入容器中
        converters.add(0,converter);
    }
```
时间格式定义，sky-common模块中
```java
package com.sky.json;
public class JacksonObjectMapper extends ObjectMapper {
	//.......
    public static final String DEFAULT_DATE_TIME_FORMAT = "yyyy-MM-dd HH:mm";
    //.......
    }
}
```
对象映射器:基于jackson将Java对象转为json，或者将json转为Java对象  
将JSON解析为Java对象的过程称为 [从JSON反序列化Java对象]  
从Java对象生成JSON的过程称为 [序列化Java对象到JSON]  

### 5.builder  
创建对象的另一种方式，按照传统的new一个对象，之后用set方法给属性赋值。但是如果类的属性很多，那么代码会很繁杂。

在类上添加@Builder注解，那么在创建对象时，可以使用链式代码，看起来更简洁。
```
/*      Employee employee=new Employee();
        employee.setId(id);
        employee.setStatus(status);*/

        Employee employee = Employee.builder()
                .status(status)
                .id(id)
                .build();
```

### 6.PageHelper
PageHelper是一款好用的开源免费的Mybatis第三方物理分页插件，大致理解为该插件可以在你的sql语句后面加上limit，你只需要告诉PageHelper要查第几页，每页几条数据。
sql语句最后的limit几到几插件帮你生成，就不需要自己计算了。
```
    public PageResult pageQuary(EmployeePageQueryDTO employeePageQueryDTO) {
        //开始分页查询,告诉PageHelper要查第几页，每页几条。动态的把limit拼接在sql语句之后。自己还是要写基础的sql
        PageHelper.startPage(employeePageQueryDTO.getPage(),employeePageQueryDTO.getPageSize());
        //这个返回值必须是Page<E>
        Page<Employee> page=employeeMapper.pageQuery(employeePageQueryDTO);
        long total = page.getTotal();
        List<Employee> records = page.getResult();
        return  new PageResult(total, records);
    }
```
## day03
实现：公共字段自动填充、新增菜品、菜品分页查询、删除菜品、修改菜品  
### 1.公共字段自动填充功能
**AOP面向切面编程思想**  
在新增修改员工、新增修改菜品时，我们需要重复的去获取时间获取当前用户。
```
        category.setCreateTime(LocalDateTime.now());
        category.setUpdateTime(LocalDateTime.now());
        category.setCreateUser(BaseContext.getCurrentId());
        category.setUpdateUser(BaseContext.getCurrentId());
```
需要在每一个业务方法中进行操作, 编码相对冗余、繁琐。 我们使用AOP切面编程，实现功能增强，来完成公共字段自动填充功能。
自定义切面类，类中定义切入点和通知。注意切面类上应有@Aspect 、@Component注解。  
```
@Aspect
@Component
@Slf4j
public class AutoFillAspect {

    @Pointcut("execution(* com.sky.mapper.*.*(..)) && @annotation(com.sky.annotation.AutoFill)")
    public void autoFillPointCut(){}

    @Before("autoFillPointCut()")
    public void autoFill(JoinPoint joinPoint){
        ...
    }
}
```
**自定义注解**  
@Target – 注解用于什么地方
@Retention – 什么时候使用该注解，通过@Retention定义注解的生命周期，RetentionPolicy.RUNTIME : 始终不会丢弃，可以使用反射获得该注解的信息。自定义的注解最常用的使用方式。
```
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface AutoFill {
    //数据库操作类型：UPDATE INSERT
    OperationType value();
}
```
**反射**  
    在通知中，需要获取得到对象，得到setter方法,调用setter方法为对象属性赋值，这时就需要用到反射机制。  
**getDeclaredMethod(autoMatchConfig.getMethodName(), Integer.class)**
其中，传入的第一个参数是方法名，第二个参数名是方法参数，传入这两个参数之后，便可以根据方法名和方法参数通过反射获取带有参数的方法.然后 **MethodName.invoke(subjectService, GlobalConfig.OPEN_TO_IPLAN)**
其中，invoke方法中传入的是上一步获取到的方法的实例对象和方法传入的实参

由此，通过反射获取方法名和参数名，然后invoke方法注入方法对象和实参，
getDeclaredMethod：返回Method方法对象；
invoke：根据传入的对象实例，通过配置的实参参数来调用方法
```
  Method setCreateTime = entity.getClass().getDeclaredMethod(AutoFillConstant.SET_CREATE_TIME, LocalDateTime.class);
  Method setCreateUser = entity.getClass().getDeclaredMethod(AutoFillConstant.SET_CREATE_USER, Long.class);
  Method setUpdateTime = entity.getClass().getDeclaredMethod(AutoFillConstant.SET_UPDATE_TIME, LocalDateTime.class);
  Method setUpdateUser = entity.getClass().getDeclaredMethod(AutoFillConstant.SET_UPDATE_USER, Long.class);
 //通过反射为对象属性赋值
 setCreateTime.invoke(entity,now);
 setCreateUser.invoke(entity,currentId);
 setUpdateTime.invoke(entity,now);
 setUpdateUser.invoke(entity,currentId);
```
### 2.文件上传
因为在新增菜品时，需要上传菜品对应的图片(文件)，包括后绪其它功能也会使用到文件上传，故要实现通用的文件上传接口。我使用使用第三方的存储服务（阿里云OSS）
1. 优点：开发简单，拥有强大功能，免维护
2. 缺点：付费 

这样，服务端将前端上传的图片传到阿里云上，再将阿里云提供的url返回给前端、存入数据库。此后就可以访问url得到图片，这样前端就可以使用。

**使用阿里云OSS**  
具体如何使用OSS服务，阿里云官网上有详细的教程https://help.aliyun.com/zh/oss/developer-reference/getting-started?spm=a2c4g.11186623.0.i7  
简单的说，需要一下几步
- 注册阿里云账号: 如果您还没有阿里云账号，请先注册一个账号，并开通 OSS 服务。
- 创建 OSS Bucket: 登录阿里云控制台，在 OSS 控制台中创建一个 Bucket，用于存储上传的文件。Bucket实质就是阿里云OSS对象存储的一个存储空间
- 获取 Access Key: 在阿里云控制台获取 Access Key ID 和 Access Key Secret，这将用于身份验证和访问控制。
- 在Maven中引用依赖
- 构建OSSClient实例，上传文件

### 3.MultipartFile
MultipartFile 接口是 Spring Framework 中用于处理文件上传的核心接口，它抽象了客户端上传的文件，使开发者可以轻松处理上传的文件内容和元数据。


以下是一些常见的 MultipartFile 接口操作： 

- 获取文件名和大小：使用 getOriginalFilename() 方法获取上传的文件名，使用 getSize() 方法获取文件大小。
- 获取文件类型：使用 getContentType() 方法获取上传文件的 MIME 类型。
- 获取字节数据：使用 getBytes() 方法获取文件的字节数组。 
- 保存文件：使用 transferTo() 方法将文件保存到指定位置。

**Mybatis 参数**   
parameterType="Setmeal" :  接口中方法参数的类型。select,insert,update,delete都可以使用 parameterType 指定类型。  
useGeneratedKeys="true" keyProperty="id":   useGeneratedKeys 参数只针对 insert 语句生效，默认为 false。当设置为 true 时，表示如果插入的表以自增列为主键，则允许 JDBC 支持自动生成主键，并可将自动生成的主键返回。1.keyProperty中对应的值是实体类的属性，而不是数据库的字段。  
添加该属性之后并非改变insert方法的返回值，也就是说，该方法还是返回新增的结果。而如果需要获取新增行的主键ID，直接使用传入的实体对象的主键对应属性的值。    

## day05
今日主要是对Redis有了基本的认识。
### 1.Redis入门
Redis是一个基于**内存**的key-value结构数据库。Redis 是互联网技术领域使用最为广泛的**存储中间件**。Mysql是基于磁盘的。  
**主要特点：**  
- 基于内存存储，读写性能高  
- 适合存储热点数据（热点商品、资讯、新闻）
- 企业应用广泛

Redis是用C语言开发的一个开源的高性能键值对(key-value)数据库，官方提供的数据是可以达到100000+的QPS（每秒内查询次数）。它存储的value类型比较丰富，也被称为结构化的NoSql数据库。

NoSql（Not Only SQL），不仅仅是SQL，泛指**非关系型数据库**。NoSql数据库并不是要取代关系型数据库，而是关系型数据库的补充。

**关系型数据库(RDBMS)：**

- Mysql
- Oracle
- DB2
- SQLServer

**非关系型数据库(NoSql)：**

- Redis
- Mongo db
- MemCached

### Redis服务启动与停止（window版） 
服务启动命令：**redis-server.exe redis.windows.conf**  
Redis服务默认端口号为 **6379** ，通过快捷键**Ctrl + C** 即可停止Redis服务，当Redis服务启动成功后，可通过客户端进行连接。  

客户端连接命令：**redis-cli.exe**

通过redis-cli.exe命令默认连接的是本地的redis服务，并且使用默认6379端口。也可以通过指定如下参数连接：

- -h ip地址
- -p 端口号
- -a 密码（如果需要）

#### 1.3.3 修改Redis配置文件

设置Redis服务密码，修改redis.windows.conf

```
requirepass 123456
```

**注意：**

- 修改密码后需要重启Redis服务才能生效
- Redis配置文件中 # 表示注释

重启Redis后，再次连接Redis时，需加上密码，否则连接失败。

```
redis-cli.exe -h localhost -p 6379 -a 123456
```
![1](https://github.com/ZhengYuTiiing/sky-take-out/assets/113531299/25e4c9fd-b2a3-4800-a1b4-00a428727cec)


此时，-h 和 -p 参数可省略不写。

![2](https://github.com/ZhengYuTiiing/sky-take-out/assets/113531299/cd334b1d-c8c0-410b-9925-70432c6ef4cc)




### 2.Redis数据类型

**五种常用数据类型介绍**

Redis存储的是key-value结构的数据，其中key是字符串类型，value有5种常用的数据类型：

- 字符串 string
- 哈希 hash
- 列表 list
- 集合 set
- 有序集合 sorted set / zset

![image](https://github.com/ZhengYuTiiing/sky-take-out/assets/113531299/dbe5d795-dff7-4476-acaa-b42633a419d8)


**解释说明：**

- 字符串(string)：普通字符串，Redis中最简单的数据类型
- 哈希(hash)：也叫散列，类似于Java中的HashMap结构
- 列表(list)：按照插入顺序排序，可以有重复元素，类似于Java中的LinkedList
- 集合(set)：无序集合，没有重复元素，类似于Java中的HashSet
- 有序集合(sorted set/zset)：集合中每个元素关联一个分数(score)，根据分数升序排序，没有重复元素



### 3. Redis常用命令

#### 3.1 字符串操作命令

Redis 中字符串类型常用命令：

- **SET** key value 					         设置指定key的值
- **GET** key                                        获取指定key的值
- **SETEX** key seconds value         设置指定key的值，并将 key 的过期时间设为 seconds 秒
- **SETNX** key value                        只有在 key    不存在时设置 key 的值

更多命令可以参考Redis中文网：https://www.redis.net.cn


#### 3.2 哈希操作命令

Redis hash 是一个string类型的 field 和 value 的映射表，hash特别适合用于存储对象，常用命令：

- **HSET** key field value             将哈希表 key 中的字段 field 的值设为 value
- **HGET** key field                       获取存储在哈希表中指定字段的值
- **HDEL** key field                       删除存储在哈希表中的指定字段
- **HKEYS** key                              获取哈希表中所有字段
- **HVALS** key                              获取哈希表中所有值

![image](https://github.com/ZhengYuTiiing/sky-take-out/assets/113531299/9e1dfce4-106e-4af8-8b81-6318f2fc57ad)



#### 3.3 列表操作命令

Redis 列表是简单的字符串列表，按照插入顺序排序，常用命令：

- **LPUSH** key value1 [value2]         将一个或多个值插入到列表头部
- **LRANGE** key start stop                获取列表指定范围内的元素
- **RPOP** key                                       移除并获取列表最后一个元素
- **LLEN** key                                        获取列表长度
- **BRPOP** key1 [key2 ] timeout       移出并获取列表的最后一个元素， 如果列表没有元素会阻塞列表直到等待超    时或发现可弹出元素为止

![image](https://github.com/ZhengYuTiiing/sky-take-out/assets/113531299/849beb7c-9109-4304-a7d1-7c9366a08d3b)




#### 3.4 集合操作命令

Redis set 是string类型的无序集合。集合成员是唯一的，这就意味着集合中不能出现重复的数据，常用命令：

- **SADD** key member1 [member2]            向集合添加一个或多个成员
- **SMEMBERS** key                                         返回集合中的所有成员
- **SCARD** key                                                  获取集合的成员数
- **SINTER** key1 [key2]                                   返回给定所有集合的交集
- **SUNION** key1 [key2]                                 返回所有给定集合的并集
- **SREM** key member1 [member2]            移除集合中一个或多个成员

![image](https://github.com/ZhengYuTiiing/sky-take-out/assets/113531299/0ecbd78f-0af3-498f-b114-90567f2d9383)




#### 3.5 有序集合操作命令

Redis有序集合是string类型元素的集合，且不允许有重复成员。每个元素都会关联一个double类型的分数。常用命令：  
常用命令： 
- **ZADD** key score1 member1 [score2 member2]     向有序集合添加一个或多个成员
- **ZRANGE** key start stop [WITHSCORES]                     通过索引区间返回有序集合中指定区间内的成员
- **ZINCRBY** key increment member                              有序集合中对指定成员的分数加上增量 increment
- **ZREM** key member [member ...]                                移除有序集合中的一个或多个成员
![image](https://github.com/ZhengYuTiiing/sky-take-out/assets/113531299/645d8356-ca1d-4b2a-a1ff-b5b3f3ec9e9c)
#### 3.6 通用命令

Redis的通用命令是不分数据类型的，都可以使用的命令：

- KEYS pattern 		查找所有符合给定模式( pattern)的 key 
- EXISTS key 		检查给定 key 是否存在
- TYPE key 		返回 key 所储存的值的类型
- DEL key 		该命令用于在 key 存在是删除 key


### 4.在Java中操作Redis
**Spring Data Redis** 是 Spring 的一部分，提供了在 Spring 应用中通过简单的配置就可以访问 Redis 服务，对 Redis 底层开发包进行了高度封装。在 Spring 项目中，可以使用Spring Data Redis来简化 Redis 操作。  
#### Spring Data Redis环境搭建
进入到sky-server模块
**1). 导入Spring Data Redis的maven坐标**
```xml
<dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```
**2). 配置Redis数据源**
在application-dev.yml中添加
```yaml
sky:
  redis:
    host: localhost
    port: 6379
    password: 123456
    database: 10
```
**解释说明：**  
database:指定使用Redis的哪个数据库，Redis服务启动后默认有16个数据库，编号分别是从0到15。可以通过修改Redis配置文件来指定数据库的数量。  
在application.yml中添加读取application-dev.yml中的相关Redis配置
```yaml
spring:
  profiles:
    active: dev
  redis:
    host: ${sky.redis.host}
    port: ${sky.redis.port}
    password: ${sky.redis.password}
    database: ${sky.redis.database}
```

**3). 编写配置类，创建RedisTemplate对象**

```java
package com.sky.config;

import lombok.extern.slf4j.Slf4j;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.StringRedisSerializer;

@Configuration
@Slf4j
public class RedisConfiguration {

    @Bean
    public RedisTemplate redisTemplate(RedisConnectionFactory redisConnectionFactory){
        log.info("开始创建redis模板对象...");
        RedisTemplate redisTemplate = new RedisTemplate();
        //设置redis的连接工厂对象
        redisTemplate.setConnectionFactory(redisConnectionFactory);
        //设置redis key的序列化器
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        return redisTemplate;
    }
}
```

**解释说明：**

当前配置类不是必须的，因为 Spring Boot 框架会自动装配 RedisTemplate 对象，但是默认的key序列化器为

JdkSerializationRedisSerializer，导致我们存到Redis中后的数据和原始数据有差别，故设置为

StringRedisSerializer序列化器。



**4). 通过RedisTemplate对象操作Redis**

在test下新建测试类

```java
package com.sky.test;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.redis.core.*;

@SpringBootTest
public class SpringDataRedisTest {
    @Autowired
    private RedisTemplate redisTemplate;

    @Test
    public void testRedisTemplate(){
        System.out.println(redisTemplate);
        //string数据操作
        ValueOperations valueOperations = redisTemplate.opsForValue();
        //hash类型的数据操作
        HashOperations hashOperations = redisTemplate.opsForHash();
        //list类型的数据操作
        ListOperations listOperations = redisTemplate.opsForList();
        //set类型数据操作
        SetOperations setOperations = redisTemplate.opsForSet();
        //zset类型数据操作
        ZSetOperations zSetOperations = redisTemplate.opsForZSet();
    }
}
```
操作字符串类型数据用ValueOperations、操作哈希类型数据用HashOperations、操作列表类型数据用ListOperations、操作集合类型数据用SetOperations、操作有序集合类型数据用ZSetOperations、通用命令操作用redisTemplate  

## day06 
今日第一次接触小程序开发，但是这并不是本项目的重点，只是了解。今日实现微信登录功能、导入商品浏览功能代码。

### 1. HttpClient
HttpClient 是Apache Jakarta Common 下的子项目，可以用来提供高效的、最新的、功能丰富的支持 HTTP 协议的客户端编程工具包，并且它支持 HTTP 协议最新的版本和建议。简单地说，它可以在Java程序中发送http请求。

### 2.微信小程序开发
开发微信小程序之前需要注册小程序（在微信公众平台）、完善小程序信息（记录下小程序的 AppID）、下载开发者工具用于开发。   
入开发微信小程序，本质上是属于前端的开发，我们的重点其实还是后端代码开发。所以，小程序的代码已经提供好了，直接导入到微信开发者工具当中，直接来使用就可以了。

### 3. 微信登录流程
**流程图：**
![image](https://github.com/ZhengYuTiiing/sky-take-out/assets/113531299/cfc25b77-71d9-4392-874c-d65dc5870701)



**步骤分析：**

1. 小程序端，调用wx.login()获取code，就是授权码。
2. 小程序端，调用wx.request()发送请求并携带code，请求开发者服务器(自己编写的后端服务)。
3. 开发者服务端，通过HttpClient向微信接口服务发送请求，并携带appId+appsecret+code三个参数。
4. 开发者服务端，接收微信接口服务返回的数据，session_key+opendId等。opendId是微信用户的唯一标识。
5. 开发者服务端，自定义登录态，生成令牌(token)和openid等数据返回给小程序端，方便后绪请求身份校验。
6. 小程序端，收到自定义登录态，存储storage。
7. 小程序端，后绪通过wx.request()发起业务请求时，携带token。
8. 开发者服务端，收到请求后，通过携带的token，解析当前登录用户的id。
9. 开发者服务端，身份校验通过后，继续相关的业务逻辑处理，最终返回业务数据。

10. **说明：**

1. 调用 [wx.login()](https://developers.weixin.qq.com/miniprogram/dev/api/open-api/login/wx.login.html) 获取 **临时登录凭证code** ，并回传到开发者服务器。
2. 调用 [auth.code2Session](https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/login/auth.code2Session.html) 接口，换取 **用户唯一标识 OpenID** 、 用户在微信开放平台帐号下的**唯一标识UnionID**（若当前小程序已绑定到微信开放平台帐号） 和 **会话密钥 session_key**。


