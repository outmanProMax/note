# MQ介绍

## MQ是什么？

一种跨进程的通信的队列，用于上下游传递消息。使用了 MQ 之后。

## MQ有什么用？

1、流量消峰

当消费者无法一次性处理全部任务时，可以使用消息队列做为缓冲。

2、应用解耦

消息发送上游只需要依赖 MQ，不用依赖其他服务，这样当其他服务出现了故障后，消息发送上游仍然可以依靠消息队列进行临时运行，当对其他服务进行快速修复后，这个修复过程不会让用户察觉到。

3.异步处理 

有些服务间调用是异步的，如a服务异步调用吧b服务，使用MQ来实现异步结果的传递，这样a服务既不需要循环调用b服务的查询，也不用提供callback方法

## 四大核心概念

生产者：产生数据发送消息的程序

交换机：是 RabbitMQ 非常重要的一个部件，一方面它接收来自生产者的消息，另一方面它将消息 推送到队列中。交换机必须确切知道如何处理它接收到的消息，是将**这些消息推送到特定队列还是推送到多个队列**，亦或者是把消息丢弃，这个得有交换机类型决定

队列：是 RabbitMQ 内部使用的一种数据结构，尽管消息流经 RabbitMQ 和应用程序，但它们只能存储在队列中。队列仅受主机的内存和磁盘限制的约束，本质上是一个大的**消息缓冲区**。许多生产者可以将消息发送到一个队列，许多消费者可以尝试从一个队列接收数据。这就是我们使用队列的方式

消费者：消费与接收具有相似的含义。消费者大多时候是一个等待接收消息的程序。请注意生产者，消费者和消息中间件很多时候并不在同一机器上。同一个应用程序既可以是生产者又是可以是消费者。

## 各个名词介绍

![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/20220723/image.15tq7c46aeyk.webp)

`Broker`：接收和分发消息的应用，RabbitMQ Server 就是 Message Broker

`Virtual host`：出于多租户和安全因素设计的，把 AMQP 的基本组件划分到一个虚拟的分组中，类似于网络中的 namespace 概念。当多个不同的用户使用同一个 RabbitMQ server 提供的服务时，可以划分出多个 vhost，每个用户在自己的 vhost 创建 exchange／queue 等

`Connection`：publisher／consumer 和 broker 之间的 TCP 连接

`Channel`：如果每一次访问 RabbitMQ 都建立一个 Connection，在消息量大的时候建立 TCP Connection 的开销将是巨大的，效率也较低。Channel 是在 connection 内部建立的逻辑连接，如果应用程序支持多线程，通常每个 thread 创建单独的 channel 进行通讯，AMQP method 包含了 channel id 帮助客 户端和 message broker 识别 channel，所以 channel 之间是完全隔离的。Channel 作为轻量级的 Connection 极大减少了操作系统建立 TCP connection 的开销

`Exchange`：message 到达 broker 的第一站，根据分发规则，匹配查询表中的 routing key，分发 消息到 queue 中去。常用的类型有：direct (point-to-point)，topic (publish-subscribe) and fanout (multicast)

`Queue`：消息最终被送到这里等待 consumer 取走

`Binding`：exchange 和 queue 之间的虚拟连接，binding 中可以包含 routing key，Binding 信息被保 存到 exchange 中的查询表中，用于 message 的分发依据

## 核心代码

### RabbitMQUtils工具类

```java
ConnectionFactory factory = new ConnectionFactory();
//工厂IP 连接RabbitMQ对列
factory.setHost("192.168.200.130");
//用户名
factory.setUsername("root");
//密码
factory.setPassword("abc123");
//创建连接
Connection connection = factory.newConnection();
//获取信道
Channel channel = connection.createChannel();

return  channel;
```

### 消费者（Spring版）

1. 消费者连接到RabbitMQ Broker，建立一个连接(Connection )，开启一个信道(Channel) 。

2. 消费者向 RabbitMQ Broker 请求消费相应队列中的消息，可能会设置相应的回调函数，以及 做一些准备工作
3. 等待 RabbitMQ Broker 回应并投递相应队列中的消息， 消费者接收消息。
4. 消费者确认 ( ack) 接收到的消息。
5. RabbitMQ 从队列中删除相应己经被确认的消息。
6. 关闭信道。
7. 关闭连接。


