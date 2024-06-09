# 配置文件

## application.yml

```yaml
mybatis:
  mapperLocations: classpath:mapper/*.xml		# 映射java中dao文件对应resource中的mapper文件地址
  type-aliases-package: com.frx01.springcloud.entities    # 所有Entity别名类所在包
```

## mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
```
  * <typeAliases>：为全类名起别名的父标签
    * <typeAlias>：为全类名起别名的子标签
      * type：指定全类名      
      * alias：指定别名
      
    * <package>：为指定包下所有类起别名的子标签，别名就是类名，首字母小写
```java
    <typeAliases>
        <package name="com.itheima.pojo"/>
    </typeAliases>
```

 * <environments>：配置数据库环境标签，default 属性指定哪个 environment
  * <environment>：配置数据库环境子标签，id 属性是唯一标识，与 default 对应
  * <transactionManager>：事务管理标签，type 属性默认 JDBC 事务
  * <dataSoure>：数据源标签
    * type 属性：POOLED 使用连接池（MyBatis 内置），UNPOOLED 不使用连接池
  * <property>：数据库连接信息标签。
    * name 属性取值：driver，url，username，password
    * value 属性取值：与 name 对应

```
    <!--
    environments：配置数据库连接环境信息。可以配置多个environment，通过default属性切换不同的environment
    -->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <!--数据库连接信息-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql:///mybatis?useSSL=false"/>
                <property name="username" value="root"/>
                <property name="password" value="1234"/>
            </dataSource>
        </environment>

        <environment id="test">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <!--数据库连接信息-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql:///mybatis?useSSL=false"/>
                <property name="username" value="root"/>
                <property name="password" value="1234"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
```

  * <mappers>：引入映射配置文件标签
  * <mapper>：引入映射配置文件子标签
    * resource：属性指定映射配置文件的名称
    * url：引用网路路径或者磁盘路径下的 sql 映射文件
    * class：指定映射配置类
  * <package>：批量注册

```
       <!--加载sql映射文件-->
       <mapper resource="UserMapper.xml"/>
    </mappers>
</configuration>
```

# SqlSessionFactory和SqlSession

```java
//2.1 获取SqlSessionFactory对象
String resource = "mybatis-config.xml";
InputStream inputStream = Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
//2.2 获取SqlSession对象
SqlSession sqlSession = sqlSessionFactory.openSession();
```

# SQL语句(以select为例，update、delete、insert同理)

## 参数占位符

* #{} ：执行SQL时，会将 #{} 占位符替换为？，将来自动设置参数值。从上述例子可以看出使用#{} 底层使用的是 `PreparedStatement`
* ${} ：拼接SQL。底层使用的是 `Statement`，会存在SQL注入问题。

### parameterType使用（可省略，代表参数占位符的类型）

示例：

```xml
<select id="selectById" parameterType="int" resultMap="brandResultMap">
    select *
    from tb_brand where id = ${id};
</select>
```

## 特殊字段处理

### 转义字符

如 使用`&lt;` 代替 `<` 的

### \<![CDATA[内容]]>

如`\<![CDATA[<]]>`代替`<`

## Mysql和java变量名一致化

### 起别名(as)解决上述问题

 brand_name as brandName

### resultMap（最好自定义类的返回类型规定都用这个）

在映射配置文件中使用resultMap定义 字段 和 属性 的映射关系

```xml
<resultMap id="brandResultMap" type="brand">
    <!--
            id：完成主键字段的映射
                column：表的列名
                property：实体类的属性名
            result：完成一般字段的映射
                column：表的列名
                property：实体类的属性名
        -->
    <result column="brand_name" property="brandName"/>
    <result column="company_name" property="companyName"/>
</resultMap>
```

> 注意：在上面只需要定义 字段名 和 属性名 不一样的映射，而一样的则不需要专门定义出来。

SQL语句正常编写

```xml
<select id="selectAll" resultMap="brandResultMap">
    select *
    from tb_brand;
