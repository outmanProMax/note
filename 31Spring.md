# 环境配置

- pom.xml

```xml
<dependencies>
    	<!--spring基本配置-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.2.10.RELEASE</version>
        </dependency>
    

    	<!--数据库mysql及其对应的spring和数据库连接池druid-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.32</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.2.10.RELEASE</version>
        </dependency>
    
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.16</version>
        </dependency>
    
    
    	<!--mybatis及其对应的spring-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.6</version>
        </dependency>

        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.0</version>
        </dependency>
    
    
		<!--测试库及其对应的spring-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.2.10.RELEASE</version>
        </dependency>
    
    	
    	<!--AOP-->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.4</version>
            <scope>compile</scope>
        </dependency>
    </dependencies>
```

- applicationContext.xml



# IOC

## 什么是IOC？

- 使用对象时，由主动new产生对象转换为由**外部**提供对象，此过程中对象创建控制权由程序转移到外部，此思想称为控制反转。
- Spring提供了一个容器，称为**IOC容器**，用来充当IOC思想中的"外部"，在需要对象时可以通过注入获取对象
- IOC容器负责对象的创建、初始化等一系列工作，其中包含了数据层和业务层的类对象
- 所谓的容器即产生受Spring控制的对象（Bean）的工厂

## 获取IOC容器

**类路径下的XML配置文件**

```java
ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
```

除了上面这种方式，Spring还提供了另外一种创建方式为:

**文件系统下的XML配置文件**

```java
ApplicationContext ctx = new FileSystemXmlApplicationContext("applicationContext.xml");
```

**使用纯注解开发**：

```java
ApplicationContext ctx = new AnnotationConfigApplicationContext(SpringConfig.class);
```

## 容器结构

- BeanFactory是IoC容器的顶层接口，初始化BeanFactory对象时，加载的bean延迟加载（懒加载）
- ApplicationContext接口是Spring容器的核心接口，初始化时bean立即加载（饿加载）

Note：可配置bean的属性**lazy-init**设置立即加载和延迟加载

- ApplicationContext接口提供基础的bean操作相关方法，通过其他接口扩展其功能
- ApplicationContext接口常用初始化类
  - ClasspathXmlApplicationContext : 根据类路径（被编译后为字节码文件夹）加载xml配置文件，并创建IOC容器对象。
  - FileSystemXmlApplicationContext ：根据系统路径加载xml配置文件，并创建IOC容器对象。
  - AnnotationConfigApplicationContext ：加载注解类配置，并创建IOC容器。

# Bean

## 什么是bean？

- 被创建或被管理的对象在IOC容器中统称为**bean**

## bean基本配置（applicationContext）

```xml
<beans>
	<bean id="bookService" name="service service4 bookEbi" class="com.itheima.service.impl.BookServiceImpl" scope="singleton"></bean>
</beans>
```

- id：bean的唯一标识
- name（默认与id相同）;bean的别名，使用逗号，分号和空格进行分隔
- class：bean绑定类型的全路径名

- scope：（默认为singleton）：bean的作用范围，singleton为单例，prototype为非单例

## bean实例化方法（dao，factory，applicationContext）

### 方式一：无参构造实例化bean（如果没有特别做额外处理就是选用这一种）

**1、在BookDaoImpl类中添加一个无参构造函数**

```java
public interface BookDao {
    public void save();
}

public class BookDaoImpl implements BookDao {
    public BookDaoImpl() {//构造函数
        System.out.println("book dao constructor is running ....");
    }
    public void save() {
        System.out.println("book dao save ...");
    }

}

public class AppForInstanceBook {
    public static void main(String[] args) {
        ApplicationContext ctx = new 
            ClassPathXmlApplicationContext("applicationContext.xml");
        BookDao bookDao = (BookDao) ctx.getBean("bookDao");
        bookDao.save();

    }
}
```

**2、applicationContext.xml**

```xml
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>
```

### 方式二：使用静态工厂实例化bean

**1、创建一个工厂类OrderDaoFactory并提供一个静态方法**

```java
public interface OrderDao {
    public void save();
}

public class OrderDaoImpl implements OrderDao {
    public void save() {
        System.out.println("order dao save ...");
    }
}

public class OrderDaoFactory {//静态工厂类
    public static OrderDao getOrderDao(){
        System.out.println("factory setup....");//模拟必要的业务操作
        return new OrderDaoImpl();
    }
}

public class AppForInstanceOrder {
    public static void main(String[] args) {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");

        OrderDao orderDao = (OrderDao) ctx.getBean("orderDao");

        orderDao.save();

    }
}
```

**2、applicationContext.xml**

```xml
<bean id="orderDao" class="com.itheima.factory.OrderDaoFactory" factory-method="getOrderDao"/>
```

- class:工厂类的类全名
- factory-mehod:具体工厂类中创建对象的方法名

### 方式三：使用实例工厂实例化bean

**1、创建一个工厂类OrderDaoFactory并提供一个普通方法**