```java
public static final String EXCHANGE_NAME = "topic_logs";
public static final String QUEUE_NAME = "Q1";

//接收消息
public static void main(String[] args) throws IOException, TimeoutException {
    Channel channel = RabbitMQUtils.getChannel();
    //声明交换机
    channel.exchangeDeclare(
                     EXCHANGE_NAME,         //交换机名字
                     BuiltinExchangeType.DIRECT)  //交换机类型
    //声明队列
    channel.queueDeclare(
                     QUEUE_NAME,    //队列的名称
                     true,            //durable:是否持久话
                     false,           //exclusive:是否独占
                     false,           //autoDelete:交换机没有被使用时，是否删掉
                     null);           //arguments:额外参数，传入一个Map用于设置x-dead-letter-exchange、x-dead-letter-exchange等
    //绑定
    channel.queueBind(QUEUE_NAME,		//队列的名称
                      EXCHANGE_NAME,	//交换机名字
                      "*.orange.*");	//routingKey

    //接收到消息时的回调
    DeliverCallback deliverCallback = (consumerTag,delivery) -> {
        String message= new String(delivery.getBody());
        System.out.println(message);
        //手动确认-确认应答（见消息应答）
        //channel.basicAck(delivery.getEnvelope().getDeliveryTag(),false);
    };
    //取消消息时的回调
    CancelCallback cancelCallback = consumerTag ->{
        System.out.println("消息消费被中断");
    };
    //接收消息
    channel.basicConsume(QUEUE_NAME,		//消费哪个队列
                         true,				//消费成功之后是否要自动应答true：代表自动应答false:代表手动应答
                         deliverCallback,	//接收到消息时的回调
                         cancelCallback);	//取消消息时的回调
}
```

### 生产者（Spring版)

1. 生产者连接 RabbitMQ ，建立 TCP 连接 ( Connection) ，开启信道/通道（ Channel ）
2. 生产者声明一个 Exchange （交换器），并设置相关属性，比如交换器类型、是否持久化等
3. 生产者声明一个队列井设置相关属性，比如是否排他、是否持久化、是否自动删除等
4. 生产者通过 bindingKey （绑定 Key ）将交换器和队列绑定（ binding ）起来
5. 生产者发送消息至 RabbitMQ Broker ，其中包含 routingKey （路由键）、交换器等信息
6. 相应的交换器根据接收到的 routingKey 查找相匹配的队列。
7. 如果找到，则将从生产者发送过来的消息存入相应的队列中。 如果没有找到，则根据生产者配置的属性选择丢弃还是回退给生产者
8. 关闭信道。
9. 关闭连接。

```java
public static final String EXCHANGE_NAME = "topic_logs";

//发送消息
public static void main(String[] args) throws IOException, TimeoutException {
    Channel channel = RabbitMQUtils.getChannel();
    //声明交换机
    channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.DIRECT)

    String message = "";
	//发送消息
    channel.basicPublish(EXCHANGE_NAME,			//交换机名字
                         "",					//routingKey
                         null,					//消息的配置属性，例如 MessageProperties.PERSISTENT_TEXT_PLAIN 表示消息持久化、设置过期时间等
                         message.getBytes());	//消息
}
```

# 队列

## 临时队列

具有随机名称 的队列，一旦我们断开了消费者的连接，队列将被自动删除。

```java
……
String QUEUE_NAME = channel.queueDeclare().getQueue();//获得随机名称
channel.queueDeclare(QUEUE_NAME, false, false, false, null);
……
```

## 延时队列

延时队列中的元素是希望 在指定时间到了以后或之前取出和处理，简单来说，延时队列就是用来存放需要在指定时间被处理的元素的队列。

### 方法1：队列设置TTL(设置指定时间之前取出和处理)

```java
Map<String, Object> params = new HashMap<>();
params.put("x-message-ttl",5000);
return QueueBuilder.durable("QA").withArguments(params).build(); // QA 队列的最大存活时间位 5000 毫秒
```

消息是否过期是在即将投递到消费者 之前判定的，如果当前队列有严重的消息积压情况，则已过期的消息也许还能存活较长时间

### 方法2：消息设置 TTL(设置指定时间之前取出和处理)

```java
rabbitTemplate.converAndSend("X","XC",message,correlationData -> {
    correlationData.getMessageProperties().setExpiration("5000");
});
```

一旦消息过期，就会被队列丢弃(如果配置了死信队列被丢到死信队 列中)

### 方法3：交换机设置TTL（需要额外安装插件）

