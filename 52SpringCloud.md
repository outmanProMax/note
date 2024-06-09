# 架构目标

**衡量网站的性能指标：**

- **响应时间**：指执行一个请求从开始到最后收到响应数据所花费的总体时间。
- **并发数**：指系统同时能处理的请求数量。
- **并发连接数**：指的是客户端向服务器发起请求，并建立了TCP连接。每秒钟服务器连接的总TCP数量
- **请求数**：也称为QPS(Query Per Second) 指每秒多少请求.
- **并发用户数**：单位时间内有多少用户
- **吞吐量**：指单位时间内系统能处理的请求数量。
- **QPS**：Query Per Second 每秒查询数。
- **TPS**：Transactions Per Second 每秒事务数。
- 一个事务是指一个客户机向服务器发送请求然后服务器做出反应的过程。客户机在发送请求时开始计时，收到服务器响应后结束计时，以此来计算使用的时间和完成的事务个数。
- 一个页面的一次访问，只会形成一个TPS；但一次页面请求，可能产生多次对服务器的请求，就会有多个QPS

QPS >= 并发连接数 >= TPS

**目标**:

- **高性能**：提供快速的访问体验。
- **高可用**：网站服务一直可以正常访问。
- **可伸缩**：通过硬件增加/减少，提高/降低处理能力。
- **高可扩展**：系统间耦合低，方便的通过新增/移除方式，增加/减少新的功能/模块。
- **安全性**：提供网站安全访问和数据加密，安全存储等策略。
- **敏捷性**：随需应变，快速响应。

# 架构演进

![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/20220808/image.20tbpa7hx21s.webp)

## 单体架构

![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/20220808/image.4x8kymw89lw0.webp)

优点：

- 简单：开发部署都很方便，小型项目首选

缺点：

- 项目启动慢
- 可靠性差
- 可伸缩性差
- 扩展性和可维护性差
- 性能低

## 垂直架构

![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/20220808/image.i16tmg4jmk0.webp)

- 垂直架构是指将单体架构中的多个模块拆分为多个独立的项目。形成多个独立的单体架构。

单体架构存在的问题：

- 项目启动慢
- 可靠性差
- 可伸缩性差
- 扩展性和可维护性差
- 性能低

垂直架构存在的问题：

- 重复功能太多

## 分布式架构

![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/20220808/image.50o93z8msc00.webp)

- 分布式架构是指在垂直架构的基础上，将公共业务模块抽取出来，作为独立的服务，供其他调用者消费，以实现服务的共享和重用。
- RPC： Remote Procedure Call 远程过程调用。有非常多的协议和技术来都实现了RPC的过程。比如：HTTP REST风格，Java RMI规范、WebService SOAP协议、Hession等等。

垂直架构存在的问题：

- 重复功能太多

分布式架构存在的问题：

- 服务提供方一旦产生变更，所有消费方都需要变更。

## SOA架构

![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/20220808/image.7c0jsgrqdow0.webp)

- SOA：（Service-Oriented Architecture，面向服务的架构）是一个组件模型，它将应用程序的不同功能单元（称为服务）进行拆分，并通过这些服务之间定义良好的接口和契约联系起来。
- ESB：(Enterparise Servce Bus) 企业服务总线，服务中介。主要是提供了一个服务于服务之间的交互。ESB 包含的功能如：负载均衡，流量控制，加密处理，服务的监控，异常处理，监控告急等等。
- 分布式架构存在的问题：
  - 服务提供方一旦产生变更，所有消费方都需要变更

## 微服务架构

![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/20220808/image.160pvm98fadc.webp)

- 微服务架构是在 SOA 上做的升华，微服务架构强调的一个重点是“业务需要彻底的组件化和服务化”，原有的单个业务系统会拆分为多个可以独立开发、设计、运行的小应用。这些小应用之间通过服务完成交互和集成。
- 微服务架构 = 80%的SOA服务架构思想 + 100%的组件化架构思想 + 80%的领域建模思想
- 特点：
- 服务实现组件化：开发者可以自由选择开发技术。也不需要协调其他团队
- 服务之间交互一般使用REST API
- 去中心化：每个微服务有自己私有的数据库持久化业务数据
- 自动化部署：把应用拆分成为一个一个独立的单个服务，方便自动化部署、测试、运维

*Dubbo 架构*

![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/20220808/image.oo29wlcrhuo.webp)

**节点角色说明：**

- **Provider**：暴露服务的服务提供方
- **Container**：服务运行容器
- **Consumer**：调用远程服务的服务消费方
- **Registry**：服务注册与发现的注册中心
- **Monitor**：统计服务的调用次数和调用时间的监控中心

# 微服务

- 微服务是一种架构风格
- 一个应用拆分为一组小型服务
- 每个服务运行在自己的进程内，也就是可独立部署和升级
- 服务之间使用轻量级HTTP交互
- 服务围绕业务功能拆分
- 可以由全自动部署机制独立部署
- 去中心化，服务自治。服务可以使用不同的语言、不同的存储技术

![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/20220811/image.5g6chrwarzs0.webp)

## 服务模块构建顺序

1. 建Module
2. 改POM
3. 写YML
4. 主启动
5. 业务类

# Eureka

Eureka采用了CS的设计架构，Eureka Sever作为服务注册功能的服务器，它是服务注册中心。而系统中的其他微服务（(消费者和服务提供者)），使用Eureka的客户端连接到 Eureka Server并维持心跳连接。这样系统的维护人员就可以通过Eureka Server来监控系统中各个微服务是否正常运行。



![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/20220813/image.5e5dv8ys3mc0.webp)

## EurekaServer服务端

- pom

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

- yml

```yml
eureka:
  instance:
    hostname: eureka7002.com #eureka服务端的实例名称
  client:
    register-with-eureka: false     #false表示不向注册中心注册自己。
    fetch-registry: false     #false表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    service-url:
    #集群版指向其它eureka
      defaultZone: http://eureka7001.com:7001/eureka/
    #单机版就是自己
      #defaultZone: http://eureka7002.com:7002/eureka/
```

- 主启动

```java
@EnableEurekaServer
```

## EurekaServer客户端

- pom

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

- yml

```yml
eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为true。
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetch-Registry: true
    service-url:
      #单机版
      defaultZone: http://localhost:7001/eureka
      # 集群版
      #defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka
  instance:
  	  #服务名称修改（也就是将IP地址，换成可读性高的名字）
      instance-id: payment8001
      #访问路径可以显示IP地址（鼠标指针移至服务名称下显示）
      prefer-ip-address: true
```

- 主启动

```java
@EnableEurekaClient
```

## 服务发现Discovery

zookeeper也可用

对于注册进eureka里面的微服务，可以通过服务发现来获得该服务的信息

- 主启动类

```java
@EnableEurekaClient
@EnableDiscoveryClient
```

- 业务类

```java
 ...
        
    @Resource
    private DiscoveryClient discoveryClient;

    ...
        
    @GetMapping(value = "/payment/discovery")
    public Object discovery(){
        List<String> services = discoveryClient.getServices();
        for (String element : services) {
            log.info("element:"+element);
        }

        List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
        for (ServiceInstance instance : instances) {
            log.info(instance.getServiceId()+"\t"+instance.getHost()+"\t"+instance.getPort()+"\t"+instance.getUri());
        }
        return this.discoveryClient;
    }
```

##  Eureka自我保护