```java
public interface UserDao {
    public void save();
}

public class UserDaoImpl implements UserDao {

    public void save() {
        System.out.println("user dao save ...");
    }
}

public class UserDaoFactory {//实例工厂类
    public UserDao getUserDao(){
        return new UserDaoImpl();
    }
}

public class AppForInstanceUser {
    public static void main(String[] args) {
        ApplicationContext ctx = new 
            ClassPathXmlApplicationContext("applicationContext.xml");
        UserDao userDao = (UserDao) ctx.getBean("userDao");
        userDao.save();
    }
}
```

**2、applicationContext.xml**

```xml
<bean id="userFactory" class="com.itheima.factory.UserDaoFactory"/>

<bean id="userDao" factory-method="getUserDao" factory-bean="userFactory"/>
```

* factory-bean:工厂的实例对象

* factory-method:工厂对象中的具体创建对象的方法名

### 方式四：使用FactoryBean实例化bean

**创建一个UserDaoFactoryBean的类，实现FactoryBean接口，重写接口的方法**

```java
public interface UserDao {
    public void save();
}

public class UserDaoImpl implements UserDao {

    public void save() {
        System.out.println("user dao save ...");
    }
}

public class UserDaoFactoryBean implements FactoryBean<UserDao> { //代替实例工厂
    //代替原始实例工厂中创建对象的方法
    @Override
    public UserDao getObject() throws Exception {
        return new UserDaoImpl();
    }
    //返回所创建类的Class对象
    @Override
    public Class<?> getObjectType() {
        return UserDao.class;
    }
}

public class AppForInstanceUser {
    public static void main(String[] args) {
        ApplicationContext ctx = new 
            ClassPathXmlApplicationContext("applicationContext.xml");
        UserDao userDao = (UserDao) ctx.getBean("userDao");
        userDao.save();
    }
}
```

**2、applicationContext.xml**

```xml
<bean id="userDao" class="com.itheima.factory.UserDaoFactoryBean"/>
```

## 获取bean对象

方式一:根据id获取

```java
BookDao bookDao = (BookDao) ctx.getBean("bookDao");
```

这种方式存在的问题是每次获取的时候都需要进行类型转换，有没有更简单的方式呢?

方式二：根据id和类型获取

```
BookDao bookDao = ctx.getBean("bookDao"，BookDao.class);
```

这种方式可以解决类型强转问题，但是参数又多加了一个，相对来说没有简化多少。

方式三:根据类型获取

```
BookDao bookDao = ctx.getBean(BookDao.class);
```

这种方式就类似我们之前所学习依赖注入中的按类型注入。必须要确保IOC容器中该类型对应的bean对象只能有一个。

## bean的生命周期

### 初始化和销毁方法

#### 方法一：init-method和destroy-method

1、针对这两个阶段，我们在BooDaoImpl类中分别添加两个方法，**方法名任意**

```java
public class BookDaoImpl implements BookDao {
    public void save() {
        System.out.println("book dao save ...");
    }
    //表示bean初始化对应的操作
    public void init(){
        System.out.println("init...");
    }
    //表示bean销毁前对应的操作
    public void destory(){
        System.out.println("destory...");
    }
}
```

2、applicationContext.xml添加配置，如下:

```xml
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl" init-method="init" destroy-method="destory"/>
```

#### 方法二：@PostConstruct和@PreDestroy

| 名称 | @PostConstruct         |
| ---- | ---------------------- |
| 类型 | 方法注解               |
| 位置 | 方法上                 |
| 作用 | 设置该方法为初始化方法 |
| 属性 | 无                     |
| 代替 | bean的属性init-method  |

| 名称 | @PreDestroy              |
| ---- | ------------------------ |
| 类型 | 方法注解                 |
| 位置 | 方法上                   |
| 作用 | 设置该方法为销毁方法     |
| 属性 | 无                       |
| 代替 | bean的属性destroy-method |

#### 方法三：实现重写InitializingBean接口下的afterPropertiesSet和DisposableBean接口下的destroy

修改BookServiceImpl类，添加两个接口`InitializingBean`， `DisposableBean`并实现接口中的两个方法`afterPropertiesSet`和`destroy`

```java
public class BookServiceImpl implements BookService, InitializingBean, DisposableBean {
    private BookDao bookDao;
    public void setBookDao(BookDao bookDao) {
        this.bookDao = bookDao;
    }
    public void save() {
        System.out.println("book service save ...");
        bookDao.save(); 
    }
    public void destroy() throws Exception {
        System.out.println("service destroy");
    }
    public void afterPropertiesSet() throws Exception {
        System.out.println("service init");
    }
}
```

### bean的生命周期控制

- 实例化：创建Bean对象，并分配内存空间
- 依赖注入：使用setter方、Autowire注解等进行依赖注入

* 初始化
  * 执行bean初始化方法，顺序为BeanPostProcessor中的预初始化方法-》 PostConstruct注解的方法-》实现InitializingBean下的afterPropertiesSet或Init-method方法-》BeanPostProcessor中的后置初始化方法
* 使用bean
  * 1.执行业务操作
