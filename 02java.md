<img src=" .\assets\">

# 二 基本结构

## 数据类型

boolean

## 变量

### 初始化
  java局部变量定义后，必须初始化，即赋值，才能使用（它没有默认值），而成员变量定义后可以不初始化，因为它有默认值。
### 声明 
  int i; 
### 常量 
  final

## 参数

NOTE：在参数传递的过程中,形参和实参完全是同一块内存空间,两者不分彼此（传递参数不会而额外占用内存）

### 可变参数
使用 Object... args ，它表明这个方法可以接收任意数量的对象（除 fint参数之外）。  

java的可变参数是指在调用方法时传入不定长度的参数，本质上是基于数组实现的。

## 输入输出

### 从屏幕输入

  Scanner in = new Scanner(System.in);
  s=in.nextLine();

### 向屏幕输出

  System.out.print("%d",i);

### 文件输入

  Scanner in = new Scanner(Paths.get("*.txt"),"UTF###8");

### 文件输出

  PrintWriter out = new PrintWriter(Paths.get("*.txt"),"UTF###8");

## 控制流程

### 带标签的break(必须放在循环之前)
  A：
  while()
  ……
  break A;

## 大数值

### 任意长度的整数
  BigInteger a = BigInteger.valueof(b);
  a=a.add(a);

### 任意长度的浮点数
  BigDecimal
### 数组排序
  int[] a = new int[100];
  ……
  Arrays.sort(a);

### 一/二维数组
  int[] num;int[\][] num;
## 正则表达式

Note：如果使用了&&或|或{次数}的话必须对涉及的数据加括号

- 正则表达式-字符类

1. \[abc\]：代表a或者b，或者c字符中的一个。

2. \[^abc\]：代表除a,b,c以外的任何字符。

3. [a-z]：代表a-z的所有小写字符中的一个。

4. [A-Z]：代表A-Z的所有大写字符中的一个。

5. [0-9]：代表0-9之间的某一个数字字符。

6. [a-zA-Z0-9]：代表a-z或者A-Z或者0-9之间的任意一个字符。

7. [a-d[m-p]]：a 到 d 或 m 到 p之间的任意一个字符。

- 正则表达式-逻辑运算符

1. &&：并且
2. |    ：或者
3. \  ：转义字符

- 正则表达式-预定义字符（使用s]时应用"\\\\“代替”\“表示转义，如\\\\d->\d）

1. "." ： 匹配任何单个字符，除了换行符。
2. "\d"：任何数字[0-9]的简写；
3. "\D"：任何非数字\[^0-9\]的简写；
4. "\s"： 空白字符：[ \t\n\x0B\f\r] 的简写
5. "\S"： 非空白字符：\[^\s\] 的简写
6. "\w"：单词字符：[a-zA-Z_0-9]的简写
7. "\W"：非单词字符：\[^\w\]
8. "/n"：换行符
9. "/t"：制表符

- 正则表达式-数量词(若无数量词限定，这可认为每段限定只针对当前字符)

1. X? : 0次或1次
2. X* : 0次到多次
3. X+ : 1次或多次
4. X{n} : 恰好n次
5. X{n,} : 至少n次
6. X{n,m}: n到m次(n和m都是包含的)

- 正则表达式-位置符

1. \b：匹配单词的结尾或开头
2. ^：匹配字符串开通
3. $：匹配字符串结尾

- 正则表达式-分组括号( )
如何识别组号？
答：只看左括号，不看右括号，按照左括号的顺序，从左往右，依次为第一组，第二组，第三组等等
如何使用组号？
答：\\组号:表示把第X组的内容再出来用一次
举例: a123a b456b为"(.).+\\1"
- 示例
```java
        String str = "192.168.0.1";
        String pattern = "((25[0-5]|2[0-4]\\d|[0-1]\\d{2}|[1-9]?\\d)\\.){3}(25[0-5]|2[0-4]\\d|[0-1]\\d{2}|[1-9]?\\d)";
        Pattern.matches(pattern, str);
```



# 三 预定义类\包

## 常用的包

    一.java.lang包。该包提供了Java语言进行程序设计的基础类，它是默认导入的包。该包里面的Runnable接口和Object、Math、String、StringBuffer、System、Thread以及Throwable类需要重点掌握，因为它们应用很广。
    二：java.util包。该包提供了包含集合框架、遗留的集合类、事件模型、日期和时间实施、国际化和各种实用工具类（字符串标记生成器、随机数生成器和位数组）。
    三：java.io包。该包通过文件系统、数据流和序列化提供系统的输入与输出。
    四：java.net包。该包提供实现网络应用与开发的类。
    五：java.sql包。该包提供了使用Java语言访问并处理存储在数据源（通常是一个关系型数据库）中的数据API。
## Object： 所有类的超类

在 C++ 中没有所有类的根类，不过，每个指针都可以转换成 void* 指针。

### 相等比较

#### ==号比较

- 比较基本数据类型：比较的是具体的值
- 比较引用数据类型（包括String和包装类等）：比较的是对象地址值

#### equals比较

- 语法

```
public boolean equals(Object o)
```

- 参数

**o** -- 任何对象。

- 返回值

如 Number 对象不为 Null，且与方法的参数类型与数值都相等返回 True，否则返回 False。

#### compareTo比较

- 语法

```
public int compareTo( NumberSubClass referenceName )
```

- 参数

**referenceName** -- 可以是一个 Byte, Double, Integer, Float, Long 或 Short 类型的参数。

- 返回值

如果指定的数与参数相等返回 0。

如果指定的数小于参数返回 -1。

如果指定的数大于参数返回 1。

### clone方法

**必须实现Cloneable接口，否则会抛出CloneNotSupportedException(克隆不被支持)的异常**

protected Object clone()    			//对象克隆

java的clone类似于拷贝构造，同样也是默认浅克隆

### hashCode方法

  散列码（ hash code ) 是由对象导出的一个整型值。

### toString方法

public String toString()				//返回该对象的字符串表示形式(可以看做是对象的内存地址值)

### finalize 方法

  类似与析构但不是析构

## 包装类

- 基本类型

| 基本类型 | 对应的包装类（位于java.lang包中） |
| -------- | --------------------------------- |
| byte     | Byte                              |
| short    | Short                             |
| int      | **Integer**                       |
| long     | Long                              |
| float    | Float                             |
| double   | Double                            |
| char     | **Character**                     |
| boolean  | Boolean                           |

- 基本方法

| 方法名                                  | 说明                                   |
| --------------------------------------- | -------------------------------------- |
| public Integer(int   value)             | 根据 int 值创建 Integer 对象(过时)     |
| public Integer(String s)                | 根据 String 值创建 Integer 对象(过时)  |
| public static Integer valueOf(int i)    | 返回表示指定的 int 值的 Integer   实例 |
| public static Integer valueOf(String s) | 返回保存指定String值的 Integer 对象    |
| static string tobinarystring(int i)     | 得到二进制                             |
| static string tooctalstring(int i)      | 得到八进制                             |
| static string toHexstring(int i)        | 得到十六进制                           |
| static int parseInt(string s)           | 将字符串类型的整数转成int类型的整数    |

### 自动装箱

1、有时， 需要将 int 这样的基本类型转换为对象。 所有的基本类型都冇一个与之对应的类。
2、自动装箱：添加 int 类型的元素到 ArrayList<lnteger>中会进行自动地装箱,
如list.add(3)将自动地变换成list.add (Integer.value0f(3));

### 自动拆箱

1、相反地， 当将一个 Integer 对象赋给一个 int 值时， 将会自动地拆箱，
如int n = list.get(i);翻译成int n = list.get(i).intValue();

2、注意，如果你在使用包装类时没有通过显式、或是通过自动装箱机制为其赋值，在你取出值、或是通过自动拆箱使用该值的时候，就可能会发生 `NullPointerException`，这个是大家要注意的。

## 字符串类

### 构建字符串

| 方法名                      | 说明                                      |
| --------------------------- | ----------------------------------------- |
| public   String()           | 创建一个空白字符串对象，不含有任何内容    |
| public   String(char[] chs) | 根据字符数组的内容，来创建字符串对象      |
| public   String(byte[] bys) | 根据字节数组的内容，来创建字符串对象      |
| String s =   “abc”;         | 直接赋值的方式创建字符串对象，内容就是abc |