如果Eureka在server端在一定时间内(默认90秒)没有收到EurekaClient发送心跳包，便会直接从服务注册列表中剔除该服务，但是在短时间( 90秒中)内丢失了大量的服务实例心跳，这时候Eurekaserver会开启自我保护机制，不会剔除该服务

- 服务器yml(禁止自我保护)

```yml
eureka:
  ...
  server:
    #关闭自我保护机制，保证不可用服务被及时踢除
    enable-self-preservation: false
    eviction-interval-timer-in-ms: 2000

```

- 客户端yml(设置心跳检测与续约时间)

```yml
eureka:
  ...
  instance:
    #开发时没置小些，保证服务关闭后注册中心能即使剔除服务
    #Eureka客户端向服务端发送心跳的时间间隔，单位为秒(默认是30秒)
    lease-renewal-interval-in-seconds: 1
    #Eureka服务端在收到最后一次心跳后等待时间上限，单位为秒(默认是90秒)，超时将剔除服务
    lease-expiration-duration-in-seconds: 2
```

## 服务起别名

```
#服务别名----使用该别名代替原服务地址localhost:8004
spring:
  application:
    name: cloud-provider-payment
```

# Zookeeper

zookeeper是一个分布式协调工具，可以实现注册中心功能

- pom

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
    <!--先排除自带的zookeeper3.5.3 防止与3.5.6起冲突-->
    <exclusions>
        <exclusion>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<!--添加zookeeper3.5.6版本-->
<dependency>
    <groupId>org.apache.zookeeper</groupId>
    <artifactId>zookeeper</artifactId>
    <version>3.5.6</version>
</dependency>
```

- yml

```yaml
spring:
  application:
    name: cloud-provider-payment
  cloud:
    zookeeper:
      connect-string: 192.168.130.200:2181 # zookeeper服务IP加端口号
```

- 主启动类

```
@EnableDiscoveryClient
```

**ZooKeeper的服务节点是临时节点，没有Eureka那含情脉脉。**

# Consul

Consul提供了微服务系统中的服务治理、配置中心、控制总线等功能。

它具有很多优点。包括：基于raft协议，比较简洁；支持健康检查，同时支持HTTP和DNS协议支持跨数据中心的WAN集群提供图形界面跨平台，支持Linux、Mac、Windows。

- pom

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-consul-discovery</artifactId>
</dependency>
```

- yml

```yaml
spring:
  application:
    name: consul-provider-payment
  ####consul注册中心地址
  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        #hostname: 127.0.0.1
        service-name: ${spring.application.name}
```

- 主启动类

```
@EnableDiscoveryClient
```

## 三个注册中心异同点

| 组件名    | 语言CAP | 服务健康检查 | 对外暴露接口 | Spring Cloud集成 |
| --------- | ------- | ------------ | ------------ | ---------------- |
| Eureka    | Java    | AP           | 可配支持     | HTTP             |
| Consul    | Go      | CP           | 支持         | HTTP/DNS         |
| Zookeeper | Java    | CP           | 支持客户端   | 已集成           |



CAP：

- C：Consistency (强一致性),C就是所有节点在同一时间看到的数据是一致的
- A：Availability (可用性)，A就是所有的请求都会收到响应
- P：Partition tolerance （分区容错性)

Note:一般来说分区容错是不可避免了，即CAP一般都有要求P，剩下只能A和P二选一

![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/20220815/image.94hpz9x1yb0.webp)

**最多只能同时较好的满足两个**。

CAP理论的核心是：**一个分布式系统不可能同时很好的满足一致性，可用性和分区容错性这三个需求**。

因此，根据CAP原理将NoSQL数据库分成了满足CA原则、满足CP原则和满足AP原则三大类:

- CA - 单点集群，满足—致性，可用性的系统，通常在可扩展性上不太强大。
- CP - 满足一致性，分区容忍必的系统，通常性能不是特别高。
- AP - 满足可用性，分区容忍性的系统，通常可能对一致性要求低一些。当网络分区出现后，为了保证可用性，系统可以返回旧值，保证系统的可用性。

# Ribbon负载均衡

## Rinbbon和Nginx的区别

服务器负载均衡需要多加一台负载均衡服务器来进行，流程是客户端->负载均衡服务器->应用服务器

客户端负载均衡通过自己完成（先从服务注册中心获取服务列表），流程是客户端->应用服务器

- pom

```xml
<dependency>
    <groupld>org.springframework.cloud</groupld>
    <artifactld>spring-cloud-starter-netflix-ribbon</artifactid>
</dependency>
```

## Ribbon负载规则

- RoundRobinRule 轮询
- RandomRule 随机
- RetryRule 先按照RoundRobinRule的策略获取服务，如果获取服务失败则在指定时间内会进行重试
- WeightedResponseTimeRule 对RoundRobinRule的扩展，响应速度越快的实例选择权重越大，越容易被选择
- BestAvailableRule 会先过滤掉由于多次访问故障而处于断路器跳闸状态的服务，然后选择一个并发量最小的服务
- AvailabilityFilteringRule 先过滤掉故障实例，再选择并发较小的实例
- ZoneAvoidanceRule 默认规则,复合判断server所在区域的性能和server的可用性选择服务器

### 负载规则替换

- 新建package - com.frx01.myrule，在com.frx01.myrule下新建MySelfRule规则类

```java
@Configuration
public class MySelfRule {

    @Bean
    public IRule myRule(){
        return new RandomRule();
    }
}
```

- RestTemple配置类加上@LoadBalanced

```
@Configuration
public class ApplicationContextConfig
{
    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate()
    {
        return new RestTemplate();
    }
}
```

- 主启动类

```java
@RibbonClient(name = "CLOUD-PAYMENT-SERVICE",configuration = MySelfRule.class)
```

# OpenFeign 简化服务调用

前面在使用Ribbon+RestTemplate时，利用RestTemplate对http请求的封装处理，形成了一套模版化的调用方法。但是在实际开发中，由于对服务依赖的调用可能不止一处，往往一个接口会被多处调用，所以通常都会针对每个微服务自行封装一些客户端类来包装这些依赖服务的调用。所以，Feign在此基础上做了进一步封装，由他来帮助我们定义和实现依赖服务接口的定义。在Feign的实现下，我们只需创建一个接口并使用注解的方式来配置它(以前是Dao接口上面标注Mapper注解,现在是**一个微服务接口上面标注一个Feign注解即可**)，即可完成对服务提供方的接口绑定，简化了使用Spring cloud Ribbon时，**自动封装服务调用客户端的开发量**。

- pom

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

- 主启动类

```java
@EnableFeignClients
```

- 业务逻辑层接口+@FeignClient配置调用provider服务

```java
@Component
@FeignClient(value = "CLOUD-PAYMENT-SERVICE")
public interface PaymentFeignService {

    @GetMapping(value = "/payment/get/{id}")//对应的provider服务的调用地址
    public CommonResult<Payment> getPaymentById(@PathVariable("id") Long id);
}
```

- 控制层Controller掉用PaymentFeignService

```java
@RestController
@Slf4j
public class OrderFeignController {

    @Resource
    private PaymentFeignService paymentFeignService;

    @GetMapping(value = "/consumer/payment/get/{id}")
    public CommonResult<Payment> getPaymentById(@PathVariable("id") Long id){
        return paymentFeignService.getPaymentById(id);
    }
}
```

## OpenFeign超时控制