* 关闭/销毁容器
  * 执行bean销毁方法， PreDestroy注解的方法-》实现DisposableBean下的destroy或destroy-method方法

**NOTE：BeanPostProcessor中的预/后置初始化方法是所有Bean初始化都会调用的，所以这两个方法不能写在某个具体的 Bean 中，否则（这两个方法）不会执行。 需要另外创建一个类来实现这两个方法**

### 关闭容器的两种方式

* close()方法，调用的时候关闭

需要将ApplicationContext更换成ClassPathXmlApplicationContext

```java
ClassPathXmlApplicationContext ctx = new 
    ClassPathXmlApplicationContext("applicationContext.xml");
ctx.close();
```

* registerShutdownHook()方法，JVM退出前调用关闭容器

调用ctx的registerShutdownHook()方法

```
ctx.registerShutdownHook();
```

**注意:**registerShutdownHook在ApplicationContext中也没有

## bean的加载方式

### 方式一：配置文件+```<bean/>```标签

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--xml方式声明自己开发的bean-->
    <bean id="cat" class="Cat"/>

    <!--xml方式声明第三方开发的bean-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"/>
</beans>
```

### 方式二：配置文件批量扫描+注解定义bean

- 注解定义bean

```JAVA
@Component("tom")//@Controller、@Service、@Component、@Repository等等均可
public class Cat {
}
```

配置文件扫描+

```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
    ">
    <!--指定扫描加载bean的位置-->
    <context:component-scan base-package="com.itheima.bean,com.itheima.config"/>
</beans>
```

### 方式三：@ComponentScan（本质是方式二的注解版本）

```java
@Configuration
@ComponentScan({"com.itheima.bean","com.itheima.config"})
public class SpringConfig3 {
}
```

### 方式四：@Import注解注入bean（一对一加载bean）

```
@Import({Dog.class,DbConfig.class})
public class SpringConfig3 {
}
```

### 方式五：register编程形式注册bean（少用）

​		前面介绍的加载bean的方式都是在容器启动阶段完成bean的加载，下面这种方式就比较特殊了，可以在容器初始化完成后手动加载bean。通过这种方式可以实现编程式控制bean的加载。

```java
public class App5 {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(SpringConfig.class);
        //上下文容器对象已经初始化完毕后，手工加载bean
        ctx.register(Mouse.class);
    }
}
```

### 方式六：导入实现了ImportSelector接口的类（少用）

​		在方式五种，我们感受了bean的加载可以进行编程化的控制，添加if语句就可以实现bean的加载控制了。但是毕竟是在容器初始化后实现bean的加载控制，那是否可以在容器初始化过程中进行控制呢？答案是必须的。实现ImportSelector接口的类可以设置加载的bean的全路径类名，记得一点，只要能编程就能判定，能判定意味着可以控制程序的运行走向，进而控制一切。

​		现在又多了一种控制bean加载的方式，或者说是选择bean的方式。

```java
public class MyImportSelector implements ImportSelector {
    @Override
    public String[] selectImports(AnnotationMetadata metadata) {
        //各种条件的判定，判定完毕后，决定是否装载指定的bean
        boolean flag = metadata.hasAnnotation("org.springframework.context.annotation.Configuration");
        if(flag){
            return new String[]{"com.itheima.bean.Dog"};
        }
        return new String[]{"com.itheima.bean.Cat"};
    }
}

```

### 方式七：导入实现了ImportBeanDefinitionRegistrar接口的类（少用）

​		方式六中提供了给定类全路径类名控制bean加载的形式，如果对spring的bean的加载原理比较熟悉的小伙伴知道，其实bean的加载不是一个简简单单的对象，spring中定义了一个叫做BeanDefinition的东西，它才是控制bean初始化加载的核心。BeanDefinition接口中给出了若干种方法，可以控制bean的相关属性。说个最简单的，创建的对象是单例还是非单例，在BeanDefinition中定义了scope属性就可以控制这个。如果你感觉方式六没有给你开放出足够的对bean的控制操作，那么方式七你值得拥有。我们可以通过定义一个类，然后实现ImportBeanDefinitionRegistrar接口的方式定义bean，并且还可以让你对bean的初始化进行更加细粒度的控制，不过对于新手并不是很友好。忽然给你开放了若干个操作，还真不知道如何下手。

```java
public class MyRegistrar implements ImportBeanDefinitionRegistrar {
    @Override
    public void registerBeanDefinitions(AnnotationMetadata metadata, BeanDefinitionRegistry registry) {
        BeanDefinition beanDefinition = 	
            BeanDefinitionBuilder.rootBeanDefinition(BookServiceImpl2.class).getBeanDefinition();
        registry.registerBeanDefinition("bookService",beanDefinition);
    }
}
```

### 方式八：导入实现了BeanDefinitionRegistryPostProcessor接口的类（少用）

​		上述七种方式都是在容器初始化过程中进行bean的加载或者声明，但是这里有一个bug。这么多种方式，它们之间如果有冲突怎么办？谁能有最终裁定权？这是个好问题，当某种类型的bean被接二连三的使用各种方式加载后，在你对所有加载方式的加载顺序没有完全理解清晰之前，你还真不知道最后谁说了算。即便你理清楚了，保不齐和你一起开发的猪队友又添加了一个bean，得嘞，这下就热闹了。

​		spring挥舞它仲裁者的大刀来了一个致命一击，都别哔哔了，我说了算，BeanDefinitionRegistryPostProcessor，看名字知道，BeanDefinition意思是bean定义，Registry注册的意思，Post后置，Processor处理器，全称bean定义后处理器，干啥的？在所有bean注册都折腾完后，它把最后一道关，说白了，它说了算，这下消停了，它是最后一个运行的。

```java
public class MyPostProcessor implements BeanDefinitionRegistryPostProcessor {
    @Override
    public void postProcessBeanDefinitionRegistry(BeanDefinitionRegistry registry) throws BeansException {
        BeanDefinition beanDefinition = 
            BeanDefinitionBuilder.rootBeanDefinition(BookServiceImpl4.class).getBeanDefinition();
        registry.registerBeanDefinition("bookService",beanDefinition);
    }
}