```java
@Bean
public CustomExchange delayedExchange(){

    Map<String,Object> arguments = new HashMap<>();
    arguments.put("x-delayed-type","direct");
    /**
         * 1.交换机的名称
         * 2.交换机的类型 x-delayed-message
         * 3.是否需要持久化
         * 4.是否需要自动删除
         * 5.其他的参数
         */
    return new CustomExchange(DELAYED_EXCHANGE_NAME,"x-delayed-message",
                              true,false,arguments);
}
```

 在消息属性上设置 TTL 的方式，消息可能并不会按时「死亡」，如果第一个消息的延时时长很长，而第二个消息的延时时长很短，第二个消息并不会优先得到执行。在交换机中设置TTL的好处在于可以解决这个问题

## 优先级队列

优先级高的消息优先消费处理

- 设置队列最大优先级

```java
Map<String, Object> params = new HashMap();
// 优先级为 10
params.put("x-max-priority", 10);
channel.queueDeclare("hello", true, false, false, params);
```

- 设置消息优先级

```java
AMQP.BasicProperties properties =
                new AMQP.BasicProperties().builder().priority(5).build();
channel.basicPublish("",QUEUE_NAME,properties,message.getBytes());
```

## 惰性队列

惰性队列会尽可能的将消息存入磁盘中

当消费者由于各种各样的原因(比如消费者下线、宕机亦或者是由于维护而关闭等)而致使长时间内不能消费消息造成堆积时，惰性队列就很有必要了。

```java
Map<String, Object> args = new HashMap<String, Object>();
args.put("x-queue-mode", "lazy");
channel.queueDeclare("myqueue", false, false, false, args);
```

# 交换机

##  Fanout

交换机转发的消息，所有绑定的队列都接收

- 消费者

```java
channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.FANOUT)
```

## Direct

交换机转发的消息，符合对应routingKey的绑定队列进行接收

- 消费者

```
channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.DIRECT);

channel.queueBind(QUEUE_NAME,EXCHANGE_NAME,"info");
```

- 生产者

```java
channel.basicPublish(EXCHANGE_NAME,"info",null,message.getBytes("UTF-8"));
```

##  Topics

交换机转发的消息，符合对应routingKey的规则的绑定队列进行接收（可以完成一条消息发送到指定多个队列中）

***(星号)可以代替一个位置**

**#(井号)可以替代零个或多个位置**

- 消费者

```
channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.TOPIC);

channel.queueBind(QUEUE_NAME,EXCHANGE_NAME,"*.*.rabbit");
```

- 生产者

```java
channel.basicPublish(EXCHANGE_NAME,"quick.orange.rabbit",,null,message.getBytes("UTF-8"));
```

# 消息应答

## 自动应答

消息发送后立即被认为已经传送成功

## 手动应答

### 手动消息应答的方法

- `Channel.basicAck` (肯定确认应答)：

```java
basicAck(long deliveryTag, boolean multiple);
```

第一个参数是消息的标记，第二个参数表示是否应用于多消息，RabbitMQ 已知道该消息并且成功的处理消息，可以将其丢弃了

- `Channel.basicReject` (否定确认应答)

```java
basicReject(long deliveryTag, boolean requeue);
```

第一个参数表示拒绝 `deliveryTag` 对应的消息，第二个参数表示是否 `requeue`：true 则重新入队列，false 则丢弃或者进入死信队列。

该方法 reject 后，该消费者还是会消费到该条被 reject 的消息。

- `Channel.basicNack` (用于否定确认)：示己拒绝处理该消息，可以将其丢弃了

```java
basicNack(long deliveryTag, boolean multiple, boolean requeue);
```

第一个参数表示拒绝 `deliveryTag` 对应的消息，第二个参数是表示否应用于多消息，第三个参数表示是否 `requeue`，与 basicReject 区别就是同时支持多个消息，可以 拒绝签收 该消费者先前接收未 ack 的所有消息。拒绝签收后的消息也会被自己消费到。

- `Channel.basicRecover`

```java
basicRecover(boolean requeue);
```

是否恢复消息到队列，参数是是否 `requeue`，true 则重新入队列，并且尽可能的将之前 `recover` 的消息投递给其他消费者消费，而不是自己再次消费。false 则消息会重新被投递给自己。

### 例子

- 消费者

```java
DeliverCallback deliverCallback =(consumerTag,message) ->{

    //沉睡1S
    SleepUtils.sleep(1);
    System.out.println("接受到的消息:"+new String(message.getBody(),"UTF-8"));
    //手动应答
    /**
             * 1.消息的标记Tag
             * 2.是否批量应答 false表示不批量应答信道中的消息
             */
    channel.basicAck(message.getEnvelope().getDeliveryTag(),false);

};

channel.basicConsume(TASK_QUEUE_NAME, false, deliverCallback, cancelCallback);
```