```xml
#设置feign客户端超时时间(OpenFeign默认支持ribbon)(单位：毫秒)
ribbon:
  #指的是建立连接所用的时间，适用于网络状况正常的情况下,两端连接所用的时间
  ReadTimeout: 5000
  #指的是建立连接后从服务器读取到可用资源所用的时间
  ConnectTimeout: 5000

```

## OpenFeign日志增强

Feign提供了日志打印功能，我们可以通过配置来调整日恙级别，从而了解Feign 中 Http请求的细节。

说白了就是对Feign接口的调用情况进行监控和输出

**日志级别**

1. NONE：默认的，不显示任何日志;

2. BASIC：仅记录请求方法、URL、响应状态码及执行时间;

3. HEADERS：除了BASIC中定义的信息之外，还有请求和响应的头信息;

4. FULL：除了HEADERS中定义的信息之外，还有请求和响应的正文及元数据。

- 日志配置类

```
import feign.Logger;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class FeignConfig {

    @Bean
    Logger.Level feignLoggerLevel(){
        return Logger.Level.FULL;
    }
}
```

- YML文件里需要开启日志的Feign客户端

```
logging:
  level:
    # feign日志以什么级别监控哪个接口
    com.frx01.springcloud.service.PaymentFeignService: debug
```

# Hystrix 服务降级|熔断

Hystrix是一个用于处理分布式系统的**延迟**和**容错**的开源库

## Hystrix服务降级

**什么是服务降级**

服务器忙或出现故障时，向调用方返回一个符合预期的、可处理的备选响应（FallBack)

**哪些情况会出发降级**

- 程序运行导常
- 超时
- 服务熔断触发服务降级
- 线程池/信号量打满也会导致服务降级

### 在服务提供端做服务降级（二者则一即可）

- pom

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
```

- 主启动类

```java
@EnableCircuitBreaker
```

- 业务类

```java
@HystrixCommand(fallbackMethod = "paymentInfo_TimeOutHandler",	//fallback方法名
                commandProperties = {@HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds",value="3000")	//超时降级的时间及单位
})
public String paymentInfo_TimeOut(Integer id){
	……
}

public String paymentInfo_TimeOutHandler(Integer id){
    return "线程池: "+Thread.currentThread().getName()+"8001系统繁忙或者运行报错，请稍后再试,,id: "+id+"\t"+"呜呜";
}
```

### 在服务消费端做服务降级（二者则一即可）

- pom

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
```

- yml

```yaml
feign:
  hystrix:
    enabled: true
```

- 主启动类

```java
@EnableHystrix
```

- 业务类（控制层或业务层皆可使用）

```java
@HystrixCommand(fallbackMethod = "paymentInfo_TimeOutHandler",	//fallback方法名
                commandProperties = {@HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds",value="3000")	//超时降级的时间及单位
})
public String paymentInfo_TimeOut(Integer id){
	……
}

public String paymentInfo_TimeOutHandler(Integer id){
    return "线程池: "+Thread.currentThread().getName()+"8001系统繁忙或者运行报错，请稍后再试,,id: "+id+"\t"+"呜呜";
}
```

###  全局通配服务降级

- 针对业务类建立全局通配服务类

```java
@SuppressWarnings("all")
@Component
public class PaymentFallbackService implements PaymentHystrixService{	//实现对应业务类
    @Override
    public String paymentInfo_OK(Integer id) {
        return "-----PaymentFallbackService fall back-paymentInfo_OK ,o(╥﹏╥)o";
    }

    @Override
    public String paymentInfo_TimeOut(Integer id) {
        return "-----PaymentFallbackService fall back-paymentInfo_TimeOut ,o(╥﹏╥)o";
    }
}
```

- 业务类

```java
@Component
@FeignClient(value = "CLOUD-PROVIDER-HYSTRIX-PAYMENT",
        fallback = PaymentFallbackService.class)//指定PaymentFallbackService类
public interface PaymentHystrixService {

    @GetMapping("/payment/hystrix/ok/{id}")
    public String paymentInfo_OK(@PathVariable("id") Integer id);

    @GetMapping("/payment/hystrix/timeout/{id}")
    public String paymentInfo_TimeOut(@PathVariable("id") Integer id);
}
```

## Hystrix服务熔断

Hystrix会监控微服务间调用的状况，当失败的调用到一定阈值，缺省是5秒内20次调用失败，就会启动熔断机制，直接拒绝访问，之后隔一段时间后会再次探测服务是否恢复，在判断是否关闭熔断

**熔断类型**

- 熔断打开：请求不再进行调用当前服务，内部设置时钟一般为MTTR(平均故障处理时间)，当打开时长达到所设时钟则进入半熔断状态。
- 熔断关闭：熔断关闭不会对服务进行熔断。
- 熔断半开：一段时间之后（默认是5秒)，这个时候断路器是半开状态，会让其中一个请求进行转发。如果成功，断路器会关闭，若失败，继续开启。

**具体使用**

```java
//=====服务熔断
@HystrixCommand(fallbackMethod = "paymentCircuitBreaker_fallback",commandProperties = {
    @HystrixProperty(name = "circuitBreaker.enabled",value = "true"),// 是否开启断路器
    @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold",value = "10"),// 请求次数
    @HystrixProperty(name = "circuitBreaker.sleepWindowInMilliseconds",value = "10000"), // 时间窗口期
    @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage",value = "60"),// 失败率达到多少后跳闸
})
public String paymentCircuitBreaker(@PathVariable("id") Integer id) {
    ...
}
```

## Hystrix图形化Dashboard搭建

除了隔离依赖服务的调用以外，Hystrix还提供了**准实时的调用监控**(Hystrix Dashboard)，Hystrix会持续地记录所有通过Hystrix发起的请求的执行信息，并以统计报表和图形的形式展示给用户，包括每秒执行多少请求多少成功，多少失败等。

- pom

```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
        </dependency>
```

- 主启动类

```java
@EnableHystrixDashboard
```

- 被监控的微服务提供类pom

```xml
<dependency
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```



- 整图说明

  ![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/20220819/image.703qlrecqrc0.webp)

# Gateway服务网关

Note：网关进行地址匹配是有上至下进行匹配

Gateway旨在提供一种简单而有效的方式来对API进行路由，以及提供一些强大的过滤器功能，例如:熔断、限流、重试等。

- pom

```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-gateway</artifactId>
        </dependency>
```

- 方式一：yml

```yaml
spring:
  application:
    name: cloud-gateway
  #############################新增网关配置###########################
  cloud:
    gateway:
      routes:
        - id: payment_routh #payment_route     #路由的ID，没有固定规则但要求唯一，建议配合服务名
          uri: http://localhost:8001           #匹配后提供服务的路由地址
          #uri: lb://cloud-payment-service     #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**             # 断言，路径相匹配的进行路由

        - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          uri: http://localhost:8001           #匹配后提供服务的路由地址
          #uri: lb://cloud-payment-service     #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/lb/**              # 断言，路径相匹配的进行路由
####################################################################
```

- 方式二：配置类

```java
@Configuration
public class GateWayConfig {

    @Bean
    public RouteLocator customRouteLocator(RouteLocatorBuilder routeLocatorBuilder){
        RouteLocatorBuilder.Builder routes = routeLocatorBuilder.routes();

        routes.route("path_route_frx01",
                r -> r.path("/guonei")		//http://news.baidu.com/guonei -> localhost:8001/guonei
                    .uri("http://news.baidu.com/guonei")).build();
        return routes.build();
    }
}
```

## 动态路由