```

# DI

## 什么是DI？

依赖注入是指在 Spring IOC 容器创建对象的过程中，将所依赖的对象通过配置进行注入，以减低程序间的耦合

## 注入方式

### setter注入

**1、在BookDaoImpl类中声明对应类型的属性,并提供对应的setter方法**

```java
public class BookServiceImpl implements BookService {
    
    private BookDao bookDao;
    private String databaseName;
    
    //引用类型注入
    public void setBookDao(BookDao bookDao) {
        this.bookDao = bookDao;
    }
    
    //简单类型注入
    public void setDatabaseName(String databaseName) {
        this.databaseName = databaseName;
    }
}
```

**2、applicationContext.xml**

```xml
<bean id="bookService" class="com.itheima.service.impl.BookServiceImpl">
	<!--简单类型注入-->
	<property name="databaseName" value="mysql"/>
	<!--引用类型注入-->
	<property name="bookDao" ref="bookDao"/>
</bean>
```

### 构造器注入

**1、在BookDaoImpl类中声明对应类型的属性,并提供对应的setter方法**

```java
public class BookServiceImpl implements BookService {
    
    private BookDao bookDao;
    private String databaseName;
    
    //引用类型注入
    public BookServiceImpl(BookDao bookDao, UserDao userDao) {
        this.bookDao = bookDao;
        this.userDao = userDao;
    }
    
    //简单类型注入，实际使用时只能有一个构造器
    public BookDaoImpl(String databaseName, int connectionNum) {
        this.databaseName = databaseName;
        this.connectionNum = connectionNum;
    }
}
```

**2、applicationContext.xml**

```xml
<bean id="bookService" class="com.itheima.service.impl.BookServiceImpl">
	<!--简单类型注入-->
    <constructor-arg name="databaseName" value="mysql"/>
    <constructor-arg name="connectionNum" value="666"/>
	<!--引用类型注入-->
    <constructor-arg name="bookDao" ref="bookDao"/>
    <constructor-arg name="userDao" ref="userDao"/>
</bean>
```

**2.5、非主流的配置applicationContext.xml**

方式一:按照类型注入

```xml
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl">
    <constructor-arg type="int" value="10"/>
    <constructor-arg type="java.lang.String" value="mysql"/>
</bean>
```

方式二:按照索引下标注入

```xml
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl">
    <constructor-arg index="1" value="100"/>
    <constructor-arg index="0" value="mysql"/>
</bean>
```

Note:使用构造器注入完成强制依赖的注入，使用setter注入完成可选依赖的注入

### 自动配置

**applicationContext.xml**

```xml
    <!--autowire属性：开启自动装配，通常使用按类型装配-->
    <bean id="bookService" class="com.itheima.service.impl.BookServiceImpl" autowire="byName"/>
```

- 使用按类型装配时（byType）必须保障容器中相同类型的bean唯一，推荐使用
- 使用按名称装配时（byName）必须保障容器中具有指定名称的bean，因变量名与配置耦合，不推荐使用

### 集合批量注入

**1、在BookDaoImpl类中声明对应类型的属性,并提供对应的setter方法**

略

**2、applicationContext.xml**

```xml
<bean id="bookService" class="com.itheima.service.impl.BookServiceImpl">
	<property name="array">
        <array>
            <value>100</value>
            <value>200</value>
        </array>
	</property>
    <property name="list">
        <list>
            <value>itcast</value>
            <value>itheima</value>
        </list>
	</property>
    <property name="set">
        <set>
            <value>itcast</value>
            <value>itheima</value>
        </set>
    </property>
    <property name="map">
        <map>
            <entry key="country" value="china"/>
            <entry key="province" value="henan"/>
        </map>
    </property>
    <property name="properties">
        <props>
            <prop key="country">china</prop>
            <prop key="province">henan</prop>
        </props>
    </property>