</select>
```

#### 一对多进行关联查询（即内连接和外连接）

在resultMap中，除了映射主表属性，还要通过collection标签映射子表属性，该标签需包含如下内容：

- 通过property属性指定主表类中子表变量对应名；
- 通过ofType属性指定子表的全类名；
- 通过result子标签定义子表字段和属性的映射关系。

具体步骤如下：

1、修改Users实体类，添加子表变量并添加被嵌套的Orders类

```mysql
public Users{
	Long userid;
	String username;
	String usersex;
	private List<Orders> ordersList;//添加嵌套的类
}

public Orders{
	Long orderid;
	Long orderprice;
}
```

2、修改UsersMapper.xml

```mysql
<resultMap id="usersAndOrdersMap" type="com.dyh.pojo.Users">
		<id property="userid" column="userid" />
		<result property="username" column="username" />
		<result property="usersex" column="usersex" />
		<!-- 配置一对多的关联关系 -->
		<collection property="ordersList" ofType="com.dyh.pojo.Orders" >
			<id property="orderid" column="orderid" />
			<result property="orderprice" column="orderprice" />
		</collection>
	</resultMap>
<select id="queryUsersAndOrders" resultMap="usersAndOrdersMap">
	SELECT u.userid,u.username,u.usersex,
		o.orderid,o.orderprice
	FROM users u,orders o
	WHERE u.userid=o.user_id
</select>
```
## 参数传递

### 单个参数

* POJO 类型

  直接使用。要求 `属性名` 和 `参数占位符名称` 一致

* Map 集合类型

  直接使用。要求 `map集合的键名` 和 `参数占位符名称` 一致

  ```java
  void select(@(Param "map1")Map map);//在此之前执行map.put(key1,value1);
  ```

  ```mysql
  <foreach collection="map1.key1" item="id" separator="," open="(" close=")">
          #{id}
  </foreach>
  ```

* Collection 集合类型

  Mybatis 会将集合封装到 map 集合中，每个键都可以获取到collection，如下：

  > map.put("arg0"，collection集合);
  >
  > map.put("collection"，collection集合;

  **可以使用 `@Param` 注解替换map集合中默认的 arg 键名。**
  
  ```java
  void select(Conllection ids);
  ```

  ```mysql
  <foreach collection="collection" item="id" separator="," open="(" close=")">
          #{id}
  </foreach>
  ```

* List 集合类型

  Mybatis 会将集合封装到 map 集合中，每个键都可以获取到list，如下：

  > map.put("arg0"，list集合);
  >
  > map.put("collection"，list集合);
  >
  > map.put("list"，list集合);

  **可以使用 `@Param` 注解替换map集合中默认的 arg 键名。**

  ```java
  void select(List ids);
  ```
  
  ```mysql
  <foreach collection="list " item="id" separator="," open="(" close=")">
          #{id}
  </foreach>
  ```

* Array 类型

  Mybatis 会将集合封装到 map 集合中，每个键都可以获取到数组，如下：

  > map.put("arg0"，数组);
  >
  > map.put("array"，数组);

  **可以使用 `@Param` 注解替换map集合中默认的 arg 键名。**


  ```java
  void select(int[] ids);
  ```

  ```mysql
  <foreach collection="array " item="id" separator="," open="(" close=")">
          #{id}
  </foreach>
  ```

* 其他类型

  比如int类型，`参数占位符名称` 叫什么都可以。尽量做到见名知意

### 多个参数（@Param、对象、map集合）

* 使用 `@Param("参数名称")` 标记每一个参数，在映射配置文件中就需要使用 `#{参数名称}` 进行占位

  ```java
  List<Brand> selectByCondition(@Param("status") int status, @Param("companyName") String companyName,@Param("brandName") String brandName);
  ```

* 将多个参数封装成一个 实体对象 ，将该实体对象作为接口的方法参数。该方式要求在映射配置文件的SQL中使用 `#{内容}` 时，里面的内容必须和实体类属性名保持一致。

  ```java
  List<Brand> selectByCondition(Brand brand);
  ```

* 将多个参数封装到map集合中，将map集合作为接口的方法参数。该方式要求在映射配置文件的SQL中使用 `#{内容}` 时，里面的内容必须和map集合中键的名称一致。

  ```
  List<Brand> selectByCondition(Map map);
  ```

## 动态SQL(if,choose,trim, foreach)

1. if

2. choose (when, otherwise)