```yaml
uri: http://localhost:8001更改为uri: lb://cloud-payment-service 
```

## 常用的Predicate

```yaml
predicates:
        # 这个时间后才能起效
        - After=2017-01-20T17:42:47.789-07:00[America/Denver]
        # 两个时间点之间
        - Between=2017-01-20T17:42:47.789-07:00[America/Denver], 2017-01-21T17:42:47.789-07:00[America/Denver]
        # cookie名为chocolate，且cookie对应值满足对应的正则表达式
        - Cookie=chocolate, ch.p
        # 请求头包括以下内容
         - Header=X-Request-Id, \d+
```

## 常用的Filter

- 生命周期：
  - pre
  - post
- 种类（具体看官方文档）：
  - GatewayFilter - 有31种
  - GlobalFilter - 有10种 常用的GatewayFilter：AddRequestParameter GatewayFilter

例如：GateWay9527项目添加MyLogGateWayFilter类：

```java
@Component
@Slf4j
public class MyLogGateWayFilter implements GlobalFilter, Ordered {

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        log.info("***********come in MyLogGateWayFilter:  "+new Date());
        String name = exchange.getRequest().getQueryParams().getFirst("uname");
        if(name==null){
            log.info("*******用户名为null，非法用户，o(╥﹏╥)o");
            exchange.getResponse().setRawStatusCode(HttpStatus.HTTP_NOT_ACCEPTABLE);
            return exchange.getResponse().setComplete();
        }
        return chain.filter(exchange);
    }
    
    @Override
    public int getOrder() {
        return 0;
    }
}
```

# Config分布式配置中心

Config可进行集中式的、动态的配置管理服务。SpringCloud Config分为**服务端**和**客户端**两部分。

**集中式**：集中管理配置文件

**动态**：当配置发生变动时，服务不需要重启即可感知到配置的变化并应用新的配置；将配置信息以REST接口的形式暴露 - post/crul访问刷新即可

**与GitHub整合配置**：由于SpringCloud Config默认使用Git来存储配置文件(也有其它方式,比如支持SVN和本地文件)，但最推荐的还是Git，而且使用的是http/https访问的形式。

## 配置总控中心

- pom

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>
```

- yml

```yaml
spring:
  application:
    name:  cloud-config-center #注册进Eureka服务器的微服务名
  cloud:
    config:
      server:
        git:
          uri: git@github.com:xustudyxu/springcloud-config.git #GitHub上面的git仓库名字
        ####搜索目录
          search-paths:
            - springcloud-config
      ####读取分支
      label: master
```

- 主启动类

```java
@EnableConfigServer
```

**对应配置进行读取规则**

- ```
  /{label(分支)}/{application}-{profile}.yml
  ```

  （推荐）

  - http://config-3344.com:3344/master/config-dev.yml
  - http://config-3344.com:3344/master/config-test.yml
  - http://config-3344.com:3344/master/config-prod.yml

- ```
  /{application}-{profile}.yml
  ```

  - http://config-3344.com:3344/config-dev.yml
- http://config-3344.com:3344/config-test.yml
  
  - http://config-3344.com:3344/config-prod.yml
  
- ```
  /{application}/{profile}[/{label}]
  ```

  - http://config-3344.com:3344/config/dev/master
  - http://config-3344.com:3344/config/test/master
  - http://config-3344.com:3344/config/test/dev

## 配置客户端

- pom

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

- bootstrap.yml

```yml
server:
  port: 3355

spring:
  application:
    name: config-client
  cloud:
    #Config客户端配置
    config:
      label: master #分支名称
      name: config #配置文件名称
      profile: dev #读取后缀名称   上述3个综合：master分支上config-dev.yml的配置文件被读取http://config-3344.com:3344/master/config-dev.yml
      uri: http://localhost:3344/ #配置中心地址k

# 暴露监控端点,以保证可以动态刷新配置
management:
  endpoints:
    web:
      exposure:
        include: "*"

#服务注册到eureka地址
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka
```

- 主启动类

```java
@RefreshScope
```

访问实现刷新配置文件

```
curl -X POST "http://localhost:3355/actuator/refresh"
```

### bootstrap.yml和application.yml

bootstrap.yml文件也是Spring Boot的默认配置文件，而且其加载的时间相比于application.yml更早。

application.yml和bootstrap.yml虽然都是Spring Boot的默认配置文件，但是定位却不相同。

bootstrap.yml可以理解成系统级别的一些参数配置，这些参数一般是不会变动的。

application.yml可以用来定义应用级别的参数，如果搭配 spring cloud config 使用，application.yml里面定义的文件可以实现动态替换。

总结就是：
bootstrap.yml文件相当于项目启动时的引导文件，内容相对**固定**。application.yml文件是微服务的一些常规配置参数，变化比较频繁。
bootstrap.yml **先于** application.yml。

# Bus消息总线

管理和传播分布式系统间的消息，就像一个分布式执行器，可用于广播状态更改、事件推送等，也可以当作微服务间的通信通道。

![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/image.5rziiyg4lyk0.webp)

## 配置总控中心

- pom

```
        <!--添加消息总线RabbitMQ支持-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-bus-amqp</artifactId>
        </dependency>
```

- yml

```yaml
  rabbitmq:
    host: localhost
    port: 5672
    username: guest
    password: guest
 
 ##rabbitmq相关配置,暴露bus刷新配置的端点<--------------------------
management:
  endpoints: #暴露bus刷新配置的端点
    web:
      exposure:
        include: 'bus-refresh'
```

## 配置客户端

- pom

```
        <!--添加消息总线RabbitMQ支持-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-bus-amqp</artifactId>
        </dependency>
```

- bootstrap.yml

```yaml
  rabbitmq:
    host: localhost
    port: 5672
    username: guest
    password: guest
 
```

**广播通知配置**：

`curl -X POST "http://localhost:3344/actuator/bus-refresh`

**定点通知配置**：

`http://localhost:3344/actuator/bus-refresh/{destination}`	

# Stream消息驱动

通过定义绑定器Binder作为中间层，实现了应用程序与消息中间件细节之间的隔离，目前仅支持RabbitMQ、 Kafka。

![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/20220822/image.19ouvdp6wnog.webp)

- Binder - 很方便的连接中间件，屏蔽差异。
- Channel - 通道，是队列Queue的一种抽象，在消息通讯系统中就是实现存储和转发的媒介，通过Channel对队列进行配置。
- Source和Sink - 简单的可理解为参照对象是Spring Cloud Stream自身，从Stream发布消息就是输出，接受消息就是输入。

## 消息驱动之生产者

- yml

```yaml
spring:
  application:
    name: cloud-stream-provider
  cloud:
      stream:
        binders: # 在此处配置要绑定的rabbitmq的服务信息；
          defaultRabbit: # 表示定义的名称，用于于binding整合
            type: rabbit # 消息组件类型
            environment: # 设置rabbitmq的相关的环境配置
              spring:
                rabbitmq:
                  host: localhost
                  port: 5672
                  username: guest
                  password: guest
        bindings: # 服务的整合处理
          output: # 这个名字是一个通道的名称
            destination: studyExchange # 表示要使用的Exchange名称定义
            content-type: application/json # 设置消息类型，本次为json，文本则设置“text/plain”
            binder: defaultRabbit # 设置要绑定的消息服务的具体设置
            group: A_Group #<----------------------------------------解决消息重复消费的关键
```

- 业务类

发送消息接口

```java
public interface IMessageProvider {
    public String send();
}
```

