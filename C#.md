# 可空类型（?）

```c#
 int? num1 = null;
 int num2 = 45;
```

C# 提供了一个特殊的数据类型，nullable 类型（可空类型），可空类型可以表示其基础值类型正常范围内的值，再加上一个 null 值。

# Null 合并运算符（ ?? ）

num2 = num1??123; <==>num2 = num1 == null  ? 123 : num1;

# 密封类(sealed)

阻止该类被继承

# 特性（Attribute）

特性（Attribute）是声明性标签

### AttributeUsage

预定义特性 AttributeUsage 描述了如何使用一个自定义特性类。它规定了特性可应用到的项目的类型。

规定该特性的语法如下：

```
[AttributeUsage(
   validon,
   AllowMultiple=allowmultiple,
   Inherited=inherited
)]
```

其中：

- 参数 validon 规定特性可被放置的语言元素。它是枚举器 *AttributeTargets* 的值的组合。默认值是 *AttributeTargets.All*。
- 参数 *allowmultiple*（可选的）为该特性的 *AllowMultiple* 属性（property）提供一个布尔值。如果为 true，则该特性是多用的。默认值是 false（单用的）。
- 参数 *inherited*（可选的）为该特性的 *Inherited* 属性（property）提供一个布尔值。如果为 true，则该特性可被派生类继承。默认值是 false（不被继承）。

### Conditional

这个预定义特性标记了一个条件方法，其执行依赖于指定的预处理标识符

```java
[Conditional(
   conditionalSymbol
)]
```

例如：

```java
[Conditional("DEBUG")]//对应必须使用#define DEBUG
```

### Obsolete

该方法以过时，但是仍然可以使用

# 访问器（accessor）

C#不允许外界直接对类的成员变量直接访问的，既然不能访问，那定义这些成员变量还有什么意义呢？所以C#中就要用set和get方法来访问私有成员变量，它们相当于外界访问对象的一个通道，一个“接口”

# 索引器（Indexer）

索引器（Indexer） 允许一个对象可以像数组一样使用下标的方式来访问。(可认为是对[]的重载)

一维索引器的语法如下：

```C#
return-type this[type index]
{
  // get 访问器
  get
  {
   // 返回 index 指定的值
  }

  // set 访问器
  set
  {
   // 设置 index 指定的值
  }
}
```

# 委托

将一个函数生成函数指针对象

```c#
delegate int NumberChanger(int n);//声明委托
namespace DelegateAppl
{
  class TestDelegate
  {
   static int num = 10;
   public static int AddNum(int p)
   {
     num += p;
     return num;
   }

   public static int MultNum(int q)
   {
     num *= q;
     return num;
   }

   static void Main(string[] args)
   {
     *// 创建委托实例*
     NumberChanger nc1 = new NumberChanger(AddNum);
     NumberChanger nc2 = new NumberChanger(MultNum);
     
     *// 使用委托对象调用方法*
     nc1(25);
     Console.WriteLine("Value of Num: {0}", getNum());
     nc2(5);
     Console.WriteLine("Value of Num: {0}", getNum());
     Console.ReadKey();
   }
  }
}
```

# 事件

```C#
using System;
namespace SimpleEvent
{
  using System;
  /***********发布器类***********/
  public class EventTest//发布器（publisher） 是一个包含事件和委托定义的对象。事件和委托之间的联系也定义在这个对象中。发布器（publisher）类的对象调用这个事件，并通知其他的对象。
  {
    private int value;

    public delegate void NumManipulationHandler();


    public event NumManipulationHandler ChangeNum;
    protected virtual void OnNumChanged()
    {
      if ( ChangeNum != null )
      {
        ChangeNum(); /* 事件被触发 */
      }else {
        Console.WriteLine( "event not fire" );
        Console.ReadKey(); /* 回车继续 */
      }
    }


    public EventTest()
    {
      int n = 5;
      SetValue( n );
    }


    public void SetValue( int n )
    {
      if ( value != n )
      {
        value = n;
        OnNumChanged();
      }
    }
  }


  /***********订阅器类***********/

  public class subscribEvent//订阅器（subscriber） 是一个接受事件并提供事件处理程序的对象。在发布器（publisher）类中的委托调用订阅器（subscriber）类中的方法（事件处理程序），订阅器（subscriber）类中的方法应与发布器中的委托一致
  {
    public void printf()
    {
      Console.WriteLine( "event fire" );
      Console.ReadKey(); /* 回车继续 */
    }
  }

  /***********触发***********/
  public class MainClass
  {
    public static void Main()
    {
      EventTest e = new EventTest(); /* 实例化对象,第一次没有触发事件 */
      subscribEvent v = new subscribEvent(); /* 实例化对象 */
      e.ChangeNum += new EventTest.NumManipulationHandler( v.printf ); /* 注册 */
      e.SetValue( 7 );
      e.SetValue( 11 );
    }
  }
}
```

# 泛型

```C#
using System;
using System.Web.Caching;

namespace Demo.CacheManager
{
   public class CacheHelper<T> where T:new()
   {
      
   }
}
```

泛型限定条件：

-  T：结构（类型参数必须是值类型。可以指定除 Nullable 以外的任何值类型）
-  T：类 （类型参数必须是引用类型，包括任何类、接口、委托或数组类型）
-  T：new() （类型参数必须具有无参数的公共构造函数。当与其他约束一起使用时new() 约束必须最后指定）
-  T：<基类名> 类型参数必须是指定的基类或派生自指定的基类
-  T：<接口名称> 类型参数必须是指定的接口或实现指定的接口。可以指定多个接口约束。约束接口也可以是泛型的。
-  T：U