### 长度

s.length()

### 查找

char charAt(int index);//String不可以使用[]索引

int indexOf(String str);//此方法为返回str第一次出现的位置

### 字符串拆分/截取

public String[] split(String regex)//参数regex表示正则表达式。可以将当前字符串中匹配regex正则表达式的符号作为"分隔符"来切割字符串

s.substring(a)//[a,s.length()-1] 间的子串

s.substring(a,b)//[a,b)间的子串

### 字符串替换

public String replaceAll(String regex,String newStr)
//参数regex表示一个正则表达式。可以将当前字符串中匹配regex正则表达式的字符串替换为newStr。

public String replaceFirst(String regex,String newStr)

### 字符串转数组/基本类型

- String转换为数组/基本类型

字节数组：byte[] b = "黑马程序员".getBytes();

字符数组：char[] chars = "黑马程序员".toCharArray();
除了Character类之外，其他所有包装类都具有parseXxx静态方法可以将字符串参数转换为对应的基本类型：
public static byte parseByte(String s)：将字符串参数转换为对应的byte基本类型。

public static short parseShort(String s)：将字符串参数转换为对应的short基本类型。

public static int parseInt(String s)：将字符串参数转换为对应的int基本类型。
……

- 基本类型转换为String

方式一：直接在数字后加一个空字符串

方式二：通过String类静态方法valueOf()

- char[]转String

  String s = new String(ch);

### 其他

- 大小写转换

str.toLowerCase();
str.toUpperCase();

- 去空格

str.trim();

- 拼接

str1.join(str2);

### StringBuilder （线程不安全）

StringBuilder 可以看成是一个容器，创建之后里面的内容是可变的。

  StringBuilder b=new StringBuilder();

- 2.增 StringBuffer append(…)
- 3.删 StringBuffer delete(int start,int end)
- 4.改 StringBuffer replace(int start,int end,String str)]
- 5.插入 StringBuffer insert(int offset, · · · )
- 6.反转 StringBuffer reverse()

  String s=b.toString();

### StringBuffer(线程安全)

 与StringBuilder类似，不同在于 StringBuffer的方法是线程安全的（能同步访问）， 但是速度较慢

## Arrays类

```java
static void sort(baseType[] a, Comparator<? super T> c)
static void fill(baseType[] a, baseType val)
static List<T> asList(baseType[] a)//该方法适用于对象型数据的数组（String、Integer…），且数组不可改变
static String[] toString((baseType[] a)
static baseType[] copyOf((baseType[] a,int newLength)
static baseType[] copyRangr((baseType[] a,int from, int to)
static void equals(baseType[] a, baseType[] b)       
static int binarySearch(baseType[] a, basetype key)
```

## Math类

```java
public static int abs(int a)					// 返回参数的绝对值
public static double ceil(double a)				// 返回大于或等于参数的最小整数
public static double floor(double a)			// 返回小于或等于参数的最大整数
public static int round(float a)				// 按照四舍五入返回最接近参数的int类型的值
public static int max(int a,int b)				// 获取两个int值中的较大值
public static int min(int a,int b)				// 获取两个int值中的较小值
public static double pow (double a,double b)	// 计算a的b次幂的值
public static double random()					// 返回一个[0.0,1.0)的随机值
```

### 随机数Random

Random跟Scanner一样，也是Java提前写好的类，我们不需要关心是如何实现的，只要直接使用就可以了。

- 调用方法一：创建对象生成随机数

```java
Random r = new Random ();
int number = r.nextInt(随机数的范围);
上面这个格式里面，只有number是变量名，可以变，其他的都不允许变。
随机数范围的特点：从0开始，不包含指定值。比如：参数为10，生成的范围[0,10)
```

- 调用方法二：匿名对象生成随机数

```java
int number = Math.random(随机数的范围);
```

## System类

```java
public static long currentTimeMillis()			// 获取当前时间所对应的毫秒值（当前时间为0时区所对应的时间即就是英国格林尼治天文台旧址所在位置）
public static void exit(int status)				// 终止当前正在运行的Java虚拟机，0表示正常退出，非零表示异常退出
public static native void arraycopy(Object src,  int  srcPos, Object dest, int destPos, int length); // 进行数值元素copy
```

## Runtime类

```java
public static Runtime getRuntime()		//当前系统的运行环境对象
public void exit(int status)			//停止虚拟机
public int availableProcessors()		//获得CPU的线程数
public long maxMemory()				    //JVM能从系统中获取总内存大小（单位byte）
public long totalMemory()				//JVM已经从系统中获取总内存大小（单位byte）
public long freeMemory()				//JVM剩余内存大小（单位byte）
public Process exec(String command) 	//运行cmd命令
```

## LocalDate类

```
  LocalDate l = LocalDate.now();
  LocalDate l = LocalDate.of(1992.12.31);
  int t = l.getYear();
  int t = l.getMonthValue();
  int t = l.getDayOfMonth();
```

## Date类

- `public Date()`：从运行程序的此时此刻到时间原点经历的毫秒值,转换成Date对象，分配Date对象并初始化此对象，以表示分配它的时间（精确到毫秒）。

- `public Date(long date)`：将指定参数的毫秒值date,转换成Date对象，分配Date对象并初始化此对象，以表示自从标准基准时间（称为“历元（epoch）”，即1970年1月1日00:00:00 GMT）以来的指定毫秒数。

- `public long getTime()` 把日期对象转换成对应的时间毫秒值。

- `public void setTime(long time)` 把方法参数给定的毫秒值设置给日期对象

## SimpleDateFormat类
`java.text.SimpleDateFormat` 是日期/时间格式化类，我们通过这个类可以帮我们完成日期和文本之间的转换,也就是可以在Date对象与String对象之间进行来回转换

- `public SimpleDateFormat(String pattern)`：用给定的模式和默认语言环境的日期格式符号构造SimpleDateFormat。参数pattern是一个字符串，代表日期时间的自定义格式。例如：SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
- `public String format(Date date)`：将Date对象格式化为字符串。
- `public Date parse(String source)`：将字符串解析为Date对象。

## BigDecimal/BigInteger

```java
import java.math.*
BigInteger num1 = new BigInteger("12345678901234567890");
BigInteger num2 = new BigInteger("231313");

num1.add(num2l);//返回两个BigInteger的和。
num1.subtract(num2)//返回两个BigInteger的差。
num1.multiply(num2)//返回两个BigInteger的乘积。
num1.divide(num2);//返回两个BigInteger的商。
num1.mod(num2);//返回两个BigInteger相除的余数。
num1.pow(int exponent);//返回BigInteger的指定次幂。
num1.compareTo(num2);//比较两个BigInteger的大小。
```



# 四 对象和类

## 自定义类

###  从构造器开始（一定用new）
   所有的 Java 对象都是在堆中构造的，必须使用new
###  方法参数（java没有引用调用只有值调用）

- Java传递基本类型时，传递的是值的拷贝
  - 即一个方法只能修改一个基本数据类型的形参，不能修改一个基本数据类型的实参

- Java传递引用类型时，传递的是引用地址的拷贝
  - 一个方法可以改变一个对象参数的值。（因为形参和实参指向同一个对象）
  - 一个方法只能修改一个引用、引用类型的形参引用一个新的对象，不能修改一个引用类型的实参对象引用一个新的对象。

总结：java的方法可以修改形参的值和地址，但是只能改变实参的值，不能改变实参的地址

### 访问器方法和更改器方法

  以C++为例，带有const后缀的方法是访问器方法，默认为更改器方法

### 多个源文件的使用

  javac Employee\*.java.\*//\*为通配符

##  对象构造

### this引用
  1、隐式参数：是调用函数的对象
  2、调用该类其他的构造器
  在 Java 中， this 引用等价于 C++ 的 this 指针。但是， 在 C++ 中， 一个构造器不能调用另一个构造器 ， 在 C++ 中， 必须将抽取出的公共初始化代码编写成一个独立的方法。
### 初始化块（{……}）
  初始化数据域的方法：在构造器（构造函数）中设置值，在声明中赋值，初始化块

## 包

### 包的导入