发送消息接口实现类

```java
@EnableBinding(Source.class)//定义消息发送管道
public class MessageProviderImpl implements IMessageProvider {

    @Resource
    private MessageChannel output;//消息发送管道

    @Override
    public String send() {
        String serial = UUID.randomUUID().toString();
        output.send(MessageBuilder.withPayload(serial).build());
        System.out.println("-------------serial:"+serial);
        return null;
    }
}
```

 ## 消息驱动之消费者

- yml

```yaml
spring:
  application:
    name: cloud-stream-consumer
  cloud:
      stream:
        binders: # 在此处配置要绑定的rabbitmq的服务信息；
          defaultRabbit: # 表示定义的名称，用于于binding整合
            type: rabbit # 消息组件类型
            environment: # 设置rabbitmq的相关的环境配置
              spring:
                rabbitmq:
                  host: localhost
                  port: 5672
                  username: guest
                  password: guest
        bindings: # 服务的整合处理
          input: # 这个名字是一个通道的名称
            destination: studyExchange # 表示要使用的Exchange名称定义
            content-type: application/json # 设置消息类型，本次为对象json，如果是文本则设置“text/plain”
            binder: defaultRabbit # 设置要绑定的消息服务的具体设置
```

- 业务类

```
@Component
@EnableBinding(Sink.class)
public class ReceiveMessageListenerController {

    @Value("${server.port}")
    private String serverPort;

    @StreamListener(Sink.INPUT)
    public void input(Message<String> message){
        System.out.println("消费者1号------------------>接收到的消息:"+message.getPayload()+"\t port:"+serverPort);
    }
}
```

## 消息重复消费

微服务消费者应用放置于同一个group中，就能够保证消息只会被其中一个应用消费一次。

**不同的组**是可以重复消费的，**同一个组**内会发生**竞争关系**，只有其中一个可以消费。

## 消息持久化

**无分组属性配置**：不会进行消息持久化

**有分组属性配置**：会进行消息的持久化

# Sleuth+Zipkin 分布式请求链路追踪

对服务的调用过程进行链路跟踪，以图形或网页的形式展现，且兼容支持了zipkin

- POM

```xml
<!--包含了sleuth+zipkin-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

- YML

```yaml
spring:
  zipkin: #<-------------------------------------关键 
      base-url: http://192.168.200.120:9411
      sender:
      	type: web
      # 取消nacos对zipkin的服务发现
      discovery-client-enabled: false
  sleuth: #<-------------------------------------关键
    sampler:
    #采样率值介于 0 到 1 之间，1 则表示全部采集
    probability: 1
```

# Nacos服务发现、配置管理和服务管理平台

Nacos就是注册中心＋配置中心的组合 -> Nacos = **Eureka+Config+Bus**

**Note：必须在D:\production\apache\nacos-server-1.3.1\nacos\bin下运行**

**./startup.cmd -m standalone**

## 服务提供者

- POM

父POM

```xml
<dependencyManagement>
    <dependencies>
        <!--spring cloud alibaba 2.1.0.RELEASE-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-alibaba-dependencies</artifactId>
            <version>2.1.0.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

本模块POM

```xml
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
```

-  YML

```yaml
server:
  port: 9001

spring:
  application:
    name: nacos-payment-provider
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #配置Nacos地址

management:
  endpoints:
    web:
      exposure:
        include: '*'
```

- 主启动类

```java
@SpringBootApplication
@EnableDiscoveryClient
public class PaymentMain9001 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentMain9001.class,args);
    }
}
```

- 业务类


```java
  @RestController
  public class PaymentController {
  
      @Value("${server.port}")
      private String serverPort;
  
      @GetMapping("/payment/nacos/{id}")
      public String getPayment(@PathVariable("id") Integer id){
          return "Nacos registry, serverPort: "+serverPort+"\t id:"+id;
      }
  }
```

## 服务消费者

- POM

```xml
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
```

- YML

```yaml
server:
  port: 83

spring:
  application:
    name: nacos-order-consumer
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848

#消费者将要去访问的微服务名称(注册成功进nacos的微服务提供者)
service-url:
  nacos-user-service: http://nacos-payment-provider
```

- 主启动

```java
@SpringBootApplication
@EnableDiscoveryClient
public class OrderNacosMain83 {
    public static void main(String[] args) {
        SpringApplication.run(OrderNacosMain83.class,args);
    }
}
```

- 业务类

ApplicationContextConfig

```java
@Configuration
public class ApplicationConfig {
    
    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }
}
```

OrderNacosController

```java
@RestController
public class OrderNacosController {

    @Resource
    private RestTemplate restTemplate;

    @Value("${service-url.nacos-user-service}")
    private String serverURL;

    @GetMapping("/consumer/payment/nacos/{id}")
    public String paymentInfo(@PathVariable("id") Integer id){
        return restTemplate.getForObject(serverURL+"/payment/nacos/"+id,String.class);
    }
}
```

## 服务配置中心

- pom

```xml
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
        </dependency>
        <!--nacos-discovery-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
```

- yml

bootstrap.yml

```
# nacos配置
server:
  port: 3377

spring:
  application:
    name: nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #Nacos服务注册中心地址
      config:
        server-addr: localhost:8848 #Nacos作为配置中心地址
        file-extension: yaml #指定yaml格式的配置
        group: DEV_GROUP	#<----指定group
        namespace: 5ca47874-779f-4dfc-a0b6-f70b21108638  #<----指定namespace


# ${spring.application.name}-${spring.profile.active}.${spring.cloud.nacos.config.file-extension}
# nacos-config-client-dev.yaml

# nacos-config-client-test.yaml   ----> config.info

```

application.yml

```
spring:
  profiles:
    active: dev # 表示开发环境
    #active: test # 表示测试环境
    #active: info
```

- 主启动


```java
  @EnableDiscoveryClient
  @SpringBootApplication
  public class NacosConfigClientMain3377
  {
      public static void main(String[] args) {
          SpringApplication.run(NacosConfigClientMain3377.class, args);
      }
  }
```

- 业务类

```java
  @RestController
  @RefreshScope   //支持Nacos的动态刷新功能
  public class ConfigClientController {
  
      @Value("${config.info}")
      private String configInfo;
  
      @GetMapping("/config/info")
      public String getConfigInfo(){
          return configInfo;
      }
  }
```

Nacos配置新增对应的配置文件

![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/20220825/image.4ssyzwcxh5o0.webp)

![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/20220825/image.5im2tpxeos00.webp)

![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/20220825/image.6qdb03c1it40.webp)

配置文件的命名规则如下

```yaml
{prefix}-${spring-profile.active}.${file-extension}
```

- `prefix`默认为`spring.application.name`的值，也可以通过配置项`spring.cloud.nacos.config.prefix`来配置。
- `spring.profile.active`即为当前环境对应的 `profile`，详情可以参考 Spring Boot文档。注意：当`spring.profile.active`为空时，对应的连接符 - 也将不存在，`datald` 的拼接格式变成{file-extension}
- file-exetension为配置内容的数据格式，可以通过配置项`spring .cloud.nacos.config.file-extension`来配置。目前只支持`properties`和`yaml`类型。

 ## NameSpace、Group和Data ID

类似Java里面的package名和类名最外层的namespace是可以用于区分部署环境的，Group和DatalD逻辑上区分两个目标对象。

1. 三者情况

![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/20220825/image.6bvqpd9va3s0.webp)

