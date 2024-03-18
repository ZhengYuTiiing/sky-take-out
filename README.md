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
//进行md5加密，然后再进行比对password = DigestUtils.md5DigestAsHex(password.getBytes());if (!password.equals(employee.getPassword())) {    //密码错误  throw new PasswordErrorException(MessageConstant.PASSWORD_ERROR);}
![image](https://github.com/ZhengYuTiiing/sky-take-out/assets/113531299/8bd4338f-c7ea-4308-9b3e-1a9ffa6e5513)

```
 
