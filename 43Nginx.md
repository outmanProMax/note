# 快速使用

1. Window下完成域名与IP的映射

修改C:\Windows\System32\drivers\etc\host

2. 修改nginx/conf/conf.d/gulimall.conf的监听地址

```
server {
    listen       80;
    server_name  gulimall.com;
    ……
```

3. 修改nginx/conf/nginx.conf

将upstream映射到我们的网关服务

    upstream gulimall{
        server 192.168.200.1:88;
    }

4. 修改nginx/conf/conf.d/gulimall.conf的重定向地址

接收到gulimall.com的访问后，如果是/，转交给指定的upstream，由于nginx的转发会丢失host头，造成网关不知道原host，所以我们添加头信息

```
  location / {
        proxy_pass http://gulimall;
        proxy_set_header Host $host;
    }
```

5. 配置springcloud网关

配置gateway为服务器，将域名为**.gulimall.com转发至商品服务。配置的时候注意 网关优先匹配的原则，所以要把这个配置放到后面

    - id: gulimall_host_route
          uri: lb://gulimall-product
          predicates:
            - Host=**.gulimall.com



# Nginx常用命令

进入 nginx 目录中: cd /usr/local/nginx/sbin

## **1、查看 nginx 版本号** 

./nginx -v

## **2、启动 nginx**

./nginx

查看是否启动成功：ps -ef|grep nginx

## **3、停止 nginx** 

./nginx -s stop

## **4、重新加载 nginx**

./nginx -s reload

# **配置文件中的内容（包含三部分）**

## 1、全局块：配置服务器整体运行的配置指令

从配置文件开始到 events 块之间的内容，主要会设置一些影响 nginx 服务器整体运行的配置指令，主要包括配

置运行 Nginx 服务器的用户（组）、允许生成的 worker process 数，进程 PID 存放路径、日志存放路径和类型以及配置文件的引入等。

比如上面第一行配置的：这是 Nginx 服务器并发处理服务的关键配置，worker_processes 值越大，可以支持的并发处理量也越多，但是会受到硬件、软件等设备的制约

 ![img](https://img2018.cnblogs.com/blog/1455597/201910/1455597-20191029102802614-510328292.png)

## 2、events 块：影响 Nginx 服务器与用户的网络连接

events 块涉及的指令主要影响 Nginx 服务器与用户的网络连接，常用的设置包括是否开启对多 work process下的网络连接进行序列化，是否允许同时接收多个网络连接，选取哪种事件驱动模型来处理连接请求，每个 word process 可以同时支持的最大连接数等。上述例子就表示每个 work process 支持的最大连接数为 1024.这部分的配置对 Nginx 的性能影响较大，在实际中应该灵活配置。

![img](https://img2018.cnblogs.com/blog/1455597/201910/1455597-20191029102827884-232471630.png)

 

## 3、http 块

这算是 Nginx 服务器配置中最频繁的部分，代理、缓存和日志定义等绝大多数功能和第三方模块的配置都在这里。

需要注意的是：http 块也可以包括 http 全局块、server 块。

### ①、http 全局块

http 全局块配置的指令包括文件引入、MIME-TYPE 定义、日志自定义、连接超时时间、单链接请求数上限等。

 ![img](https://img2018.cnblogs.com/blog/1455597/201910/1455597-20191029102851922-1592334025.png)

### ②、server 块

这块和虚拟主机有密切关系，虚拟主机从用户角度看，和一台独立的硬件主机是完全一样的，该技术的产生是为了

节省互联网服务器硬件成本。

每个 http 块可以包括多个 server 块，而每个 server 块就相当于一个虚拟主机。

而每个 server 块也分为全局 server 块，以及可以同时包含多个 locaton 块。

 ![img](https://img2018.cnblogs.com/blog/1455597/201910/1455597-20191029102901346-2016909563.png)

##### 全局 server 块

最常见的配置是**本虚拟机主机**的监听配置和本虚拟主机的名称或 IP 配置。

##### location 块

一个 server 块可以配置多个 location 块。

这块的主要作用是基于 Nginx 服务器接收到的请求字符串（例如 server_name/uri-string），对虚拟主机名称（也可以是 IP 别名）之外的字符串（例如 前面的 /uri-string）进行匹配，对特定的请求进行处理。地址定向、数据缓存和应答控制等功能，还有许多第三方模块的配置也在这里进行。

# Nginx作用

## 1、反向代理

反向代理对外暴露的是代理服务器地址，隐藏了真实服务器 IP 地址，与之区别的是正向代理，正向代理需要在客户端配置代理服务器进行指定网站访问

## 2、负载均衡

增加服务器的数量，然后将请求分发到各个服务器上，将原先请求集中到单个服务器上的 情况改为将请求分发到多个服务器上，将负载分发到不同的服务器，也就是我们所说的负 载均衡

### **nginx 分配服务器策略** 

**第一种 轮询（默认）**

每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器 down 掉，能自动剔除。

**第二种 weight**

weight 代表权重默认为 1,权重越高被分配的客户端越多

 ![img](https://img2018.cnblogs.com/blog/1455597/201910/1455597-20191029103434709-133983538.png)

**第三种 ip_hash**

每个请求按访问 ip 的 hash 结果分配，这样每个访客固定访问一个后端服务器

 ![img](https://img2018.cnblogs.com/blog/1455597/201910/1455597-20191029103440910-1253898902.png)

**第四种 fair（第三方）**

按后端服务器的响应时间来分配请求，响应时间短的优先分配

## 3、动静分离

Nginx 动静分离简单来说就是把动态跟静态请求分开，不能理解成只是单纯的把动态页面和静态页面物理分离。严格意义上说应该是动态请求跟静态请求分开，可以理解成使用 Nginx处理静态页面，Tomcat 处理动态页面。动静分离目前主流推崇的方案为：把静态文件独立成单独的域名，放在独立的服务器上

通过 location 指定不同的后缀名实现不同的请求转发。通过 expires 参数设置，可以设置不同资源的过期时间

# Nginx 高可用

两台并列的nginx,都可以处理内部服务器,如果有一台nginx挂了,另一个迅速顶上。而对外统一使用同一个虚拟的ip进行访问

<img src="assets/Nginx高可用.jpg">

# Nginx 的原理 

- mater 和 worker

nginx拥有有一个 master 进程和多个 worker 进程。master 进程主要用来管理 worker 进程，包括：接受信号，将信号分发给 worker 进程，监听 worker 进程工作状态，当 worker 进程退出时 (非正常)，启动新的 worker 进程。基本的网络事件会交给 worker 进程处理。多个 worker 进程之间是对等的，他们同等竞争来自客户端的请求，各进程互相之间是独立的 。

- 一个 master 和多个 woker 有好处 

（1）可以使用 nginx –s reload 热部署，利用 nginx 进行热部署操作 ，将不在工作的worker进行更新，有新的请求连接时分配给这些更新了的worker，正在工作的worker完成本次工作后在进行更新

（2）每个 woker 是独立的进程，如果有其中的一个 woker 出现问题，其他 woker 独立的， 继续进行争抢，实现请求过程，不会造成服务中断