默认情况：Namespace=public，Group=DEFAULT_GROUP，默认Cluster是DEFAULT

- Nacos默认的Namespace是public，Namespace主要用来实现隔离。
  - 比方说我们现在有三个环境：开发、测试、生产环境，我们就可以创建三个Namespace，不同的Namespace之间是隔离的。
- Group默认是DEFAULT_GROUP，Group可以把不同的微服务划分到同一个分组里面去
- Service就是微服务:一个Service可以包含多个Cluster (集群)，Nacos默认Cluster是DEFAULT，Cluster是对指定微服务的一个虚拟划分。
  - 比方说为了容灾，将Service微服务分别部署在了杭州机房和广州机房，这时就可以给杭州机房的Service微服务起一个集群名称(HZ) ，给广州机房的Service微服务起一个集群名称(GZ)，还可以尽量让同一个机房的微服务互相调用，以提升性能。
- 最后是Instance，就是微服务的实例。

## Nacos持久化切换

Nacos默认自带的是嵌入式数据库derby，[nacos的pom.xml (opens new window)](https://github.com/alibaba/nacos/blob/develop/pom.xml)中可以看出。

derby到mysql切换配置步骤：

1. nacos-server-1.1.4\nacos\conf录下找到nacos-mysql.sql文件，执行脚本。

```mysql
create database nacos_config;
use nacos_config;
source nacos-mysql.sql
```



1. nacos-server-1.1.4\nacos\conf目录下找到application.properties，添加以下配置（按需修改对应值）。

```properties
spring.datasource.platform=mysql

db.num=1
db.url.0=jdbc:mysql://localhost:3306/nacos_config?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true
db.user=root
db.password=abc123
```

## Nacos集群配置

- 追加配置cluster.conf

```
192.168.200.130:3333
192.168.200.130:4444
192.168.200.130:5555
```

- 修改startup.sh

```shell
while getopts ":m:f:s:p:" opt
do
    case $opt in
        m)
            MODE=$OPTARG;;
        f)
            FUNCTION_MODE=$OPTARG;;
        s)
            SERVER=$OPTARG;;
        p)
            PORT=$OPTARG;;
        ?)
        echo "Unknown parameter"
        exit 1;;
    esac
done
...
nohup $JAVA -Dserver.port=${PORT} ${JAVA_OPT} nacos.nacos >> ${BASE_DIR}/logs/start.out 2>&1 &
```

- 修改NGINX

修改内容

```nginx
    upstream cluster{
        server 127.0.0.1:3333;
        server 127.0.0.1:4444;
        server 127.0.0.1:5555;
    }

    server {
        listen 1111;
        server_name localhost;

        location / {
            proxy_pass http://cluster;
        }
    }
```

按照指定启动

```sh
[root@master conf]# nginx -c /usr/local/nginx/conf/nginx.conf
[root@master conf]# nginx -s reload
```

# Sentinel服务熔断与限流

Sentinel 以流量为切入点，从流量控制、熔断降级、系统负载保护等多个维度保护服务的稳定性。

**Note：必须启在D:\production\apache下运行**

**java -jar sentinel-dashboard-1.8.2.jar**

- pom

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
    <version>2.1.8.RELEASE</version>
</dependency>
<!-- sentinel熔断降级限流-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
```

- yml

```yaml
spring:
  cloud:
    sentinel:
      transport:
        dashboard: localhost:8080   #配置Sentinel dashboard地址
        #默认8719端口，假如被占用会自动从8719开始依次+1扫描，直至找到未被占用的端口
        port: 8719

management:
  endpoints:
    web:
      exposure:
        include: '*'

#开启对feign的监控，一般不用
feign:
  sentinel:
    enabled: true
```

- 主启动

```java
@SpringBootApplication
@EnableDiscoveryClient
public class MainApp8401 {
    public static void main(String[] args) {
        SpringApplication.run(MainApp8401.class,args);
    }
}
```

- 业务类

```java
@RestController
@Slf4j
public class FlowLimitController {

    @GetMapping("/testA")
    public String testA(){
        return "----------------testA";
    }

    @GetMapping("/testB")
    public String testB(){
        log.info(Thread.currentThread().getName()+"\t"+".....testB");
        return "----------------testB";
    }
}
```

## 流控规则（请求过多进行流控）

基本介绍

![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/20220827/image.3kvfyj3ozgo0.webp)

进一步解释说明：

- 资源名：唯一名称，为对应请求路径或微服务名。

- 针对来源：Sentinel可以针对调用者进行限流，填写微服务名，默认default（不区分来源）。

- 阈值类型/单机阈值：
  - QPS(每秒钟的请求数量)︰当调用该API的QPS达到阈值的时候，进行限流。
  - 线程数：当调用该API的线程数达到阈值的时候，进行限流。
  
- 是否集群：不需要集群。

- 流控模式：

  - 直接：API达到限流条件时，直接限流。

  - 关联：当关联的资源达到阈值时，就限流自己。
  - 链路：只记录指定链路上的流量（指定资源从入口资源进来的流量，如果达到阈值，就进行限流)【API级别的针对来源】。

- 流控效果：
  - 快速失败：直接失败，抛异常，返回页面 Blocked by Sentinel (flow limiting)
  - Warm up：根据Code Factor（冷加载因子，默认3）的值，从阈值/codeFactor，经过预热时长，才达到设置的QPS阈值。
  - 排队等待：匀速排队，让请求以匀速的速度通过，阈值类型必须设置为QPS，否则无效。

##  降级规则（服务节点异常过多进行熔断）

除了流量控制以外，对调用链路中不稳定的资源进行熔断降级也是保障高可用的重要措施之一

- RT（平均响应时间或，，慢调用比例，秒级）
  - 平均响应时间 超出阈值 且 在时间窗口内通过的请求>=5，两个条件同时满足后触发降级。
  - 窗口期过后关闭断路器。
  - RT最大4900（更大的需要通过-Dcsp.sentinel.statistic.max.rt=XXXX才能生效）。
- 异常比率（秒级）
  - QPS >= 5且异常比例（秒级统计）超过阈值时，触发降级;时间窗口结束后，关闭降级 。异常比率的阈值范围是 `[0.0, 1.0]`
- 异常数(分钟级)
  - 异常数(分钟统计）超过阈值时，触发降级;时间窗口结束后，关闭降级



## 热点规则

热点参数限流会统计传入参数中的热点参数，并根据配置的限流阈值与模式，对包含热点参数的资源调用进行限流。热点参数限流可以看做是一种特殊的流量控制，仅对包含热点参数的资源调用生效。

**配置**

![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/20220828/image.65m5scosrpw0.webp)

针对某一特定的热点资源进行限流，QPS==1

如果该热点资源的第一个参数（String）==5进行特殊限流，QPS==200

## **系统规则**

系统保护规则是从应用级别的入口流量进行控制，从单台机器的 load、CPU 使用率、平均 RT、入口 QPS 和并发线程数等几个维度监控应用指标，让系统尽可能跑在最大吞吐量的同时保证系统整体的稳定性。

系统保护规则是应用整体维度的，而不是资源维度的，并且**仅对入口流量生效**。入口流量指的是进入应用的流量（`EntryType.IN`），比如 Web 服务或 Dubbo 服务端接收的请求，都属于入口流量。

系统规则支持以下的模式:

- Load 自适应（仅对 Linux/Unix-like 机器生效）：系统的 load1 作为启发指标，进行自适应系统保护。当系统 load1 超过设定的启发值，且系统当前的并发线程数超过估算的系统容量时才会触发系统保护（BBR 阶段）。系统容量由系统的 `maxQps * minRt` 估算得出。设定参考值一般是 `CPU cores * 2.5`。
- **CPU usage**（1.5.0+ 版本）：当系统 CPU 使用率超过阈值即触发系统保护（取值范围 0.0-1.0），比较灵敏。
- **平均 RT**：当单台机器上所有入口流量的平均 RT 达到阈值即触发系统保护，单位是毫秒。
- **并发线程数**：当单台机器上所有入口流量的并发线程数达到阈值即触发系统保护。
- **入口 QPS**：当单台机器上所有入口流量的 QPS 达到阈值即触发系统保护。

## blockHandler、fallback和exceptionsToIgnore

- fallback管运行异常（程序错误等）
- blockHandler管配置违规(流量异常等)

- exceptionsToIgnore，忽略指定异常，即这些异常不用兜底方法处理

```java
@RestController
@Slf4j
public class CircleBreakerController {

    public static final String SERVICE_URL = "http://nacos-payment-provider";

    @Resource
    private RestTemplate restTemplate;

    @RequestMapping("/consumer/fallback/{id}")
    //value为起别名
    //fallback只负责业务异常
    //blockHandler只负责sentinel限流、降级、系统保护触发后的返回
    @SentinelResource(value = "fallback",fallback = "handlerFallback",blockHandler = "blockHandler",
    exceptionsToIgnore = {IllegalArgumentException.class}) 
    public CommonResult<Payment> fallback(@PathVariable Long id){
        ……
    }

    //本例是fallback
    public CommonResult handlerFallback(@PathVariable Long id, Throwable e){
        ……
    }

    //本例是blockHandler
    public CommonResult blockHandler(@PathVariable Long id, BlockException blockException){
        ……
    }
}
```

**Note：Sentinel可以集成Ribbon和OpenFeign**

## Sentinel配置持久化

- pom

```xml
<!--SpringCloud ailibaba sentinel-datasource-nacos 后续做持久化用到-->
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-datasource-nacos</artifactId>
</dependency>
```

- yml

```yaml
spring:
  cloud:
    sentinel:
      datasource: #<---------------------------关注点，添加Nacos数据源配置
        ds1:
          nacos:
            server-addr: localhost:8848
            dataId: cloudalibaba-sentinel-service
            groupId: DEFAULT_GROUP
            data-type: json
            rule-type: flow

```

添加Nacos业务规则配置

![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/20220829/image.4awbthpovfw0.webp)



```json
[{
    "resource": "/rateLimit/byUrl",
    "IimitApp": "default",
    "grade": 1,
    "count": 1, 
    "strategy": 0,
    "controlBehavior": 0,
    "clusterMode": false
}]
```

- resource：资源名称；
- limitApp：来源应用；
- grade：阈值类型，0表示线程数, 1表示QPS；
- count：单机阈值；
- strategy：流控模式，0表示直接，1表示关联，2表示链路；
- controlBehavior：流控效果，0表示快速失败，1表示Warm Up，2表示排队等待；
- clusterMode：是否集群。

#  Seata分布式事务

一次业务操作需要跨多个数据源（数据库）或需要跨多个系统进行远程调用，就会产生分布式事务问题。

**java -jar sentinel-dashboard-1.8.2.jar**

分布式事务处理过程的一ID+三组件模型：

- Transaction ID XID 全局唯一的事务ID
- 三组件概念
  - TC (Transaction Coordinator) - 事务协调者：维护全局和分支事务的状态，驱动全局事务提交或回滚。（管理全局事务）
  - TM (Transaction Manager) - 事务管理器：定义全局事务的范围：开始全局事务、提交或回滚全局事务。（请求开启全局事务）
  - RM (Resource Manager) - 资源管理器：管理分支事务处理的资源，与TC交谈以注册分支事务和报告分支事务的状态，并驱动分支事务提交或回滚。（如数据库管理）

处理过程：

1. TM向TC申请开启一个全局事务，全局事务创建成功并生成一个全局唯一的XID；
2. XID在微服务调用链路的上下文中传播；
3. RM向TC注册分支事务，将其纳入XID对应全局事务的管辖；
4. TM向TC发起针对XID的全局提交或回滚决议；
5. TC调度XID下管辖的全部分支事务完成提交或回滚请求。

## 首次使用初始化化设置

对D:\production\apache\seata-server-0.7.1\conf下的文件进行配置

- file.conf

**service模块**

```nginx
service {
    ##fsp_tx_group是自定义的
    vgroup_mapping.my.test.tx_group="fsp_tx_group" 
    default.grouplist = "127.0.0.1:8091"
    enableDegrade = false
    disable = false
    max.commitretry.timeout= "-1"
    max.ollbackretry.timeout= "-1"
}
```

**store模块**

```nginx
## transaction log store
store {
	## store mode: file, db
	## 改成db，将日志存在db里
	mode = "db"
	
	## file store
	file {
		dir = "sessionStore"
		
		# branch session size, if exceeded first try compress lockkey, still exceeded throws exceptions
		max-branch-session-size = 16384
		# globe session size, if exceeded throws exceptions
		max-global-session-size = 512
		# file buffer size, if exceeded allocate new buffer
		file-write-buffer-cache-size = 16384
		# when recover batch read size
		session.reload.read_size= 100
		# async, sync
		flush-disk-mode = async
	}

	# database store
	db {
		## the implement of javax.sql.DataSource, such as DruidDataSource(druid)/BasicDataSource(dbcp) etc.
		datasource = "dbcp"
		## mysql/oracle/h2/oceanbase etc.
		## 配置数据源
		db-type = "mysql"
		driver-class-name = "com.mysql.jdbc.Driver"
		url = "jdbc:mysql://127.0.0.1:3306/seata"
		user = "root"
		password = "你自己密码"
		min-conn= 1
		max-conn = 3
		global.table = "global_table"
		branch.table = "branch_table"
		lock-table = "lock_table"
		query-limit = 100
	}
}
```

- 如果日志是存在db中则需要在seata库里建表

  建表db_store.sql在\seata-server-0.9.0\seata\conf目录里面

- registry.conf将seata加入注册中心

```nginx
registry {
  # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
  # 改用为nacos
  type = "nacos"

  nacos {
  	## 加端口号
    serverAddr = "localhost:8848"
    namespace = ""
    cluster = "default"
  }
  ...
}
```

## 每次开启的设置

- 对应数据库新建undo_log

```sql
-- 注意此处0.3.0+ 增加唯一索引 ux_undo_log
CREATE TABLE `undo_log` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `branch_id` bigint(20) NOT NULL,
  `xid` varchar(100) NOT NULL,
  `context` varchar(128) NOT NULL,
  `rollback_info` longblob NOT NULL,
  `log_status` int(11) NOT NULL,
  `log_created` datetime NOT NULL,
  `log_modified` datetime NOT NULL,
  `ext` varchar(100) DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `ux_undo_log` (`xid`,`branch_id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
```

- 打开seata

必须在D:\production\apache\seata-server-1.3.0\seata\bin下运行seata-server.bat

打开后在服务注册中心中应有一个serverAddr的应用

### 开启全局事务

**以下配置对所有需要开启全局事务均相同**

- yml（方法1:）

```yaml
spring:
  cloud:
    alibaba:
      seata:
        #自定义事务组名称需要与seata-server中的对应
        tx-service-group: gulimall-order-fascar-service-group
```

- file.conf（建立在resource下，方法2：修改vgroup_mapping与当前服务名对应）

Note：方法1和方法2二选一

```nginx
transport {
  # tcp udt unix-domain-socket
  type = "TCP"
  #NIO NATIVE
  server = "NIO"
  #enable heartbeat
  heartbeat = true
  #thread factory for netty
  thread-factory {
    boss-thread-prefix = "NettyBoss"
    worker-thread-prefix = "NettyServerNIOWorker"
    server-executor-thread-prefix = "NettyServerBizHandler"
    share-boss-worker = false
    client-selector-thread-prefix = "NettyClientSelector"
    client-selector-thread-size = 1
    client-worker-thread-prefix = "NettyClientWorkerThread"
    # netty boss thread size,will not be used for UDT
    boss-thread-size = 1
    #auto default pin or 8
    worker-thread-size = 8
  }
  shutdown {
    # when destroy server, wait seconds
    wait = 3
  }
  serialization = "seata"
  compressor = "none"
}

service {

  vgroup_mapping.gulimall-order-fascar-service-group = "default" #修改自定义事务组名称

  default.grouplist = "127.0.0.1:8091"
  enableDegrade = false
  disable = false
  max.commit.retry.timeout = "-1"
  max.rollback.retry.timeout = "-1"
  disableGlobalTransaction = false
}


client {
  async.commit.buffer.limit = 10000
  lock {
    retry.internal = 10
    retry.times = 30
  }
  report.retry.count = 5
  tm.commit.retry.count = 1
  tm.rollback.retry.count = 1
}

## transaction log store
store {
  ## store mode: file、db
  mode = "db"

  ## file store
  file {
    dir = "sessionStore"

    # branch session size , if exceeded first try compress lockkey, still exceeded throws exceptions
    max-branch-session-size = 16384
    # globe session size , if exceeded throws exceptions
    max-global-session-size = 512
    # file buffer size , if exceeded allocate new buffer
    file-write-buffer-cache-size = 16384
    # when recover batch read size
    session.reload.read_size = 100
    # async, sync
    flush-disk-mode = async
  }

  ## database store
  db {
    ## the implement of javax.sql.DataSource, such as DruidDataSource(druid)/BasicDataSource(dbcp) etc.
    datasource = "dbcp"
    ## mysql/oracle/h2/oceanbase etc.
    db-type = "mysql"
    driver-class-name = "com.mysql.jdbc.Driver"
    url = "jdbc:mysql://127.0.0.1:3306/seata"
    user = "root"
    password = "hsp"
    min-conn = 1
    max-conn = 3
    global.table = "global_table"
    branch.table = "branch_table"
    lock-table = "lock_table"
    query-limit = 100
  }
}
lock {
  ## the lock store mode: local、remote
  mode = "remote"

  local {
    ## store locks in user's database
  }

  remote {
    ## store locks in the seata's server
  }
}
recovery {
  #schedule committing retry period in milliseconds
  committing-retry-period = 1000
  #schedule asyn committing retry period in milliseconds
  asyn-committing-retry-period = 1000
  #schedule rollbacking retry period in milliseconds
  rollbacking-retry-period = 1000
  #schedule timeout retry period in milliseconds
  timeout-retry-period = 1000
}

transaction {
  undo.data.validation = true
  undo.log.serialization = "jackson"
  undo.log.save.days = 7
  #schedule delete expired undo_log in milliseconds
  undo.log.delete.period = 86400000
  undo.log.table = "undo_log"
}

## metrics settings
metrics {
  enabled = false
  registry-type = "compact"
  # multi exporters use comma divided
  exporter-list = "prometheus"
  exporter-prometheus-port = 9898
}

support {
  ## spring
  spring {
    # auto proxy the DataSource bean
    datasource.autoproxy = false
  }
}
```

- registry.conf（建立在resource下）

```nginx
registry {
  # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
  type = "nacos"

  nacos {
    serverAddr = "localhost:8848"
    namespace = ""
    cluster = "default"
  }
  eureka {
    serviceUrl = "http://localhost:8761/eureka"
    application = "default"
    weight = "1"
  }
  redis {
    serverAddr = "localhost:6379"
    db = "0"
  }
  zk {
    cluster = "default"
    serverAddr = "127.0.0.1:2181"
    session.timeout = 6000
    connect.timeout = 2000
  }
  consul {
    cluster = "default"
    serverAddr = "127.0.0.1:8500"
  }
  etcd3 {
    cluster = "default"
    serverAddr = "http://localhost:2379"
  }
  sofa {
    serverAddr = "127.0.0.1:9603"
    application = "default"
    region = "DEFAULT_ZONE"
    datacenter = "DefaultDataCenter"
    cluster = "default"
    group = "SEATA_GROUP"
    addressWaitTime = "3000"
  }
  file {
    name = "file.conf"
  }
}

config {
  # file、nacos 、apollo、zk、consul、etcd3
  type = "file"

  nacos {
    serverAddr = "localhost"
    namespace = ""
  }
  consul {
    serverAddr = "127.0.0.1:8500"
  }
  apollo {
    app.id = "seata-server"
    apollo.meta = "http://192.168.1.204:8801"
  }
  zk {
    serverAddr = "127.0.0.1:2181"
    session.timeout = 6000
    connect.timeout = 2000
  }
  etcd3 {
    serverAddr = "http://localhost:2379"
  }
  file {
    name = "file.conf"
  }
}
```

- 对主动发起全局事务的方法进行注解

```java
/**
     * 创建订单->调用库存服务扣减库存->调用账户服务扣减账户余额->修改订单状态
     * 简单说：下订单->扣库存->减余额->改状态
     */
    //rollbackFor = Exception.class表示对任意异常都进行回滚
    @GlobalTransactional(name = "fsp-tx-order",rollbackFor = Exception.class)
    @Override
    public void create(Order order) {
		...
    }
```

### Seata之原理

> **整体机制**
>
> 两阶段提交协议的演变：
>
> - 一阶段：业务数据和回滚日志记录在同一个本地事务中提交，释放本地锁和连接资源。
> - 二阶段：
>   - 提交异步化，非常快速地完成。
>   - 回滚通过一阶段的回滚日志进行反向补偿。

- 一阶段加载

在一阶段，Seata会拦截“业务SQL”

1. 解析SQL语义，找到“业务SQL" 要更新的业务数据，在业务数据被更新前，将其保存成"before image”
2. 执行“业务SQL" 更新业务数据，在业务数据更新之后,
3. 其保存成"after image”，最后生成行锁。

以上操作全部在一个数据库事务内完成, 这样保证了一阶段操作的原子性。

![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/20220902/image.3aepx5ztene0.webp)

- 二阶段提交

二阶段如果顺利提交的话，因为"业务SQL"在一阶段已经提交至数据库，所以Seata框架只需将一阶段保存的快照数据和行锁删掉，完成数据清理即可。

![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/20220902/image.17voa04kq7ek.webp)

- 二阶段回滚

二阶段如果是回滚的话，Seata 就需要回滚一阶段已经执行的 “业务SQL"，还原业务数据。

回滚方式便是用"before image"还原业务数据；但在还原前要首先要校验脏写，对比“数据库当前业务数据”和"after image"。

如果两份数据完全一致就说明没有脏写， 可以还原业务数据，如果不一致就说明有脏写, 出现脏写就需要转人工处理。

![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/20220902/image.5faqn74q58k0.webp)