一般使用package

什么时候需要导包？

​	情况一：在使用Java中提供的非核心包中的类时

​	情况二：使用自己写的其他包中的类时

什么时候不需要导包？

​	情况一：在使用Java核心包（java.lang）中的类时

​	情况二：在使用自己写的同一个包中的类时

### 类的导入
  一般使用import
  区别： 1、#include 将外部特性的声明加栽进来，这是因为 C++ 编译器无法查看任何文件的内部，除了正在编译的文件以及在头文件中明确包含的文件
  2、Java编译器可以查看其他文件的内部，只要告诉它到哪里去查看就可以了（如果显式地给出包名， 就可以不使用 import）
  3、 在 Java 中， package 与import 语句类似于 C++中的 namespace 和 using 指令。

### 静态导入
  import 语句不仅可以导入类，还增加了导入静态方法和静态域的功能。
  import static java.lang.System.*;

### 将类放入包中
  要想将一个类放人包中， 就必须将包的名字放在源文件的开头
  package com.horstiann.corejava;
  public class Employee
  {
  ……
  }
### 包作用域
  如果没有指定 public 或 private , 这个部分（类、方法或变量）可以被同一个包中的所有方法访问
  ##注释

## 注释

### 方法注释
  @param 变量描述
  这个标记将对当前方法的“ param” （参数）部分添加一个条目。这个描述可以占据多行， 并可以使用 HTML 标记。一个方法的所有 @param 标记必须放在一起。
  @return 描述
  这个标记将对当前方法添加“ return” （返回）部分。这个描述可以跨越多行， 并可以使用 HTML 标记。
  @throws 类描述
  这个标记将添加一个注释， 用于表示这个方法有可能抛出异常。 
  /**
  注释
  */
### 通用注释
  @author 姓名
  这个标记将产生一个 author(作者）条目。可以使用多个 @author 标记，每个 @author 标记对应一个作者
  @version
  这个标记将产生一个“ version”（版本）条目。这里的文本可以是对当前版本的任何描述。
  下面的标记可以用于所有的文档注释中。
  @since 文本
  这个标记将产生一个“ since” （始于）条目。这里的 text 可以是对引人特性的版本描述。例如， ©since version 1.7.10
  @deprecated
  这个标记将对类、 方法或变量添加一个不再使用的注释。文本中给出了取代的建议。
  @deprecated 文本
  通过 @see 和@link标记，可以使用超级链接， 链接到 javadoc 文档的相关部分或外部文档。
  @see 引用
  这个标记将在“ see also” 部分增加一个超级链接。它可以用于类中，也可以用于方法中。

### 重写注解@Override

* @Override:注解，重写注解校验！
* 这个注解标记的方法，就说明这个方法必须是重写父类的方法，否则编译阶段报错。

### 测试注解@Test

- @Test注解是[JUnit](https://so.csdn.net/so/search?q=JUnit&spm=1001.2101.3001.7020)测试的基础，JUnit 4的优势就在于支持了注解。
- @Test的使用 是该方法可以不用main方法调用就可以测试出运行结果，是一种测试方法，注意被测试的方法必须是public修饰的。

# 五 继 承

##  类、超类和子类（超类即父类）

### 定义子类
  Java 与 C++ 定义继承类的方式十分相似。Java 用关键字 extends 代替了 C++中的冒号（：）。
  在 Java 中， 所有的继承都是公有继承， 而没有 C++ 中的私有继承和保护继承 。
### 覆盖方法
  在 Java 中使用关键字 super 调用超类的方法，而在 C++ 中则采用超类名加上：：操作符的形式。
  例如， 在 Manager 类的 getSalary 方法中，应该将 super.getSalary 替换为 Employee::getSalary
### 子类构造器

NOTE：super的作用：一是调用超类的方法，二是调用超类的构造器

  public Manager(String name, double salary, int year, int month, int day)
  {
  	super(name, salary, year, month, day);
  	bonus = 0;
  }

### 阻止继承：final 类和方法（非虚函数）
  类被声明为 final。如果这样做，子类就不能覆盖这个类中所有方法
  类中的特定方法也可以被声明为 final。如果这样做，子类就不能覆盖这个方法。

### 类型转换

  1、只能在继承层次内进行类型转换。
  2、在将超类转换成子类之前，应该使用 instanceof进行检查。（如果变量属于该数据类型或者其子类类型，返回true;如果变量不属于该数据类型或者其子类类型，返回false。）

#### 向上转型（自动转换）

- **向上转型**：多态本身是子类类型向父类类型向上转换（自动转换）的过程，这个过程是默认的。
  当父类引用指向一个子类对象时，便是向上转型。
  使用格式：

```java
父类类型  变量名 = new 子类类型();
如：Animal a = new Cat();
```

#### 向下转型（强制转换）

- **向下转型**：父类类型向子类类型向下转换的过程，这个过程是强制的。
  一个已经向上转型的子类对象，将父类引用转为子类引用，可以使用强制类型转换的格式，便是向下转型。

- 使用格式：

  ```java
  子类类型 变量名 = (子类类型) 父类变量名;
  如:Aniaml a = new Cat();
     Cat c =(Cat) a;  
  ```

## 权限修饰符

|                  | public | protected | 默认 | private |
| ---------------- | ------ | --------- | ---- | ------- |
| 同一类中         | √      | √         | √    | √       |
| 同一包中的类     | √      | √         | √    |         |
| 不同包的子类     | √      | √         |      |         |
| 不同包中的无关类 | √      |           |      |         |

### 抽象类及抽象方法

  public abstract class Person
  {
  public abstract String getDescriptionO；
  }
  抽象方法充当着占位的角色， 它们的具体实现在子类中。
  除了抽象方法之外，抽象类还可以包含具体数据和具体方法

# 六 接口、lambda和内部类

## 接口

### 接口概念
  1、接口不是类，而是对类的一组需求描述，这些类要遵从接口描述的统一格式进行定义
  2、接口无法实例化，但是可以声明变量
### 接口定义与使用
  定义：public interface 接口名<T>
  使用：（p……）class 类名 implements 接口名<T>

### 接口的成员

  在JDK7，包括JDK7之前，接口中的**只有**包含：抽象方法和常量

- 抽象方法

​       注意：接口中的抽象方法默认会自动加上public abstract修饰程序员无需自己手写！！

-  常量

 在接口中定义的成员变量默认会加上： public static final修饰程序员无需自己手写。

### 为什么使用接口不使用抽象类
  抽象类表示通用属性存在这样一个问题： 每个类只能扩展于一个类
### 静态方法？
### 默认方法
  1、用 default 修饰在方法前修饰，如default int compareTo(T other) { return 0; }//默认方法的内容一定会被重写（？或弃置），所以可以随便写
  2、默认方法一般用于“接口演化”：假如很久之前创建了一个接口，并且这个接口已经被很多类实现了，但是现在你需要给这个接口中扩展一个新方法A，这就导致前面那些已经实现该接口的类必须要重写这个方法A，否则就不能编译。

### 解决默认方法和继承冲突

NOTE：类与接口是实现关系，接口与接口是继承关系

  1、超类优先。
  2、接口冲突，必须覆盖这个方法来解决冲突。

## lambda及方法引用

### 格式
  (传入参数）->返回类型{……}；
  NOTE：java中的lambda默认且只能以[=]，非mutable(C++中的作用域内所以参数全部值传递，且不允许改变)的方式

### 函数式接口（类似于仿函数）
定义：有且仅有一个抽象方法的接口

 	对于只有一个抽象方法的接口， 需要这种接口的对象时， 就可以提供一个 lambda 表达式实现函数式接口中的那个抽象方法。
  <img src=" \assets\1.JPG">

<img src="\assets\2.JPG">

### 方法引用

- 方法引用的出现原因

如果我们在Lambda中所指定的操作方案，已经有地方存在相同方案，则可选择传入方法引用代替Lambda

- 三种方法引用的方法

  1、object::instanceMethod	相当于	（参数）-> object.instanceMethod(参数……)
  2、Class::staticMethod	相当于	（参数）-> Class.staticMethod(参数……)
  3、Class::instanceMethod	相当于	(对象A，参数……)  ->  对象A.staticMethod(参数……)

### 构造器引用
  构造器引用与方法引用很类似，只不过方法名为 new

## 内部类

NOTE：内部类访问外部类对象的格式是：**外部类名.this**

### 为什么使用内部类

  内部类方法可以访问该类定义所在的作用域中的数据， 包括私有的数据。
  内部类可以对同一个包中的其他类隐藏起来。
  当想要定义一个回调函数且不想编写大量代码时，使用匿名 （anonymous) 内部类比较便捷。

### 成员内部类（定义在成员内部）
  class outer{
  class inner{……}
  outer.inner 对象 = outer.new inner();//调用内部类
  }

### 局部内部类（定义在方法内部）
  void fun{
  class inner{……}
  inner 对象 = new inner();//调用内部类
  }

### 静态内部类（使用static关键字修饰的成员内部类）
  静态内部类只能访问外部类的静态成员，同时通过外部类访问静态内部类成员时，可以跳过外部内直接通过内部类访问静态内部类成员。
  class outer{
  static class inner{……}
  inner 对象 new inner();//调用内部类
  }

### 匿名内部类
  将局部内部类的使用再深人一步。 假如只创建这个类的**一个对象**，就不必命名了。这种类被称为匿名内部类（anonymous inner class)，匿名内部类**必须继承一个父类或实现一个接口**

  void fun{

  inner 对象 = new inner (){
  ……
  };

//调用这个对象

  }

# 七 异常、断言和日志

##  处理错误

###  异常分类
   1、所有的异常都是由 Throwable 继承而来，但在下一层立即分解为两个分支：Error（描述了 Java 运行时系统的内部错误和资源耗尽错误）和 Exception；
   Exception 层次结构又分解为两个分支：一个分支派生于 RuntimeException（由程序错误导致的异常）; 另一个分支包含其他异常
   2、 派 生 于 Error 类 或 RuntimeException 类的所有异常称为非受查( unchecked ) 异常，所有其他的异常称为受查（ checked) 异常