</bean>
```

- property标签表示setter方式注入，构造方式注入constructor-arg标签内部也可以写`<array>`、`<list>`、`<set>`、`<map>`、`<props>`标签

# properties

- 声明环境变量，一般用于对于第三方bean进行配置管理

步骤1:准备properties配置文件

resources下创建一个jdbc.properties文件,并添加对应的属性键值对

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://127.0.0.1:3306/spring_db
jdbc.username=root
jdbc.password=root
```

步骤2:开启`context`命名空间

在applicationContext.xml中开`context`命名空间

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
            http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context
            http://www.springframework.org/schema/context/spring-context.xsd">
</beans>
```

步骤3:加载properties配置文件

在配置文件中使用`context`命名空间下的标签来加载properties配置文件

```xml
<context:property-placeholder location="jdbc.properties"/>
```

步骤4:完成属性注入

使用`${key}`来读取properties配置文件中的内容并完成属性注入

```xml
<!--    1.开启context命名空间-->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
            http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context
            http://www.springframework.org/schema/context/spring-context.xsd
            ">
    
    <!--    2.使用context空间加载properties文件-->
    <context:property-placeholder location="classpath*:*.properties" system-properties-mode="NEVER"/>
    
    <!--    3.使用属性占位符${}读取properties文件中的属性-->
<!--    说明：idea自动识别${}加载的属性值，需要手工点击才可以查阅原始书写格式-->
    <bean class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${jdbc.driver}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
</beans>
```

- classpath\*:*properties  ： 设置加载当前工程类路径和当前工程所依赖的所有jar包中的——所有properties文件

- system-properties-mode属性：是否加载系统属性

# 注解开发

## @Component等(配置bean)

| 名称 | @Component/@Controller/@Service/@Repository |
| ---- | ------------------------------------------- |
| 类型 | 类注解                                      |
| 位置 | 类定义上方                                  |
| 作用 | 设置该类为spring管理的bean                  |
| 属性 | value（默认）：定义bean的id                 |
| 代替 | \<bean>\</bean>                             |

### tip：@Mapper和@Repository的区别

1. @Mapper是MyBatis的注解，@Repository是Spring中的注解，这些注解就是声明一个Bean。

2. @Mapper注解不需要在SpringBoot启动类上配置扫描类；而使用@Repository需要在SpringBoot启动类上配置扫描类@MapperScan扫描dao类

## @Configuration(配置类声明）

| 名称 | @Configuration              |
| ---- | --------------------------- |
| 类型 | 类注解                      |
| 位置 | 类定义上方                  |
| 作用 | 设置该类为spring配置类      |
| 属性 | value（默认）：定义bean的id |
| 代替 | applicationContext.xml；\<beans>\</beans> |

配置类下可以做Bean的扫描加载（@ComponentScan、@Import）、将方法的返回值配置为Bean（@Bean）、声明环境变量

## @ComponentScan(把指定路径下带有指定注解的类自动装配到bean容器里)

| 名称 | @ComponentScan                                           |
| ---- | -------------------------------------------------------- |
| 类型 | 类注解                                                   |
| 位置 | 类定义上方                                               |
| 作用 | 设置spring配置类扫描路径，用于加载使用注解格式定义的bean |
| 属性 | value（默认）：扫描路径，此路径可以逐层向下扫描          |
| 代替 | <context:component-scan base-package="com.itheima"/>                 |

在@Configuration注解的配置类下才可以使用

**说明:**

component-scan

* component:组件,Spring将管理的bean视作自己的一个组件
* scan:扫描

base-package指定Spring框架扫描的包路径，它会扫描指定包及其子包中的所有类上的注解。

* 包路径越多[如:com.itheima.dao.impl]，扫描的范围越小速度越快
* 包路径越少[如:com.itheima],扫描的范围越大速度越慢
* 一般扫描到项目的组织名称即Maven的groupId下[如:com.itheima]即可。

例如：

```java
@Configuration
@ComponentScan("com.itheima")
public class SpringConfig {
}
```

## Bean的作用范围

### @Scope

| 名称 | @Scope                                                       |
| ---- | ------------------------------------------------------------ |
| 类型 | 类注解                                                       |
| 位置 | 类定义上方                                                   |
| 作用 | 设置该类创建对象的作用范围<br/>可用于设置创建出的bean是否为单例对象 |
| 属性 | value（默认）：定义bean作用范围，<br/>**默认值singleton（单例），可选值prototype（非单例）** |
| 代替 | bean的属性scope                                              |

## DI依赖注入

### @Autowired（按类型自动装配）


| 名称 | @Autowired                                                   |
| ---- | ------------------------------------------------------------ |
| 类型 | 属性注解  或  方法注解（了解）  或  方法形参注解（了解）     |
| 位置 | 属性定义上方  或  标准set方法上方  或  类set方法上方  或  方法形参前面 |
| 作用 | 为引用类型自动装配                                           |
| 属性 | required：true/false，定义该属性是否允许为null               |
| 代替 | bean的属性autowire="byType"                                  |

### @Qualifier（按名字自动装配）

| 名称 | @Qualifier                                           |
| ---- | ---------------------------------------------------- |
| 类型 | 属性注解  或  方法注解（了解）                       |
| 位置 | 属性定义上方  或  标准set方法上方  或  类set方法上方 |
| 作用 | 为引用类型属性指定注入的beanId                       |
| 属性 | value（默认）：设置注入的beanId                      |
| 代替 | bean的属性autowire="byName"                                  |

### @Value（简单类型注入）

| 名称 | @Value                                               |
| ---- | ---------------------------------------------------- |
| 类型 | 属性注解  或  方法注解（了解）                       |
| 位置 | 属性定义上方  或  标准set方法上方  或  类set方法上方 |
| 作用 | 为  基本数据类型  或  字符串类型  属性设置值         |
| 属性 | value（默认）：要注入的属性值                        |
| 代替 | \<property name="" value=""/>                        |

### @PropertySource（加载properties文件）

| 名称 | @PropertySource                                              |
| ---- | ------------------------------------------------------------ |
| 类型 | 类注解                                                       |
| 位置 | 类定义上方                                                   |
| 作用 | 加载properties文件中的属性值，不允许使用通配符               |
| 属性 | value（默认）：设置加载的properties文件对应的文件名或文件名组成的数组 |
| 代替 |  <context:property-placeholder location=""/>                                 |

例如：

````java
@PropertySource("jdbc.properties")
public class SpringConfig {
}
````

### @ConfigurationProperties和@EnableConfigurationProperties

Note:该注释是在SpringBoot体系中的注释

@ConfigurationProperties

可不用，配置属性名的通用前缀，在spring开发过程中我们常使用到@ConfigurationProperties注解，通常是用来将properties和yml配置文件属性转化为bean对象使用

@EnableConfigurationProperties（类名.class）

可以调用使用@ConfigurationProperties的类的对象，与@ConfigurationProperties配对使用

```properties
#application.properties
gulimall.thread.core-size=20
gulimall.thread.max-size=200
gulimall.thread.keep-alive-time=10
```

```java
@Component
@ConfigurationProperties(prefix = "gulimall.thread")
@Data
public class ThreadPollConfigProperties {
    //也可使用@Value("${gulimall.thread.core-size}")一个属性一个属性进行注入
    private Integer coreSize;
    private Integer maxSize;
    private Integer keepAliveTime;
}