# 消息应答和发布

## 持久化

将内存中的消息和队列存储在磁盘文件中

### 队列持久化

```java
channel.queueDeclare(TASK_QUEUE_NAME,durable,false,false,null);
```

### 消息持久化

```java
channel.basicPublish("",TASK_QUEUE_NAME,MessageProperties.PERSISTENT_TEXT_PLAIN,message.getBytes("UTF-8"));
```

## 生产者分发

### 公平分发

默认发送方法，即所有消费者依次处理一条消息

### 不公平分发

对消费者的缓冲区的上限进行限制，以避免缓冲区里面无限制的未确认消息问题

```java
int prefetchCount = 4;
channel.basicQos(prefetchCount);
```

## 消费者发布确认

### 单个消息确认

- 消费者

```java
//开启发布确认
channel.confirmSelect();

//批量发消息
for (int i = 0; i < 1000; i++) {
    String message = i+"";
    channel.basicPublish("",queueName,null,message.getBytes());
    //单个消息就马上进行发布确认
    boolean flag = channel.waitForConfirms();
}
```

### 批量确认发布

- 消费者

```java
//开启发布确认
channel.confirmSelect();

//批量确认消息大小
int batchSize =100;

//批量发送消息，批量发布确认
for (int i = 0; i < MESSAGE_COUNT; i++) {
    String message=i+"";
    channel.basicPublish("",queueName,null,message.getBytes());

    //判断达到100条消息的时候，批量确认一次
    if((i+1)%batchSize==0){
        //发布确认
        channel.waitForConfirms();
    }
}
```

### 异步确认发布

- 消费者

```java
//开启发布确认
channel.confirmSelect();

//消息确认回调的函数
ConfirmCallback ackCallback = (deliveryTag,multiple) ->{
    System.out.println("确认的消息:"+deliveryTag);
};
/**
         * 1.消息的标记
         * 2.是否为批量确认
         */
//消息确认失败回调函数
ConfirmCallback nackCallback= (deliveryTag,multiple) ->{
    System.out.println("未确认的消息:"+deliveryTag);
};

//准备消息的监听器 监听那些消息成功了，哪些消息失败了
/**
         * 1.监听哪些消息成功了
         * 2.监听哪些消息失败了
         */
channel.addConfirmListener(ackCallback,nackCallback);//异步通知

//批量发送消息
for (int i = 0; i < MESSAGE_COUNT; i++) {
    String message=i+"消息";
    channel.basicPublish("",queueName,null,message.getBytes());
}
```

## 极端情况下保证消息可靠投递

在生产环境中由于一些不明原因，导致 RabbitMQ 重启，在 RabbitMQ 重启期间生产者消息投递失败，导致消息丢失，需要手动处理和恢复。则可以采用以下两种方法保证消息不会丢失

### 消息回调与消息回退

- application.yml

```yml
spring:
  rabbitmq:
	publisher-confirm-type: correlated
  	template:
      mandatory: true
```

- MyCallBack类

```java
@Component
public class MyCallBack implements RabbitTemplate.ConfirmCallback,RabbitTemplate.ReturnsCallback {

    //注入
    @Autowired
    private RabbitTemplate rabbitTemplate;

    @PostConstruct
    public void init(){
        //注入接口
        rabbitTemplate.setConfirmCallback(this);
        rabbitTemplate.setReturnsCallback(this);
    }
    /**
     * 消息回调方法
     * @param correlationData  保存回调信息的Id及相关信息
     * @param ack              交换机收到消息 为true
     * @param cause            未收到消息的原因
     *
     */
    @Override
    public void confirm(CorrelationData correlationData, boolean ack,String cause) {
        String id = correlationData!=null?correlationData.getId():"";
        if(ack){
            log.info("交换机已经收到了ID为:{}的消息",id);
        }else {
            log.info("交换机还未收到ID为:{}的消息，由于原因:{}",id,cause);
        }
    }

    /**
     * 消息不可达时进行消息回退
     *  message      消息
     *  replyCode    编码
     *  replyText    退回原因
     *  exchange     从哪个交换机退回
     *  routingKey   通过哪个路由 key 退回
     */
    @Override
    public void returnedMessage(ReturnedMessage returned) {
        log.error("消息{},被交换机{}退回，退回原因:{},路由key:{}",
                new String(returned.getMessage().getBody()),returned.getExchange(),
                returned.getReplyText(),returned.getRoutingKey());
    }
}

```