```
<choose><!--相当于switch-->
            <when test="status != null"><!--相当于case-->
                status = #{status}
            </when>
            <when test="companyName != null and companyName != '' "><!--相当于case-->
                company_name like #{companyName}
            </when>
            <when test="brandName != null and brandName != ''"><!--相当于case-->
                brand_name like #{brandName}
            </when>
            <otherwise><!--相当于default-->
            	略
            </otherwise>
</choose>
```

3. trim (where, set)

\<where>\</where>代替sql的where，会动态的去掉第一个条件前的 and ，如果所有的参数没有值则不加where关键字

\<set>\</set>代替sql中的set，防止出现“，”结尾的语法错误，和set后接内容为空的语法错误

4. foreach

```mysql
<foreach collection="array" item="id" separator="," open="(" close=")">
        #{id}
</foreach>
```

* collection 属性：
  * mybatis会将数组参数，封装为一个Map集合。
    * 默认：array = 数组
    * 使用@Param注解改变map集合的默认key的名称
* item 属性：本次迭代获取到的元素。
* separator 属性：集合项迭代之间的分隔符。`foreach` 标签不会错误地添加多余的分隔符。也就是最后一次迭代不会加分隔符。
* open 属性：该属性值是在拼接SQL语句之前拼接的语句，只会拼接一次
* close 属性：该属性值是在拼接SQL语句拼接后拼接的语句，只会拼接一次

## insert标签

### useGeneratedKeys

是够获取自动增长的主键值。true表示获取

### keyProperty  

指定将获取到的主键值封装到哪儿个属性里

# Mybatis缓存

## MyBatis的一级缓存

一级缓存是SqlSession级别的，通过同一个SqlSession查询的数据会被缓存，下次查询相同的数据，就会从缓存中直接获取，不会从数据库重新访问

使一级缓存失效的四种情况：

1) 不同的SqlSession对应不同的一级缓存

2) 同一个SqlSession但是查询条件不同

3) 同一个SqlSession两次查询期间执行了任何一次增删改操作

4) 同一个SqlSession两次查询期间手动清空了缓存

## MyBatis的二级缓存

二级缓存是SqlSessionFactory级别，通过同一个SqlSessionFactory创建的SqlSession查询的结果会被

缓存；此后若再次执行相同的查询语句，结果就会从缓存中获取

二级缓存开启的条件：

a>在核心配置文件中，设置全局配置属性cacheEnabled="true"，默认为true，不需要设置

b>在映射文件中设置标签\<cache/>

c>二级缓存必须在SqlSession关闭或提交之后有效

d>查询的数据所转换的实体类类型必须实现序列化的接口

使二级缓存失效的情况：

两次查询之间执行了任意的增删改，会使一级和二级缓存同时失效

## 二级缓存的相关配置

在mapper配置文件中添加的cache标签可以设置一些属性：

①eviction属性：缓存回收策略，默认的是 LRU。

LRU（Least Recently Used） – 最近最少使用的：移除最长时间不被使用的对象。

FIFO（First in First out） – 先进先出：按对象进入缓存的顺序来移除它们。

SOFT – 软引用：移除基于垃圾回收器状态和软引用规则的对象。

WEAK – 弱引用：更积极地移除基于垃圾收集器状态和弱引用规则的对象。

②flushInterval属性：刷新间隔，单位毫秒

默认情况是不设置，也就是没有刷新间隔，缓存仅仅调用语句时刷新

③size属性：引用数目，正整数

代表缓存最多可以存储多少个对象，太大容易导致内存溢出

④readOnly属性：只读， true/false

true：只读缓存；会给所有调用者返回缓存对象的相同实例。因此这些对象不能被修改。这提供了 很重要的性能优势。

false：读写缓存；会返回缓存对象的拷贝（通过序列化）。这会慢一些，但是安全，因此默认是

false。

## MyBatis缓存查询的顺序

先查询二级缓存，因为二级缓存中可能会有其他程序已经查出来的数据，可以拿来直接使用。

如果二级缓存没有命中，再查询一级缓存

如果一级缓存也没有命中，则查询数据库

SqlSession关闭之后，一级缓存中的数据会写入二级缓存