@Configuration
@EnableConfigurationProperties(ThreadPollConfigProperties.class)
public class MyThreadConfig {
    
    @Autowired
    ThreadPollConfigProperties threadPollConfigProperties;

    @Bean
    public ThreadPoolExecutor threadPoolExecutor(){
        return new ThreadPoolExecutor(20, 200, 10, TimeUnit.SECONDS,
                new LinkedBlockingDeque<>(100000),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy());
    }
}
```



## 管理第三方的bean

### @Bean（将方法的返回值制作为bean）

| 名称 | @Bean                                  |
| ---- | -------------------------------------- |
| 类型 | 方法注解                               |
| 位置 | 方法定义上方                           |
| 作用 | 设置该方法的返回值作为spring管理的bean |
| 属性 | value（默认）：定义bean的id            |

由于无法进入第三方的源码使用@Component配置bean，所以必须使用@Bean制作bean，之后就可以将当前方法的返回值就可以交给spring管控

以Druid的Bean配置为例：

```java
@Bean
public DataSource dataSource(){
    DruidDataSource ds = new DruidDataSource();
    ds.setDriverClassName("com.mysql.jdbc.Driver");
    ds.setUrl("jdbc:mysql://localhost:3306/spring_db");
    ds.setUsername("root");
    ds.setPassword("root");
    return ds;
}
```

### @Import（进行第三方bean的包扫描）

| 名称 | @Import                                                      |
| ---- | ------------------------------------------------------------ |
| 类型 | 类注解                                                       |
| 位置 | 类定义上方                                                   |
| 作用 | 导入配置类                                                   |
| 属性 | value（默认）：定义导入的配置类类名，当配置类有多个时使用数组格式一次性导入多个配置类 |

#### 对比@ComponentScan、@Import和@ImportResource

- @ComponentScan对包进行扫描，对扫描到的bean进行加载，即其扫描的类必须本身是Bean（类上加Configuration或Component等注解、但是Bean注解的方法不行）
- @Import可以要求该类可以不是bean，导入时会自动将类配置成bean，并且对该类里面的属性进行解析（无需加Component等注解）
- @ImportResource可扫描xml文件进行包的扫描，一般用于完成对一些老系统的兼容

### 第三方bean依赖注入

-  简单类型

在Config类中定义字段并使用@Value进行依赖注入

- 引用类型

1、在在SpringConfig中使用@ComponentScan完成对包的扫描

2、为bean定义方法设置形参，容器会根据类型自动装配对象，如

```java
public DataSource dataSource(BookDao bookDao)
```

# AOP

## 什么是AOP？

AOP(Aspect Oriented Programming)面向切面编程，一种编程范式，在不惊动原始设计的基础上为方法进行功能**增强**，SpringAOP的核心本质是采用代理模式实现的

## 切面

- 通知（Advice）：若干个方法的共性功能，在切入点处执行，最终体现为一个方法 
- 切面（Aspect）：描述通知与切入点的对应关系

1、在aop.MyAdvice定义切面类

```java
@Component
@Aspect
public class MyAdvice {
    @Pointcut("execution(void com.itheima.dao.BookDao.update1())")//被代理的类的方法
    private void pt(){}

