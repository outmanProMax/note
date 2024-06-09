[CRUD 接口 | MyBatis-Plus (baomidou.com)](https://baomidou.com/pages/49cc81/#save)

# 初始使用步骤

## pom.xml补全依赖

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.4.1</version>
</dependency>
```

## Dao接口继承BaseMapper<>

```java
@Mapper
public interface UserDao extends BaseMapper<User>{
}
```

# CRUD

## 新增

```java
int insert (T t)
```

* T:泛型，新增用来保存新增数据

* int:返回值，新增成功后返回1，没有新增成功返回的是0

例子：

```java
@SpringBootTest
class Mybatisplus01QuickstartApplicationTests {

    @Autowired
    private UserDao userDao;

    @Test
    void testUpdate() {
        User user = new User();
        user.setId(1L);
        user.setName("Tom888");
        user.setPassword("tom888");
        userDao.updateById(user);
    }
}
```

## 删除

### 单ID删除

```java
int deleteById (Serializable id)
```

### 多ID删除

```java
int deleteBatchIds(@Param(Constants.COLLECTION) Collection<? extends Serializable> idList);
```

## 修改

```java
int updateById(T t);
```

- T:泛型，需要修改的数据内容，注意因为是根据ID进行修改，所以传入的对象中需要有ID属性值
- int:返回值，修改成功后返回1，未修改数据返回0

## 查询

![1631020283701](E:\我的笔记\assets\1631020283701.png)

### 根据ID查询

#### 单ID查询

在进行根据ID查询之前，我们可以分析下根据ID查询的方法:

```java
T selectById (Serializable id)
```

- Serializable：参数类型,主键ID的值
- T:根据ID查询只会返回一条数据

#### 多ID查询

```java
List<T> selectBatchIds(@Param(Constants.COLLECTION) Collection<? extends Serializable> idList);
```

### 根据queryWrapper查询

* selectList：查询结果为多个或者单个

* selectOne:查询结果为单个

#### 查询结果为多个

在进行查询所有之前，我们可以分析下查询所有的方法:

```java
List<T> selectList(Wrapper<T> queryWrapper)
```

- Wrapper：用来构建条件查询的条件，目前我们没有可直接传为Null
- List<T>:因为查询的是所有，所以返回的数据是一个集合

#### 查询结果为单个

在进行查询所有之前，我们可以分析下查询所有的方法:

```java
T selectList(Wrapper<T> queryWrapper)
```

- Wrapper：用来构建条件查询的条件，目前我们没有可直接传为Null
- T:返回的数据是为指定类型数据

#### 查询结果进行分页

基础的增删改查就已经学习完了，刚才我们在分析基础开发的时候，有一个分页功能还没有实现，在MP中如何实现分页功能，就是咱们接下来要学习的内容。

分页查询使用的方法是:

```java
IPage<T> selectPage(IPage<T> page, Wrapper<T> queryWrapper)
```

- IPage:用来构？建分页查询条件
- Wrapper：用来构建条件查询的条件，目前我们没有可直接传为Null
- IPage:返回值，你会发现构建分页条件和方法的返回值都是IPage

内含：records：为分页查询的具体数据，是一个List

pages：

current：

size：

total：

例子：

##### 步骤1:调用方法传入参数获取返回值

```java
@SpringBootTest
class Mybatisplus01QuickstartApplicationTests {

    @Autowired
    private UserDao userDao;
    
    //分页查询
    @Test
    void testSelectPage(){
        //1 创建IPage分页对象,设置分页参数,1为当前页码，3为每页显示的记录数
        IPage<User> page=new Page<>(1,3);
        //2 执行分页查询
        userDao.selectPage(page,null);
        //3 获取分页结果
        System.out.println("当前页码值："+page.getCurrent());
        System.out.println("每页显示数："+page.getSize());
        System.out.println("一共多少页："+page.getPages());
        System.out.println("一共多少条数据："+page.getTotal());
        System.out.println("数据："+page.getRecords());
    }
}
```

##### 步骤2:设置分页拦截器

这个拦截器MP已经为我们提供好了，我们只需要将其配置成Spring管理的bean对象即可。

```java
@Configuration
public class MybatisPlusConfig {
    
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor(){
        //1 创建MybatisPlusInterceptor拦截器对象
        MybatisPlusInterceptor mpInterceptor=new MybatisPlusInterceptor();
        //2 添加分页拦截器
        mpInterceptor.addInnerInterceptor(new PaginationInnerInterceptor());
        return mpInterceptor;
    }
}
```

# DQL编程控制

## 条件查询

以下方法等价于select * from table where age<18;

- 方法一：QueryWrapper(age为数据库中变量的名称)

```java
QueryWrapper<User> qw = new QueryWrapper();
qw.lt("age",18);
Employee emp = employeeService.getOne(qw);
```

- 方法二：QueryWrapper的基础上使用lambda

```java
QueryWrapper<User> qw = new QueryWrapper<User>();
qw.lambda().lt(User::getAge, 10);
Employee emp = employeeService.getOne(qw);
```

- 方法三：LambdaQueryWrapper

```java
LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<User>();
lqw.lt(User::getAge, 10);
Employee emp = employeeService.getOne(qw);
```

### 或查询

```
lqw.lt(User::getAge, 10).or().gt(User::getAge, 30);
```

### 与查询

    lqw.gt(User::getAge, 10).and(e -> {e.lt(User::getAge, 30)});
### null判定

```
lqw.lt(uq.getAge2()!=null,其他条件);//lqw.gt同理
```

## 多返回结果查询

### 查询指定字段

```java
@Test
void testGetAll(){
    LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<User>();
    lqw.select(User::getId,User::getName,User::getAge);
    List<User> userList = userDao.selectList(lqw);
    System.out.println(userList);
}
```

### 聚合查询

```java
@Test
void testGetAll(){
    QueryWrapper<User> lqw = new QueryWrapper<User>();
    lqw.select("avg(age) as avgAge");
    List<Map<String, Object>> userList = userDao.selectMaps(lqw);
    System.out.println(userList);
}
```

### 分组查询

```java
void testGetAll(){
    QueryWrapper<User> lqw = new QueryWrapper<User>();
    lqw.select("count(*) as count,tel");
    lqw.groupBy("tel");
    List<Map<String, Object>> list = userDao.selectMaps(lqw);
    System.out.println(list);
}
```

## 查询条件

### 等值查询

```
lqw.eq(User::getName, "Jerry").
```

### 范围查询

```java
lqw.between(User::getAge, 10, 30);
```

* gt():大于(>)
* ge():大于等于(>=)
* lt():小于(<)
* lte():小于等于(<=)
* between():between ? and ?

### 模糊查询

```java
lqw.likeLeft(User::getName, "J");
```

* like():前后加百分号,如 %J%
* likeLeft():前面加百分号,如 %J
* likeRight():后面加百分号,如 J%

### 排序查询

```java
lqw.orderBy(true,false, User::getId);
```

## 映射匹配兼容性

### 数据库字段名与Dao字段名不一致@TableField

| 名称     | @TableField                                                  |
| -------- | ------------------------------------------------------------ |
| 类型     | **属性注解**                                                 |
| 位置     | 模型类属性定义上方                                           |
| 作用     | 设置当前属性对应的数据库表中的字段关系                       |
| 相关属性 | value(默认)：设置数据库表字段名称<br/>exist:设置属性在数据库表字段中是否存在，默认为true，此属性不能与value合并使用<br/>select:设置属性是否参与查询，此属性与select()映射配置不冲突 |

#### 数据库表字段中是否存在exist

设置属性在数据库表字段中是否存在，默认为true，此属性不能与value合并使用

#### 属性是否参与查询select

设置属性是否参与查询，此属性与select()映射配置不冲突

### 数据库表名与Dao类名不一致@TableName

| 名称     | @TableName                    |
| -------- | ----------------------------- |
| 类型     | **类注解**                    |
| 位置     | 模型类定义上方                |
| 作用     | 设置当前类对应于数据库表关系  |
| 相关属性 | value(默认)：设置数据库表名称 |

#### 统一进行配置TableName

配置起来还是比较繁琐，简化方式为在application.yml配置文件中配置如下内容:

```yml
mybatis-plus:
  global-config:
    db-config:
    	table-prefix: tbl_
```

### 满足条件时，才返回当前字段@JsonInclude

```
@JsonInclude(JsonInclude.Include.*NON_EMPTY*)
private List<CategoryEntity> children;
```

# DML编程控制

## id生成策略控制@TableId

| 名称     | @TableId                                                     |
| -------- | ------------------------------------------------------------ |
| 类型     | **属性注解**                                                 |
| 位置     | 模型类中用于表示主键的属性定义上方                           |
| 作用     | 设置当前类中主键属性的生成策略                               |
| 相关属性 | value(默认)：设置数据库表主键名称<br/>type:设置主键属性的生成策略，值查照IdType的枚举值 |

* NONE/INPUT: 不设置id生成策略，MP不自动生成，约等于INPUT,所以这两种方式都需要用户手动设置，但是手动设置第一个问题是容易出现相同的ID造成主键冲突，为了保证主键不冲突就需要做很多判定，实现起来比较复杂
* AUTO:数据库ID自增,这种策略适合在数据库服务器只有1台的情况下使用,不可作为分布式ID使用
* ASSIGN_UUID:可以在分布式的情况下使用，而且能够保证唯一，但是生成的主键是32位的字符串，长度过长占用空间而且还不能排序，查询性能也慢
* ASSIGN_ID:使用雪花算法生产id，可以在分布式的情况下使用，生成的是Long类型的数字，可以排序性能也高，但是生成的策略和服务器时间有关，如果修改了系统时间就有可能导致出现重复主键

## 逻辑删除@TableLogic

逻辑删除即将某条记录标记为不可用

- 修改数据库表添加`deleted`列

- 实体类添加属性`deleted`

| 名称     | @TableLogic                               |
| -------- | ----------------------------------------- |
| 类型     | **属性注解**                              |
| 位置     | 模型类中用于表示删除字段的属性定义上方    |
| 作用     | 标识该字段为进行逻辑删除的字段            |
| 相关属性 | value：逻辑未删除值<br/>delval:逻辑删除值 |

```java
@Data
public class User {
    ……
    @TableLogic(value="0",delval="1")
    //value为正常数据的值，delval为删除数据的值
    private Integer deleted;
}
```

### 统一进行配置TableLogic

```
mybatis-plus:
  global-config:
    db-config:
      # 逻辑删除字段名
      logic-delete-field: deleted
      # 逻辑删除字面值：未删除为0
      logic-not-delete-value: 0
      # 逻辑删除字面值：删除为1
      logic-delete-value: 1
```

## 数据校验注解@Valid、@Validated

1）、给Entity添加校验注解，并定义自己的message提示

```java
	@NotNull(message = "修改必须指定品牌id",groups = {UpdateGroup.class})
	@Null(message = "新增不能指定id",groups = {AddGroup.class})	
	@TableId
	private Long brandId;
	/**
	 * 品牌名
	 */
	@NotBlank(message = "品牌名必须提交")
	private String name;
	/**
	 * 品牌logo地址
	 */
	@NotEmpty
	@URL(message = "logo必须是一个合法的url地址")
	private String logo;
	/**
	 * 检索首字母
	 */
	@NotEmpty
	@Pattern(regexp = "/^[a-zA-Z]$/", message = "检索首字母必须是一个字母")
	private String firstLetter;
	/**
	 * 排序
	 */
	@NotNull
	@Min(value = 0, message = "排序必须大于等于0")
	private Integer sort;
```

2)   、开启校验功能@Valid
   效果：校验错误以后会有默认的响应；

```java
    //出现错误时由统一异常类处理
    @RequestMapping("/save")
    public R save(@Valid @RequestBody BrandEntity brand){
		brandService.save(brand);

        return R.ok();
    }
```

3）、分组校验（多场景的复杂校验）
      1)   、	给校验注解标注什么情况需要进行校验

```
@NotBlank(message = "品牌名必须提交",groups = {AddGroup.class,UpdateGroup.class})
```


​      2）、   选择校验分组

```
@Validated({AddGroup.class})
```

### 自定义校验注释

1）、编写一个自定义的校验注解

```java
@Documented
@Constraint(validatedBy = { ListValueConstraintValidator.class })
@Target({ *METHOD*, *FIELD*, *ANNOTATION_TYPE*, *CONSTRUCTOR*, *PARAMETER*, *TYPE_USE* })
@Retention(*RUNTIME*)
public @interface ListValue {
    String message() default "{com.atguigu.common.valid.ListValue.message}";

    Class<?>[] groups() default { };

    Class<? extends Payload>[] payload() default { };

    int[] vals() default { };
}
```

2）、编写一个自定义的校验器 *ConstraintValidator*

```java
public class ListValueConstraintValidator implements ConstraintValidator<ListValue,Integer> {

    private Set<Integer> set = new HashSet<>();
    //初始化方法
    @Override
    public void initialize(ListValue constraintAnnotation) {

        int[] vals = constraintAnnotation.vals();
        for (int val : vals) {
            set.add(val);
        }
    }

    //判断是否校验成功
    @Override
    public boolean isValid(Integer value, ConstraintValidatorContext context) {

        return set.contains(value);
    }
}
```

## 统一异常处理

针对于错误状态码，是我们进行随意定义的，然而正规开发过程中，错误状态码有着严格的定义规则，如该在项目中我们的错误状态码定义


为了定义这些错误状态码，我们可以单独定义一个常量类，用来存储这些错误状态码。

- 在common中新建BizCodeEnume用来存储状态码

```java
/***
 * 错误码和错误信息定义类
 * 1. 错误码定义规则为5为数字
 * 2. 前两位表示业务场景，最后三位表示错误码。例如：100001。10:通用 001:系统未知异常
 * 3. 维护错误码后需要维护错误描述，将他们定义为枚举形式
 * 错误码列表：
 *  10: 通用
 *      001：参数格式校验
 *  11: 商品
 *  12: 订单
 *  13: 购物车
 *  14: 物流
 */
public enum BizCodeEnum {

    UNKNOW_EXEPTION(10000,"系统未知异常"),

    VALID_EXCEPTION( 10001,"参数格式校验失败");

    private int code;
    private String msg;
    BizCodeEnum(int code, String msg) {
        this.code = code;
        this.msg = msg;
    }

    public int getCode() {
        return code;
    }

    public String getMsg() {
        return msg;
    }
}
```

- 在product里面新建类`GulimallExceptionControllerAdvice`，用来集中处理所有异常

```java
@RestControllerAdvice(basePackages = "com.xmh.gulimall.product.controller")
public class GulimallExceptionControllerAdvice {

    // 处理数据校验异常
    @ExceptionHandler(value = MethodArgumentNotValidException.class)
    public R handleVaildException(MethodArgumentNotValidException e){
        BindingResult bindingResult = e.getBindingResult();
        Map<String, String> errorMap = new HashMap<>();
        bindingResult.getFieldErrors().forEach((fieldError)->{
            errorMap.put(fieldError.getField(), fieldError.getDefaultMessage());
        });

        return R.error(BizCodeEnume.VALID_EXCEPTION.getCode(),BizCodeEnume.VALID_EXCEPTION.getMsg()).put("data", errorMap);
    }

    //处理全局异常
    @ExceptionHandler(value = Throwable.class)
    public R handleException(Throwable throwable){
        return R.error(BizCodeEnume.UNKNOW_EXEPTION.getCode(), BizCodeEnume.UNKNOW_EXEPTION.getMsg());
    }

}
```



## 乐观锁

- 数据库表添加列`version`

- 在模型类中添加对应的属性

```java
@Data
//@TableName("tbl_user") 可以不写是因为配置了全局配置
public class User {
    ……
    @Version
    private Integer version;
}
```

- 添加乐观锁的拦截器

```java
@Configuration
public class MpConfig {
    @Bean
    public MybatisPlusInterceptor mpInterceptor() {
        //1.定义Mp拦截器
        MybatisPlusInterceptor mpInterceptor = new MybatisPlusInterceptor();
        //2.添加乐观锁拦截器
        mpInterceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
        return mpInterceptor;
    }
}
```

# 代码生成器

```java
public class CodeGenerator {
    public static void main(String[] args) {
        //1.获取代码生成器的对象
        AutoGenerator autoGenerator = new AutoGenerator();

        //设置数据库相关配置
        DataSourceConfig dataSource = new DataSourceConfig();
        dataSource.setDriverName("com.mysql.cj.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost:3306/mybatisplus_db?serverTimezone=UTC");
        dataSource.setUsername("root");
        dataSource.setPassword("abc123");
        autoGenerator.setDataSource(dataSource);

        //设置全局配置
        GlobalConfig globalConfig = new GlobalConfig();
        globalConfig.setOutputDir(System.getProperty("user.dir")+"/mybatisgenerator/src/main/java");    //设置代码生成位置
        globalConfig.setOpen(false);    //设置生成完毕后是否打开生成代码所在的目录
        globalConfig.setAuthor("黑马程序员");    //设置作者
        globalConfig.setFileOverride(true);     //设置是否覆盖原始生成的文件
        globalConfig.setMapperName("%sDao");    //设置数据层接口名，%s为占位符，指代模块名称
        globalConfig.setIdType(IdType.ASSIGN_ID);   //设置Id生成策略
        autoGenerator.setGlobalConfig(globalConfig);

        //设置包名相关配置
        PackageConfig packageInfo = new PackageConfig();
        packageInfo.setParent("com.itheima");   //设置生成的包名，与代码所在位置不冲突，二者叠加组成完整路径
        packageInfo.setEntity("domain");    //设置实体类包名
        packageInfo.setMapper("dao");   //设置数据层包名
        autoGenerator.setPackageInfo(packageInfo);

        //策略设置
        StrategyConfig strategyConfig = new StrategyConfig();
        strategyConfig.setInclude("tbl_user");  //设置当前参与生成的表名，参数为可变参数
        strategyConfig.setTablePrefix("tbl_");  //设置数据库表的前缀名称，模块名 = 数据库表名 - 前缀名  例如： User = tbl_user - tbl_
        strategyConfig.setRestControllerStyle(true);    //设置是否启用Rest风格
        strategyConfig.setVersionFieldName("version");  //设置乐观锁字段名
        strategyConfig.setLogicDeleteFieldName("deleted");  //设置逻辑删除字段名
        strategyConfig.setEntityLombokModel(true);  //设置是否启用lombok
        autoGenerator.setStrategy(strategyConfig);
        //2.执行生成操作
        autoGenerator.execute();
    }
}
```

# Lombok

**@Data**注解:包括下面所有方法除了有参构造方法

**@ToString**注解:tostring方法

**@Slf4j**注解:日志

**@EqualsAndHashCode**注解:equals和hashcode方法

**@NoArgsConstructor**:无参构造器

**@AllArgsConstructor**:全参构造器

**@Setter**:为Dao类的属性提供setter方法

**@Getter**:为Dao类的属性提供getter方法

# 热部署Devtools

**开发时使用，生产环境关闭**

**1.Adding devtools to your project**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <scope>runtime</scope>
    <optional>true</optional>
</dependency>
```

**2.Adding plugin to your pom.xml**

下段配置复制到聚合父类总工程的pom.xml

```xml
<build>
    <!--
	<finalName>你的工程名</finalName>（单一工程时添加）
    -->
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <fork>true</fork>
                <addResources>true</addResources>
            </configuration>
        </plugin>
    </plugins>
</build>
```

3.**Enabling automatic build**

File -> Settings(New Project Settings->Settings for New Projects) ->Complier

下面项勾选

- Automatically show first error in editor
- Display notification on build completion
- Build project automatically
- Compile independent modules in parallel

**4.Update the value of**

键入Ctrl + Shift + Alt + / ，打开Registry，勾选：

- compiler.automake.allow.when.app.running
- actionSystem.assertFocusAccessFromEdt

**5.重启IDEA**