### 备份交换机

<img src="assets\备份交换机.JPG">

```java
    //声明普通交换机,设置该交换机的备份交换机
    @Bean("confirmExchange")
    public DirectExchange confirmExchange(){
        return ExchangeBuilder.directExchange(CONFIRM_EXCHANGE_NAME)
                .durable(true).withArgument("alternate-exchange",BACKUP_EXCHANGE_NAME).build();
    }
	//备份交换机的配置与普通交换机类似，只不过备份交换机是使用Fanout的
```

# 死信

## 死信的概念

某些时候由于特定的原因**导致 queue 中的某些消息无法被消费**，这样的消息如果没有后续的处理，就变成了死信，有死信自然就有了死信队列。

```java
//1、声明死信和普通交换机，类型为direct
channel.exchangeDeclare(NORMAL_EXCHANGE, BuiltinExchangeType.DIRECT);
channel.exchangeDeclare(DEAD_EXCHANGE, BuiltinExchangeType.DIRECT);
////////////////////////////////////////////////////////////////////
//2、声明死信队列
 String DEAD_QUEUE = "dead-queue";
//声明死信队列
channel.queueDeclare(DEAD_QUEUE,false,false,false,null);
//死信队列绑定死信交换机与 routingkey
channel.queueBind(DEAD_QUEUE, DEAD_EXCHANGE, "dead_routingkey");

//////////////////////////////////////////////////////////////////////
//3、声明普通队列并绑定死信队列
String NORMAL_QUEUE = "normal-queue";
//普通队列的参数
Map<String,Object> arguments = new HashMap<>();
//arguments.put("x-message-ttl",10000);//过期时间 10s 由生产者指定 更加灵活
arguments.put("x-dead-letter-exchange",DEAD_EXCHANGE);//正常的队列设置死信交换机
arguments.put("x-dead-letter-routing-key","lisi");//设置死信routingKey
//声明普通队列
channel.queueDeclare(NORMAL_QUEUE,false,false,false,arguments);
//绑定普通的交换机与队列
channel.queueBind(NORMAL_QUEUE,NORMAL_EXCHANGE,"normal_routingkey");
```

## 死信的来源

### 消息 TTL 过期

TTL是 Time To Live 的缩写, 也就是生存时间

- 生产者

```java
AMQP.BasicProperties properties =
                new AMQP.BasicProperties().builder().expiration("10000").build();

channel.basicPublish(NORMAL_EXCHANGE,"zhangsan",properties,message.getBytes());
```

### 队列达到最大长度

队列满了，无法再添加数据到 MQ 中

- 消费者

```java
//设置正常队列长度的限制，例如发送10个消息，6个为正常，4个为死信
arguments.put("x-max-length",6);
```

### 消息被拒绝

(basic.reject 或 basic.nack) 并且 requeue = false

- 消费者

```java
DeliverCallback deliverCallback = (consumerTag,message) ->{
    String msg = new String(message.getBody(), "UTF-8");
    if(msg.equals("info5")){
        System.out.println("Consumer01接受的消息是："+msg+"： 此消息是被C1拒绝的");
        //requeue 设置为 false 代表拒绝重新入队 该队列如果配置了死信交换机将发送到死信队列中
        channel.basicReject(message.getEnvelope().getDeliveryTag(), false);
    }else {
        System.out.println("Consumer01接受的消息是："+msg);
        channel.basicAck(message.getEnvelope().getDeliveryTag(), false);
    }

};
//开启自动应答，也就是关闭手动应答
channel.basicConsume(NORMAL_QUEUE,false,deliverCallback,consumerTag -> {});
```

# 幂等性

一般是指我们在进行多次操作时，所得到的结果是一样的，不会因为进行多次操作而产生不同结果

## 为什么会有幂等性问题？

- 用户重复提交请求

- 消费者在消费处理成功，但是给 MQ 返回 ack 时网络中断，此时MQ未收到确认信息，该条消息会重新发给其他的消费者，造成一条消息重复消费

## 解决方法

- 利用数据库唯一约束去重

我们使用一些规则或者时间戳给的请求加上的唯一信息码，而该信息码对应数据库的数据具有唯一约束，当进行重复调用时，将会触发数据库唯一约束异常。

- Redis 的原子性

利用 redis 执行 setnx 命令，将消息通过setnx写入redis中，一旦这个消息被消费过就不会再次被消费