    @Before("pt()")
    public void method(){
        System.out.println(System.currentTimeMillis());
    }
}
```

2、在SpringConfig中配置启用切面

```java
@Configuration
@ComponentScan("com.itheima")
@EnableAspectJAutoProxy
public class SpringConfig {

}
```

### @EnableAspectJAutoProxy  

| 名称 | @EnableAspectJAutoProxy |
| ---- | ----------------------- |
| 类型 | 配置类注解              |
| 位置 | 配置类定义上方          |
| 作用 | 开启注解格式AOP功能     |

### @Aspect

| 名称 | @Aspect               |
| ---- | --------------------- |
| 类型 | 类注解                |
| 位置 | 切面类定义上方        |
| 作用 | 设置当前类为AOP切面类 |

## 切入点

* 切入点（Pointcut）：匹配连接点的式子，指向需要增强的方法

```java
@Pointcut("execution(void com.itheima.dao.BookDao.update())")//需要增强的方法
private void pt(){}//切入点定义依托一个不具有实际意义的方法进行，即无参数、无返回值、方法体无实际逻辑
```

### @Pointcut   

| 名称 | @Pointcut                   |
| ---- | --------------------------- |
| 类型 | 方法注解                    |
| 位置 | 切入点方法定义上方          |
| 作用 | 设置切入点方法              |
| 属性 | value（默认）：切入点表达式 |

### 切入点表达式

```
execution(public User com.itheima.service.UserService.findById(int))
```

* 语法格式
  * execution：动作关键字，描述切入点的行为动作，例如execution表示执行到指定切入点
  * **访问控制修饰符**（public）：针对接口开发均采用public描述（可省略）
  * **返回值类型**（User）：对于增删改类使用精准类型加速匹配，对于查询类使用\*通配快速描述
  * **包名**（com.itheima.service.UserService）：书写尽量不使用..匹配，效率过低，常用\*做单个包描述匹配，或精准匹配
  * **接口名/类名**（UserService）：通常描述接口，而不描述实现类（如果描述到实现类，就出现紧耦合了），书写名称与模块相关的采用\*匹配，例如UserService书写成\*Service，绑定业务层接口名
  * **方法名**（findById）：书写以**动词**进行精准匹配，名词采用*匹配，例如getById书写成getBy*,selectAll书写成selectAll
  * **形参**（int）:参数，直接写参数的类型，多个类型用逗号隔开
  * **异常**：通常不使用异常作为匹配规则
* 通配符
  * `*`:单个独立的任意符号，可以独立出现，也可以作为前缀或者后缀的匹配符出现
  * `..`：多个连续的任意符号，可以独立出现，常用于简化包名与参数的书写
  * `+`：专用于匹配子类类型

## 通知

- 通知是在切点处执行的代码。通知定义了在目标方法执行前、执行后或异常抛出时需要执行的代码

```java
public int method(){//@Around环绕通知可以实现其他四种通知类型的功能
    //@Before
    try{
        //@Before
        //原始方法
        //@AfterReturning
    }catch(Exception e){
        //@AfterThrowing
    }
    //@After
}
```

### @Before

| 名称 | @Before                                                      |
| ---- | ------------------------------------------------------------ |
| 类型 | 方法注解                                                     |
| 位置 | 通知方法定义上方                                             |
| 作用 | 设置当前通知方法与切入点之间的绑定关系，当前通知方法在原始切入点方法前运行 |
| 参数 | 传入参数：JoinPoint，返回值：无，异常：无 |

```java
@Before("pt()")
public void before(JoinPoint jp) 
    Object[] args = jp.getArgs();
    ……
}
```

### @After

| 名称 | @After                                                       |
| ---- | ------------------------------------------------------------ |
| 类型 | 方法注解                                                     |
| 位置 | 通知方法定义上方                                             |
| 作用 | 设置当前通知方法与切入点之间的绑定关系，当前通知方法在原始切入点方法后运行 |
| 参数 | 传入参数：JoinPoint，返回值：无，异常：无 |

```java
@After("pt()")
public void after(JoinPoint jp) 
    Object[] args = jp.getArgs();
    ……
}
```

### @AfterReturning  

| 名称 | @AfterReturning                                              |
| ---- | ------------------------------------------------------------ |
| 类型 | 方法注解                                                     |
| 位置 | 通知方法定义上方                                             |
| 作用 | 设置当前通知方法与切入点之间绑定关系，当前通知方法在原始切入点方法正常执行完毕后执行 |
| 参数 | 传入参数：JoinPoint，返回值：returning，异常：无 |

```java
@AfterReturning(value = "pt()",returning = "ret")
public void afterReturning(JoinPoint jp, Object ret) {
    Object[] args = jp.getArgs();
    ……
}
```

### @AfterThrowing  

| 名称 | @AfterThrowing                                               |
| ---- | ------------------------------------------------------------ |
| 类型 | 方法注解                                                     |
| 位置 | 通知方法定义上方                                             |
| 作用 | 设置当前通知方法与切入点之间绑定关系，当前通知方法在原始切入点方法运行抛出异常后执行 |
| 参数 | 传入参数：JoinPoint，返回值：无，异常：参数读取 |

```java
@AfterThrowing(value = "pt()",throwing = "t")
public void afterThrowing(JoinPoint jp, Throwable t) {
    Object[] args = jp.getArgs();
    ……
}
```

### @Around

| 名称 | @Around                                                      |
| ---- | ------------------------------------------------------------ |
| 类型 | 方法注解                                                     |
| 位置 | 通知方法定义上方                                             |
| 作用 | 设置当前通知方法与切入点之间的绑定关系，当前通知方法在原始切入点方法前后运行 |
| 参数 | 传入参数：ProceedingJoinPoint，返回值：proceed函数返回，异常：抛出或处理 |
```java
@Around("pt()")
public Object around(ProceedingJoinPoint pjp) throws Throwable{//也可使用try……catch……处理异常
    Object[] args = pjp.getArgs();
	……//可修改传入的参数args
    Object ret = pjp.proceed(args);//执行原方法
    ……//可修改返回值ret
    return ret;
}
```

## AOP事务

Spring事务是基于AOP实现的，AOP是基于动态代理实现的

- 事务作用：在数据层保障一系列的数据库操作同成功同失败
- Spring事务作用：在数据层或**业务层**保障一系列的数据库操作同成功同失败（失败进行回滚）

数据层有事务我们可以理解，为什么业务层也需要处理事务呢?

举个简单的例子，

* 转账业务会有两次数据层的调用，一次是加钱一次是减钱
* 把事务放在数据层，加钱和减钱就有两个事务
* 没办法保证加钱和减钱同时成功或者同时失败
* 这个时候就需要将事务放在业务层进行处理。

为了解决这个问题必须要了解事务的角色

### 事务的角色

![1630249111055](assets/1630249111055.png)

- 事务管理员：发起事务方，在Spring中通常指代业务层开启事务的方法
- 事务协调员：加入事务方，在Spring中通常指代数据层方法，也可以是业务层方法

#### 事务传播

可使用propagation设置事务的传播行为

```java
@Service
public class LogServiceImpl implements LogService {

