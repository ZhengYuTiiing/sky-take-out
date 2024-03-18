# 外卖项目
一款专门为餐饮企业（餐厅、饭店）定制的一款软件产品。

包含**用户端**和**管理端**
![image](https://github.com/ZhengYuTiiing/sky-take-out/assets/113531299/83517aae-65da-4e4f-8a0f-b093d05d4c7f)

本项目集中关注点是开发工程师的代码实现，在软件的需求分析、数据库设计、原型设计等已经实现的基础上进行开发。
涉及到的技术栈如下：
![image](https://github.com/ZhengYuTiiing/sky-take-out/assets/113531299/e19a26e2-6403-4e67-8c12-617e4d84a84a)

## day01
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

**注：**upstream后面的名称可自定义，但要上下保持一致。



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
• Header
• Payload
• Signature
类似于xxxx.xxxx.xxxx格式,例如:
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