###  声明受查异常throws（不要声明非受查异常）
   例：public FilelnputStream(String name) throws FileNotFoundException
   1、出现FileNotFoimdException 异常， 构造器将不会初始化一个新的 FilelnputStream 对象， 而是抛出一个 FileNotFoundException 类对象
   2、子类方法中声明的受查异常不能比超类方法中声明的异常更通用（即子类异常应该比父类异常范围小）

###  抛出异常throw
   throw new EOFExceptionQ();
   或者
   EOFException e = new EOFException();
   throw e;
###  创建异常类
   class FileFormatException extends IOException//定义一个派生于Exception 的类，或者派生于 Exception 子类的类
   {
   public FileFormatExceptionO {}
   public FileFormatException(String gripe)//包含两个构造器， 一个是默认的构造器；另一个是带有详细描述信息的构造器（超类 Throwable 的 toString 方法将会打印出这些详细信息)
   {
   	super(gripe);
   }
   }

##  捕获异常

###  捕获异常try…catch…finally
   try
   {
   code
   }
   catch (ExceptionType e)
   {
   handlerfor this type
   }
   finally
   {
   一定执行的code
   }
###  再次抛出异常与异常链???
###  带资源的 try 语句
   try (Scanner in = new Scanner(new FileInputStream(/usr/share/dict/words")), "UTF###8")
   {
   while (in.hasNext())
   System.out.println(in.next());
   }
   这个块正常退出时， 或者存在一个异常时， 都会调用 in.close()方法， 就好像使用了finally块一样。

###  分析堆栈轨迹元素???
   堆栈轨迹（ stack trace ) 是一个方法调用过程的列表， 它包含了程序执行过程中方法调用的特定位置

## 断言

断言机制允许在测试期间向代码中插入一些检査语句。当代码发布时，这些插人的检测语句将会被自动地移走。
Java 语言引人了关键字 assert。这个关键字有两种形式：assert 条件；和assert 条件：表达式；
这两种形式都会对条件进行检测， 如果结果为 false, 则抛出一个 AssertionError 异常。在第二种形式中，表达式将被传人 AssertionError 的构造器， 并转换成一个消息字符串

##  记录曰志

# 八 泛型

## 泛型类

定义：修饰符 A<T> { . . . }
继承：子类是泛型类，子类和父类（接口）泛型类型要一致；
子类不是泛型类，父类泛型（接口）类型要明确
使用：A<String> a1 = new A<>();//创建对象时可以使用菱形语法（泛型推断）

## 泛型方法

定义：修饰符<T> 返回值类型 fun(T... a)
调用：<String>fun("]ohnM, "Q.", "Public");方法调用中可以省略 <String> 类型参数。编译器有足够的信息能够推断出所调用的方法

##  类型变量的限定（extends）

public static class<T extends Serializable & Comparable> (T a){……}； 
1、其中extends不是继承的意思，而是表示 T 应该是绑定类型的子类型 （只是同名而已）。 T 和绑定类型可以是类， 也可以是接口。
可以有多个限定， 例如：T extends Comparable & Serializable限定类型用“ &” 分隔，而逗号用来分隔类型变量。

##  泛型代码和虚拟机

### 类 型 擦 除
  1、擦除泛型T类型, 并替换为限定类型M（无限定的变量用 Object,有限定则用限定类Comparable代替)
  2、当子类继承父类（接口）实现抽象泛型方法时，编译器会为子类自动生成桥接方法。（原因：父类中的被泛型擦除后的类型（如Object）与子类中实例化后的类型(如String)不一致，导致无法完成重写）

### 调用遗留代码？？？

##  约束与局限性

### 不能使用基本类型实例化泛型
### 反射查看实例化的对象时无法显示对应的泛型（不能看到Employee\<String>只能看到Employee）
### 泛型数组相关
  1、泛型数组声明允许，但是不允许new泛型数组；
  2、可以使用newinstance（Class\<T>,int len）创建泛型数组；

## 通配符

### 无限制通配符
  无限制通配符为“？”，通配符是类型实参，不是类型形参
### 通配符下限super
  类/接口<? super 实参类型>//要求该泛型类型必须是实参类型及其父类类型
### 通配符上限extends
  类/接口<? extends 实参类型>//要求该泛型类型必须是实参类型及其子类类型

### 区别T和？

T表示泛型，代表一种数据类型

?是通配符，表示不确定的数据类型

# 九 集合

## 集合框架

<img src=" .\assets\4.JPG">
<img src=" .\assets\3.JPG">

## Collection集合（单列集合）

| 方法名                     | 说明                               |
| :------------------------- | :--------------------------------- |
| boolean add(E e)           | 添加元素                           |
| E remove(int index)  | 从集合中删除指定索引的元素并返回            |
| boolean remove(Object o)   | 从集合中移除指定的元素             |
| boolean removeIf(Object o) | 根据条件进行移除                   |
| void   clear()             | 清空集合中的元素                   |
| boolean contains(Object o) | 判断集合中是否存在指定的元素       |
| boolean isEmpty()          | 判断集合是否为空                   |
| int   size()               | 集合的长度，也就是集合中元素的个数 |

### 迭代器

  public interface Iterator<E>
  {

  E next();//当调用 next 时，迭代器就越过下一个元素，并返回刚刚越过的那个元素的引用
  boolean hasNext();//如果调用 remove 之前没有调用 next 将是不合法的
  void remove();//remove 方法将会删除上次调用 next 方法时返回的元素
  default void forEachRemaining(Consumer<? super E> action);
  }

## List集合（有序，可重复，有索引）

NOTE：有序指的是存取有序，比如1，3，4，13，那么取（遍历）出来的顺序也是1，3，4，1，有索引指的是可以通过下标检索进行crud

| 方法名                          | 描述                                   |
| ------------------------------- | -------------------------------------- |
| void add(int index,E   element) | 在此集合中的指定位置插入指定的元素     |
| E remove(int   index)           | 删除指定索引处的元素，返回被删除的元素 |
|boolean remove(Object o) |从集合中移除指定的元素 |
| E set(int index,E   element)    | 修改指定索引处的元素，返回被修改的元素 |
| E get(int   index)              | 返回指定索引处的元素                   |

### ArrayList集合

​	底层是数组结构实现，查询快、增删慢

- 扩容方法：添加时进行判断，超过容量时进行扩容，容量小于10，扩容到10，大于10，扩容为原来的1.5倍

### LinkedList集合

​	底层是链表结构实现，查询慢、增删快

- 特有方法

| 方法名                    | 说明                             |
| ------------------------- | -------------------------------- |
| public void addFirst(E e) | 在该列表开头插入指定的元素       |
| public void addLast(E e)  | 将指定的元素追加到此列表的末尾   |
| public E getFirst()       | 返回此列表中的第一个元素         |
| public   E getLast()      | 返回此列表中的最后一个元素       |
| public E removeFirst()    | 从此列表中删除并返回第一个元素   |
| public   E removeLast()   | 从此列表中删除并返回最后一个元素 |

### vector集合

与ArrayList类似，不过ArrayList是同步的, 而Vector是非同步的

## Set集合（无序，不可重复，无索引）

### TreeSet集合（红黑树）

- 构造方法

TreeSet()：根据其元素的自然排序进行排序

TreeSet(Comparator comparator) ：根据指定的比较器进行排序

- comparator构造

(默认)左小右大， `return` 前减后(o1-o2)或o1.compareTo(o2)
左大右右小， `return` 后减前(o2-o1)或o2,compareTo(o1)

#### 方式一：自然排序Comparable

自然排序，就是让元素所属的类实现Comparable接口，重写compareTo(T o)方法

- 如果指定的数与参数相等返回 0。
- 如果指定的数小于参数返回 -1。
- 如果指定的数大于参数返回 1。

```java
public class TreeMember implements Comparable<TreeMember>{
   	……
    @Override
    public int compareTo(TreeMember t) {
        if(this.val > t.val)
            return 1;
        ……
    }
}
public class MyTreeSet {
        TreeSet<TreeMember> ts = new TreeSet<>();
}
```

#### 方式二：比较器排序Comparator

比较器排序，就是让集合构造方法接收Comparator的实现类对象，重写compare(T o1,T o2)方法

- 如果参数o1与参数o2相等返回 0。
- 如果参数o1小于参数o2返回 负数。
- 如果参数o1大于参数o2返回 正数,将大的节点放在树顶

```java
public class TreeMember implements Comparable<TreeMember>{
   	……
}
public class MyTreeSet {
        TreeSet<TreeMember> ts = new TreeSet<>(new Comparator<TreeMember>{
            @Override
            public int compare(TreeMember t1, TreeMember t2) {
				return t1.val - t2.val;//从小到大
            }
        });
}
```

#### 方法三：构造lambda表达式

```java
public class TreeMember implements Comparable<TreeMember>{
   	……
}
public class MyTreeSet {
        TreeSet<TreeMember> ts = new TreeSet<>((t1.val, t2.val)->{
            return t1.val - t2.val;//从小到大
        });
}
```

#### 两种比较方式总结

+ 两种比较方式小结
  + 自然排序: 自定义类实现Comparable接口,重写compareTo方法,根据返回值进行排序
  + 比较器排序: 创建TreeSet对象的时候传递Comparator的实现类对象,重写compare方法,根据返回值进行排序
  + 在使用的时候,默认使用自然排序,当自然排序不满足现在的需求时,必须使用比较器排序
+ 两种方式中关于返回值的规则
  + 如果返回值为负数，表示当前存入的元素是较小值，存左边
  + 如果返回值为0，表示当前存入的元素跟集合中元素重复了，不存
  + 如果返回值为正数，表示当前存入的元素是较大值，存右边

###  HashSet集合（哈希表）

+ HashSet底层是哈希表结构的
+ 依赖hashCode方法和equals方法保证键的唯一
+ 如果键要存储的是自定义对象，需要重写hashCode和equals方法

## Map集合（双列集合,键不可重复,值可重复）

- 基本功能

| 方法名                              | 说明                                 |
| ----------------------------------- | ------------------------------------ |
| V   put(K key,V   value)            | 添加或修改元素                       |
| V   remove(Object key)              | 根据键删除键值对元素                 |
| void   clear()                      | 移除所有的键值对元素                 |
| boolean containsKey(Object key)     | 判断集合是否包含指定的键             |
| boolean containsValue(Object value) | 判断集合是否包含指定的值             |
| boolean isEmpty()                   | 判断集合是否为空                     |
| int size()                          | 集合的长度，也就是集合中键值对的个数 |

- 获取功能

| 方法名                           | 说明                     |
| -------------------------------- | ------------------------ |
| V   get(Object key)              | 根据键获取值             |
| Set<K>   keySet()                | 获取所有键的集合         |
| Collection<V>   values()         | 获取所有值的集合         |
| Set<Map.Entry<K,V>>   entrySet() | 获取所有键值对对象的集合 |

- 遍历方法

```java
for (Integer key : map.keySet()) {
	sum += key + map.get(key)
}
```
    for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
    	sum += entry.getKey() + entry.getValue();
    }
### HashMap集合(哈希表)

+ HashMap底层是哈希表结构的
+ 依赖hashCode方法和equals方法保证键的唯一
+ 如果键要存储的是自定义对象，需要重写hashCode和equals方法

### TreeMap集合（红黑树）
  + TreeMap底层是红黑树结构
  + 依赖自然排序或者比较器排序,对键进行排序
  + 如果键存储的是自定义对象,需要实现Comparable接口或者在创建TreeMap对象时候给出比较器排序规则

### LinkedHashMap集合（哈希表+双链表）

底层数据结构是哈希表，但是用一个额外的双链表记录储存的顺序，保证存取的有序性

### ConcurrentHashMap集合

1 、HashMap是线程不安全的。多线程环境下会有数据安全问题

2 、Hashtable是线程安全的，但是会将整张表锁起来，效率低下

3、ConcurrentHashMap也是线程安全的，效率较高。     在JDK7和JDK8中，底层原理不一样。

### Map进行对比

```
默认：HashMap（效率最高）
如果要保证存取有序：LinkedHashMap
如果要进行排序：TreeMap
```

## Stack类（非抽象类）

```java
// 入栈
public E push(E item)

// 出栈
public synchronized E pop() 

// 得到栈顶元素
public synchronized E peek()
    
// 是否为空
public boolean isEmpty()
```

## Queue类

- Deque类（双端队列抽象类）
- LinkedList（实现类）

```
// 入队
public E offer(E item)

// 出队
public synchronized E poll() 

// 得到栈顶元素
public synchronized E peek()
    
// 是否为空
public boolean isEmpty()
```

### PriorityQueue

(默认)小顶堆（小的在前）， PriorityQueue<Integrer> priorityQueue = new PriorityQueue<>((pair1, pair2) -> pair2 - pair1);//pair2.compareTo(pair1);
大顶堆（大的在前）， PriorityQueue<Integrer> priorityQueue = new PriorityQueue<>((pair1, pair2) -> pair1 - pair2);

## 不可变集合

### 什么是不可变集合

是一个长度不可变，内容也无法修改的集合

### 不可变的list集合

List<String> list = List.of("张三", "李四", "王五", "赵六");

### 不可变的set集合

Set<String> set = Set.of("张三", "张三", "李四", "王五", "赵六");

### 不可变的map集合
  键值对个数小于等于10：
  Map<String, String> map = Map.of("张三", "南京", "张三", "北京", "王五", "上海")
  键值对个数大于10？？？;

# 十 文件

## 构造方法

- `public File(String pathname) ` ：通过将给定的**路径名字符串**转换为抽象路径名来创建新的 File实例。  

- `public File(String parent, String child) ` ：从**父路径名字符串+子路径名字符串**创建新的 File实例。

- `public File(File parent, String child)` ：从**父路径对象中取出路径名字字符串+和子路径名字符串**创建新的 File实例。  

## 常用方法

### 获取文件属性的方法

- `public String getAbsolutePath() ` ：返回此File的绝对路径名字符串。

- ` public String getPath() ` ：将此File转换为路径名字符串。 

- `public String getName()`  ：返回由此File表示的文件或目录的名称。  

- `public long length()`  ：返回由此File表示的文件的长度

### 判断文件（目录）存在的方法

- `public boolean exists()` ：此File表示的文件或目录是否实际存在。
- `public boolean isDirectory()` ：此File表示的是否为目录。
- `public boolean isFile()` ：此File表示的是否为文件。

### 创建删除文件（目录）的方法

- `public boolean createNewFile()` ：当且仅当具有该名称的文件尚不存在时，创建一个新的空文件。 
- `public boolean delete()` ：删除由此File表示的文件或目录。  
- `public boolean mkdir()` ：创建由此File表示的目录。
- `public boolean mkdirs()` ：创建由此File表示的目录，包括任何必需但不存在的父目录

# 十一 流

## Stream流

所有的流均在io包下

### Stream流的三类方法

Stream流的三类方法

- 获取Stream流

  - 创建一条流水线,并把数据放到流水线上准备进行操作

- 中间方法

  - 流水线上的操作
  - 一次操作完毕之后,还可以继续进行其他操作

- 终结方法

  - 一个Stream流只能有一个终结方法

  - 是流水线上的最后一个操作

### 生成Stream流的方式

- Collection体系集合

  使用默认方法stream()生成流， default Stream<E> stream()

- Map体系集合 ？？？

  把Map转成Set集合，间接的生成流

- 数组

  通过Arrays中的静态方法stream生成流

- 同种数据类型的多个数据

  通过Stream接口的静态方法of(T... values)生成流
### Stream流中间操作方法【应用】

- 概念

  中间操作的意思是,执行完此方法之后,Stream流依然可以继续执行其他操作

- 常见方法

  | 方法名                                          | 说明                                                         | 例子                                                         |
  | ----------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
  | Stream<T> filter(Predicate predicate)           | 用于对流中的数据进行过滤                                     | .filter(entity -> entity.num >  0)                           |
  | Stream<T> limit(long maxSize)                   | 返回此流中的元素组成的流，截取前指定参数个数的数据           |                                                              |
  | Stream<T> skip(long n)                          | 跳过指定参数个数的数据，返回由该流的剩余元素组成的流         |                                                              |
  | static <T> Stream<T> concat(Stream a, Stream b) | 合并a和b两个流为一个流                                       |                                                              |
  | Stream<T> distinct()                            | 返回由该流的不同元素（根据Object.equals(Object) ）组成的流   |                                                              |
  | Stream<T> map(Function f) 。                    | 接收一个函数作为参数，该函数会被应用到每个元素上，并将其映射成一个新的元素，并将所有返回的元素转换成流 | .map(entity -> { entity.setNum(0);    return temp;}          |
  | Stream<T>  sorted(Comparator com)               | 产生一个新的流，按比较器顺序定制排序                         | .sorted((entity1, entity2) -> entity1.getNum() - entity2.getNum()) |
### Stream流终结操作方法【应用】

- 概念

  终结操作的意思是,执行完此方法之后,Stream流将不能再执行其他操作

- 常见方法

  | 方法名                        | 说明                                                  |
  | ----------------------------- | ----------------------------------------------------- |
  | void forEach(Consumer action) | 对此流的每个元素执行操作（于map类似，但不会在生成流） |
  | long count()                  | 返回此流中的元素数                                    |

### Stream流的收集操作【应用】

- 概念

  对数据使用Stream流的方式操作完毕后,可以把流中的数据收集到集合中

- 常用方法

  | 方法名                         | 说明                                                         |
  | ------------------------------ | ------------------------------------------------------------ |
  | R collect(Collector collector) | 把结果收集到集合中collect负责收集数据.（获取流中剩余的数据,但是他不负责创建容器,也不负责把数据添加到容器中，必须自己定义容器并装入） |
  | R toArray()                    | 把结果转化为数组                                             |

  

- 工具类Collectors提供了具体的收集方式

  | 方法名                                                       | 说明                   |
  | ------------------------------------------------------------ | ---------------------- |
  | public static <T> Collector toList()                         | 把元素收集到List集合中 |
  | public static <T> Collector toSet()                          | 把元素收集到Set集合中  |
  | public static  Collector toMap(Function keyMapper,Function valueMapper) | 把元素收集到Map集合中  |

- 例子：List<String> list2 = list1.stream().collect(Collectors.toList());

## 字节流

### 区别字节流与字符流
* **字节流** ：以字节（Byte）为单位，读写数据的流。字符流，可操作任意文件。

* **字符流** ：以字符（char）为单位，读写数据的流。字符流，只能操作文本文件，不能操作图片，视频等非文本文件。

  <img src=" .\assets\5.png">
### 字节输出流【OutputStream】

#### 构造方法

* `public FileOutputStream(File file)`：创建文件输出流以写入由指定的 File对象表示的文件。 

* `public FileOutputStream(String name)`： 创建文件输出流以指定的名称写入文件。  

* `public FileOutputStream(File file, boolean append)`： 创建文件输出流以写入由指定的 File对象表示的文件。  

* `public FileOutputStream(String name, boolean append)`： 创建文件输出流以指定的名称写入文件

#### 写方法  

* `public void write(byte[] b)`：将 b.length字节从指定的字节数组写入此输出流。  

* `public void write(byte[] b, int off, int len)` ：从指定的字节数组写入 len字节，从偏移量 off开始输出到此输出流。  

* `public abstract void write(int b)` ：将指定的字节输出流。

#### 其他方法

* `public void close()` ：关闭此输出流并释放与此流相关联的任何系统资源。  

* `public void flush() ` ：刷新此输出流并强制任何缓冲的输出字节被写出。
  NOTE：写出换行为 fos.write("\r\n".getBytes());

### 字节输入流【InputStream】

#### 构造方法

* `FileInputStream(File file)`： 通过打开与实际文件的连接来创建一个 FileInputStream ，该文件由文件系统中的 File对象 file命名。 
* `FileInputStream(String name)`： 通过打开与实际文件的连接来创建一个 FileInputStream ，该文件由文件系统中的路径名 name命名。

#### 读方法    

- `public abstract int read()`： 从输入流读取数据的下一个字节。 
- `public int read(byte[] b)`： 从输入流中读取一些字节数，并将它们存储到字节数组 b中 。 

#### 其他方法

- `public void close()` ：关闭此输入流并释放与此流相关联的任何系统资源。 

## 字符流

### 字符输出流【Writer】

#### 构造方法

* `FileWriter(File file)`： 创建一个新的 FileWriter，给定要读取的File对象。   

* `FileWriter(String fileName)`： 创建一个新的 FileWriter，给定要读取的文件的名称。 

#### 写方法  

* `public abstract void write(int b)` ：将指定的字节输出流。

#### 其他方法

* `public void close()` ：关闭此输出流并释放与此流相关联的任何系统资源。  

* `public void flush() ` ：刷新此输出流并强制任何缓冲的输出字节被写出。
  NOTE：写出换行为 fos.write("\r\n".getBytes());
  

### 字符输入流【Reader】

#### 构造方法

* `FileReader(File file)`： 创建一个新的 FileReader ，给定要读取的File对象。   
* `FileReader(String fileName)`： 创建一个新的 FileReader ，给定要读取的文件的名称。  

#### 读方法    

- `public abstract int read()`： 读取一个字符的数据，读取到文件末尾，返回`-1` 
- `public int read(char[] c)`： 每次读取b的长度个字符到数组中，返回读取到的有效字符个数，读取到末尾时，返回`-1` ，代码使用演示：

#### 其他方法

- `public void close()` ：关闭此输入流并释放与此流相关联的任何系统资源。

## 缓冲流

缓冲流,也叫高效流，是对4个基本的`FileXxx` 流的增强，所以也是4个流，按照数据类型分类：

* **字节缓冲流**：`BufferedInputStream`，`BufferedOutputStream` 
* **字符缓冲流**：`BufferedReader`，`BufferedWriter`

### 字节缓冲流【BufferedInput（Output）Stream】
- 构造方法

`public BufferedInputStream(InputStream in)` ：创建一个 新的缓冲输入流。 
`public BufferedOutputStream(OutputStream out)`： 创建一个新的缓冲输出流。

### 字符缓冲流【BufferedReader（Writer）】
- 构造方法

`public BufferedReader(Reader in)` ：创建一个 新的缓冲输入流。 
`public BufferedWriter(Writer out)`： 创建一个新的缓冲输出流。

- 特有方法

BufferedReader：`public String readLine()`: 读一行文字。 
BufferedWriter：`public void newLine()`: 写一行行分隔符,由系统属性定义符号。

## 转换流（指定编码读取）

### InputStreamReader类  

- 构造方法

InputStreamReader(InputStream in): 创建一个使用默认字符集的字符流。

InputStreamReader(InputStream in, String charsetName)`: 创建一个指定字符集的字符流。

### OutputStreamWriter类 

- 构造方法

OutputStreamWriter(OutputStream in)`: 创建一个使用默认字符集的字符流。 

OutputStreamWriter(OutputStream in, String charsetName)`: 创建一个指定字符集的字符

## 序列化（对象转字节）

将对象进行写入写出

### 可序列化对象

* 该类必须实现`java.io.Serializable ` 接口。`Serializable` 是一个标记接口，不实现此接口的类将不会使任何状态序列化或反序列化，会抛出`NotSerializableException` 。

* 该类的所有属性必须是可序列化的。如果有一个属性不需要可序列化的，则该属性必须注明是瞬态的，使用`transient` 关键字修饰。

* 序列版本号必须不变。`serialVersionUID` 该版本号的目的在于验证序列化的对象和对应类是否版本匹配（如何设置见黑马）。
例：
public class Employee implements java.io.Serializable {
     // 加入序列版本号
     private static final long serialVersionUID = 1L;
     public String name;
     public transient String address;
     // 添加新的属性 ,重新编译, 可以反序列化,该属性赋为默认值.
     public int eid; 

     public void addressCheck() {
         System.out.println("Address  check : " + name + " -- " + address);
     }
}

### ObjectOutputStream类

- 构造方法

public ObjectOutputStream(OutputStream out) ： 创建一个指定OutputStream的

- 序列化操作

public final void writeObject (Object obj)` : 将指定的对象写出

### ObjectInputStream类

ObjectInputStream反序列化流，将之前使用ObjectOutputStream序列化的原始数据恢复为对象。 

- 构造方法

public ObjectInputStream(InputStream in) `： 创建一个指定InputStream的ObjectInputStream。

- 反序列化操作

如果能找到一个对象的class文件，我们可以进行反序列化操作，调用`ObjectInputStream`读取对象的方法：public final Object readObject ()` : 读取一个对象。

## 其他流

- 打印流PrintStream类

平时我们在控制台打印输出，是调用`print`方法和`println`方法完成的，这两个方法都来自于`java.io.PrintStream`类，该类能够方便地打印各种数据类型的值，是一种便捷的输出方式。

构造方法：

public PrintStream(String fileName)  `： 使用指定的文件名创建一个新的打印流。

- 压缩流和解压缩流

压缩流：

​	负责压缩文件或者文件夹

解压缩流：

​	负责把压缩包中的文件和文件夹解压出来

# 十三 网络编程

### InetAddress

InetAddress：此类表示Internet协议（IP）地址

- 相关方法

  | 方法名                                    | 说明                                                         |
  | ----------------------------------------- | ------------------------------------------------------------ |
  | static InetAddress getByName(String host) | 确定主机名称的IP地址。主机名称可以是机器名称，也可以是IP地址 |
  | String getHostName()                      | 获取此IP地址的主机名                                         |
  | String getHostAddress()                   | 返回文本显示中的IP地址字符串                                 |

## UDP

### UDP发送数据

- 构造方法

  | 方法名                                                      | 说明                                                 |
  | ----------------------------------------------------------- | ---------------------------------------------------- |
  | DatagramSocket()                                            | 创建数据报套接字并将其绑定到本机地址上的任何可用端口 |
  | DatagramPacket(byte[] buf,int len,InetAddress add,int port) | 创建数据包,发送长度为len的数据包到指定主机的指定端口 |

- 相关方法

  | 方法名                         | 说明                   |
  | ------------------------------ | ---------------------- |
  | void send(DatagramPacket p)    | 发送数据报包           |
  | void close()                   | 关闭数据报套接字       |
  | void receive(DatagramPacket p) | 从此套接字接受数据报包 |

- 发送数据的步骤

  - 创建发送端的Socket对象(DatagramSocket)
  - 创建数据，并把数据打包
  - 调用DatagramSocket对象的方法发送数据
  - 关闭发送端

- 代码演示

  ```java
  public class SendDemo {
      public static void main(String[] args) throws IOException {
          //创建发送端的Socket对象(DatagramSocket)
          // DatagramSocket() 构造数据报套接字并将其绑定到本地主机上的任何可用端口
          DatagramSocket ds = new DatagramSocket();
  
          //创建数据，并把数据打包
          //DatagramPacket(byte[] buf, int length, InetAddress address, int port)
          //构造一个数据包，发送长度为 length的数据包到指定主机上的指定端口号。
          byte[] bys = "hello,udp,我来了".getBytes();
  
          DatagramPacket dp = new DatagramPacket(bys,bys.length,InetAddress.getByName("127.0.0.1"),10086);
  
          //调用DatagramSocket对象的方法发送数据
          //void send(DatagramPacket p) 从此套接字发送数据报包
          ds.send(dp);
  
          //关闭发送端
          //void close() 关闭此数据报套接字
          ds.close();
      }
  }
  ```

### UDP接收数据

- 接收数据的步骤

  - 创建接收端的Socket对象(DatagramSocket)
  - 创建一个数据包，用于接收数据
  - 调用DatagramSocket对象的方法接收数据
  - 解析数据包，并把数据在控制台显示
  - 关闭接收端

- 构造方法

  | 方法名                              | 说明                                            |
  | ----------------------------------- | ----------------------------------------------- |
  | DatagramPacket(byte[] buf, int len) | 创建一个DatagramPacket用于接收长度为len的数据包 |

- 相关方法

  | 方法名            | 说明                                     |
  | ----------------- | ---------------------------------------- |
  | byte[]  getData() | 返回数据缓冲区                           |
  | int  getLength()  | 返回要发送的数据的长度或接收的数据的长度 |

- 示例代码

  ```java
  public class ReceiveDemo {
      public static void main(String[] args) throws IOException {
        	//创建接收端的Socket对象(DatagramSocket)
        	DatagramSocket ds = new DatagramSocket(12345);//替换部分一
  
        	//创建一个数据包，用于接收数据
        	byte[] bys = new byte[1024];
        	DatagramPacket dp = new DatagramPacket(bys, bys.length);
  
        	//调用DatagramSocket对象的方法接收数据
        	ds.receive(dp);
  
        	//解析数据包，并把数据在控制台显示
        	System.out.println("数据是：" + new String(dp.getData(), 0,                                             dp.getLength()));
          }
      }
  }
  ```


### UDP组播实现

1、需要绑定的IP为组播IP

2、将接收端替换部分一替换为以下

    // 1. 创建接收端Socket对象(MulticastSocket)
    MulticastSocket ms = new MulticastSocket(10000);
    
    // 2. 把当前计算机绑定一个组播地址,表示添加到这一组中.
    ms.joinGroup(InetAddress.getByName("224.0.1.0"));
### UDP广播实现

绑定IP地址必须为255.255.255.255，其他与单播实现一致

## TCP

### TCP发送数据

- 构造方法

  | 方法名                               | 说明                                           |
  | ------------------------------------ | ---------------------------------------------- |
  | Socket(InetAddress address,int port) | 创建流套接字并将其连接到指定IP指定端口号       |
  | Socket(String host, int port)        | 创建流套接字并将其连接到指定主机上的指定端口号 |

- 相关方法

  | 方法名                         | 说明                 |
  | ------------------------------ | -------------------- |
  | InputStream  getInputStream()  | 返回此套接字的输入流 |
  | OutputStream getOutputStream() | 返回此套接字的输出流 |

- 示例代码

  ```java
  public class Client {
      public static void main(String[] args) throws IOException {
          //TCP协议，发送数据
  
          //1.创建Socket对象
          //细节：在创建对象的同时会连接服务端
          //      如果连接不上，代码会报错
          Socket socket = new Socket("127.0.0.1",10000);
  
          //2.可以从连接通道中获取输出流
          OutputStream os = socket.getOutputStream();
          //写出数据
          os.write("aaa".getBytes());
  
          //3.释放资源
          os.close();
          socket.close();
      }
  }
  ```

### TCP接收数据

- 构造方法

  | 方法名                 | 说明                             |
  | ---------------------- | -------------------------------- |
  | ServerSocket(int port) | 创建绑定到指定端口的服务器套接字 |

- 相关方法

  | 方法名          | 说明                           |
  | --------------- | ------------------------------ |
  | Socket accept() | 监听要连接到此的套接字并接受它 |

- 示例代码

  ```java
  public class Server {
      public static void main(String[] args) throws IOException {
          //TCP协议，接收数据
  
          //1.创建对象ServerSocker
          ServerSocket ss = new ServerSocket(10000);
  
          //2.监听客户端的链接
          Socket socket = ss.accept();
  
          //3.从连接通道中获取输入流读取数据
          InputStream is = socket.getInputStream();
          int b;
          while ((b = is.read()) != -1){
              System.out.println((char) b);
          }
  
          //4.释放资源
          socket.close();
          ss.close();
      }
  }
  ```

# 十四 反射

## 反射

反射机制可以用来：
1、在运行时分析类的能力。
2、在运行时查看对象， 例如， 编写一个 toString 方法供所有类使用。
3、实现通用的数组操作代码。
4、利用 Method 对象， 这个对象很像中的函数指针。

### 获取Class 类

  1、源代码阶段获取：Class.forName("全类名")
  2、加载阶段获取：类.class
  3、运行阶段：对象.getClass()
  NOTE：newlnstance 方法对应 C++ 中虚拟构造器的习惯用法。 然而，C++ 中的虚拟构造器不是一种语言特性， 需要由专门的库支持。 Class 类与 C++ 中的 typejnfo 类相似，getClass 方法与 C++ 中的 typeid 运算符等价。 但 Java 中的 Class 比 C++ 中的 type_info的功能强。C++ 中的 typejnfo 只能以字符串的形式显示一个类型的名字， 而不能创建那个类型的对象

### 反射主要使用的种类

  Class类	代表类的实体，在运行的Java应 用程序中表示类和接口
  Field类	代表类的成员变量(成员变量也称为类的属性)
  Method类	代表类的方法
  Constructor类	代表类的构造方法
  Annotation类	代表类的注解

Note：getDeclareFields、 getDeclareMethods 和 getDeclaredConstructors 方法将分别返回类中声明的全部域、 方法和构造器， 其中包括私有和受保护成员，但不包括超类的成员。

### 通过反射实例化对象

  对象.newInstance()	

### 使用反射编写泛型数组代码？？？

### 调用任意方法

  Object invoke(Object obj, Object... args)
  其中Object obj为隐式传参（传一个实例化对象），Object... args为方法的显示传参

### 隐式传参和显示传参

  隐式参数：是调用函数的对象。
  显示参数：是通常所说的参数在方法名括号中间的参数,就是所谓能看得见的参数。

### Class.newinstance和Constructor.newinstance

  Class.newinstance只能调用public的构造函数

  Constructor.newinstance可以调用private的构造函数，但是需要通过setAccessible(true)实现

# 十五 命名规范

## **包命名规范**

包名使用小写英文字母进行命名，并使用“.”进行分割，每个被分割的单元只能包含一个名词。

一般地，包命名常采用顶级域名作为前缀，例如com，net，org，edu，gov，cn，io等，随后紧跟公司/组织/个人名称以及功能模块名称。

## **类命名规范**

类(Class)通常采用名词进行命名，且首字母**大写**，如果一个类名包含两个以上名词，建议使用驼峰命名(Camel-Case)法书写类名,每个名词首字母也应该大写。

### **接口命名规范**

简单来说，接口也是类(不太严谨)，所以，接口的名称的书写也应该符合类名书写规范，首字母应该大写，与普通类名不同的是，接口命名时通常采用形容词或动词来描述接口的动作行为。

### 抽象类命名规范

命名抽象类时，会以“Abstract”/“Base”作为类名的前缀。

### 异常类命名规范

异常类在命名时需要使用“Exception”作为其后缀

### 接口实现类命名规范

为了便于阅读，在通常情况下，建议接口实现类使用“Impl作为后缀”。

## 方法命名规范

方法(Method)命名时,其首字母应该**小写**，如果方法签名由多个单词组成，则从第二个单词起，使用驼峰命名法进行书写

### **表述条件**

如果一个方法需要一些条件参数，则可以使用“by”/“with”等字符作为方法名中条件的连接符，例如：

### 表述设置

如果一个方法是要增（插入insert）、删（删除delete），改（设置set，修改update），查（获取get，查询find/query）等操作，应该将对应的动词作为其名词的前缀

### 其他规范

如果一个方法用于获取某组数据的长度或数量，则该方法应该使用length或size命名；

如果方法的返回值为布尔类型(Boolean)，则该方法应该使用“is”或”has”作为前缀；

如果方法用于将一种类型的数据转换为另一种数据数类型，则可以使用“to”作为前缀。

## 变量命名规范

变量命名通常以小写字母开头，如果变量名由多个单词构成，则从第二个单词起首字母需要大写，

### 数组

- []型：名字+Array

- Map集合：名字+Map；

- 其他数组：名字的复数形式

## 常量命名规范

一般地，常量名称采用全部大写的英文单词书写，如果常量名称由多个单词组成，则单词之间统一使用“_”进行分割

## 枚举命名规范

枚举其命名规范遵循普通类的命名约束条件，首字母大写，采用驼峰命名法；枚举类中定义的值的名称遵循常量的命名规范

## 泛型类约定命名

在书写泛型类时，通常做以下的约定：

- E表示Element，通常用在集合中；
- ID用于表示对象的唯一标识符类型
- T表示Type(类型)，通常指代类；
- K表示Key(键),通常用于Map中；
- V表示Value(值),通常用于Map中，与K结对出现；
- N表示Number,通常用于表示数值类型；
- ？表示不确定的Java类型；
- X用于表示异常；
- U,S表示任意的类型

# 十六 JDK高级特性

## JDK9

1、不可变集合

为所有集合（List/Set/Map）增加了`of`和`copyOf`方法，用于创建不可变集合

2、改进Stream

Stream 接口中添加了 4 个新的方法：dropWhile, takeWhile, ofNullable。还有个 iterate 方法的新重载方法，可以让你提供一个 Predicate (判断条件)来指定什么时候结束迭代

3、允许接口使用私有的默认方法

## JDK10

1、var类型自动推导

## JDK11

1、字符串增强

```java
// 判断字符串是否为空白
 
" ".isBlank(); // true
 
// 去除首尾空白
 
" Javastack ".strip(); // "Javastack"
 
// 去除尾部空格
 
" Javastack ".stripTrailing(); // " Javastack"
 
// 去除首部空格
 
" Javastack ".stripLeading(); // "Javastack "
 
// 复制字符串
 
"Java".repeat(3);// "JavaJavaJava"
 
// 行数统计
 
"A\nB\nC".lines().count(); // 3
```

## JDK12

1、Switch表达式（Switch Expressions）： 引入了新的switch表达式，允许switch语句作为表达式使用，从而减少冗余代码。

```java
int day = 2;
String dayType = switch (day) {
    case 1, 2, 3, 4, 5 -> "Weekday";
    case 6, 7 -> "Weekend";
    default -> "Invalid";
};
```

## JDK13

1、Switch表达式增强： 在JDK 12中引入的switch表达式在JDK 13中进一步增强，允许使用yield关键字来返回值。

```java
int result = switch (day) {
    case 1, 2, 3, 4, 5 -> {
        System.out.println("Weekday");
        yield 42;
    }
    case 6, 7 -> {
        System.out.println("Weekend");
        yield 0;
    }
    default -> throw new IllegalArgumentException("Invalid");
};
```

## JDK14

Records（记录类）： 引入了记录类，这是一种新的类类型，用于声明不可变的、自动实现equals()、hashCode()和toString()等方法的数据对象。

```java
record Point(int x, int y) { }
```

## JDK15

1. Sealed Classes（封闭类）：

   引入了封闭类，这是一种新的类类型，允许类明确指定允许继承的子类。

   ```java
   sealed class Shape permits Circle, Rectangle, Triangle {
       // ...
   }
   ```