    @Autowired
    private LogDao logDao;
	//propagation设置事务属性：传播行为设置为当前操作需要新事务
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void log(String out,String in,Double money ) {
        logDao.log("转账操作由"+out+"到"+in+",金额："+money);
    }
}
```

![1630254257628](assets/1630254257628.png)

### 事务使用步骤

1、使用@Transactional标记事务

#### @Transactional   

| 名称 | @Transactional                                               |
| ---- | ------------------------------------------------------------ |
| 类型 | 接口注解  类注解  方法注解                                   |
| 位置 | 业务层接口上方  业务层实现类上方  业务方法上方               |
| 作用 | 为当前业务层方法添加事务（如果设置在类或接口上方则类或接口中所有方法均添加事务） |

**事务属性**

* readOnly：true只读事务，false读写事务，增删改要设为false,查询设为true。

* timeout:设置超时时间单位秒，在多长时间之内事务没有提交成功就自动回滚，-1表示不设置超时时间。

* rollbackFor:当出现指定异常进行事务回滚（**默认只有Error和RuntimeException 会进行事务回滚**）

* noRollbackFor:当出现指定异常不进行事务回滚

* rollbackForClassName等同于rollbackFor,只不过属性为异常的类全名字符串

* noRollbackForClassName等同于noRollbackFor，只不过属性为异常的类全名字符串

* isolation设置事务的隔离级别

* propagation设置事务的传播行为（事务管理员：发起事务要求其他事务响应；事务协调员：对事务管理员的请求进行相应响应的事务）

Note：Transactional可以写在接口类上、接口方法上、实现类上和实现类方法上

* 写在接口类上，该接口的所有实现类的所有方法都会有事务
* 写在接口方法上，该接口的所有实现类的该方法都会有事务
* 写在实现类上，该类中的所有方法都会有事务
* 写在实现类方法上，该方法上有事务
* **建议写在实现类或实现类的方法上**

2、在JdbcConfig类中配置事务管理器

```java
@Bean
public PlatformTransactionManager transactionManager(DataSource dataSource){
    DataSourceTransactionManager transactionManager = new DataSourceTransactionManager();
    transactionManager.setDataSource(dataSource);
    return transactionManager;
}
```
3、SpringConfig的配置类中使用EnableTransactionManagement开启事务允许

#### @EnableTransactionManagement

| 名称 | @EnableTransactionManagement           |
| ---- | -------------------------------------- |
| 类型 | 配置类注解                             |
| 位置 | 配置类定义上方                         |
| 作用 | 设置当前Spring环境中开启注解式事务支持 |

