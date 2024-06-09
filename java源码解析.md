# HashMap

## 继承关系

HashMap继承了AbstractMap，实现了Map、**Cloneable**和Serializable

## 存储结构

JDK1.8之前HashMap底层是**数组+链表**结合而成的高级数据结构，即 链表散列

JDK1.8之后，在链表长度大于8且容量>=64,就会进行树化，即链表转红黑树。

## Field

```
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; //默认的初始容量为16

static final int MAXIMUM_CAPACITY = 1 << 30;//最大的容量为2的30次方s

static final float DEFAULT_LOAD_FACTOR = 0.75f;//默认的装载因子

static final int TREEIFY_THRESHOLD = 8;//当一个桶中的元素个数大于等于8时进行树化

static final int UNTREEIFY_THRESHOLD = 6;//桶的个数达到64的时候才进行树化

static final int MIN_TREEIFY_CAPACITY = 64;//桶的个数达到64的时候才进行树化

transient Node<K,V>[] table;//存储元素的数组，总是2的幂次倍

transient Set<Map.Entry<K,V>> entrySet;
transient int size;

transient int modCount;//每次扩容和更改map结构的计数器

int threshold;//临界值,等于capacity*loadFactor

final float loadFactor;//加载因子  
```

## Node结点（链表）

static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        V value;
        // 指向下一个节点
        Node<K,V> next;

        Node(int hash, K key, V value, Node<K,V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }
    
        public final K getKey() 
        public final V getValue()    
        public final String toString() 
    	// 重写hashCode()方法
        public final int hashCode() 
        public final V setValue(V newValue)
    	// 重写 equals() 方法
        public final boolean equals(Object o)
    }
## TreeNode（红黑树节点）

继承了LinkedHashMap.Entry<K,V>类

    static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
            TreeNode<K,V> parent;  // red-black tree links 父
            TreeNode<K,V> left;//左
            TreeNode<K,V> right;//右
            // 在删除元素的时候可以快速找到它的前置节点
            TreeNode<K,V> prev;    // needed to unlink next upon deletion 
            boolean red;//颜色判断
            TreeNode(int hash, K key, V val, Node<K,V> next)//调用父类的构造
            {
                super(hash, key, val, next);
            }    
    		方法略
    }
## 构造函数

	/// 指定“容量大小”和“加载因子”的构造函数
	public HashMap(int initialCapacity, float loadFactor) {
		//初始容量合法性校验
	    if (initialCapacity < 0)
	        throw new IllegalArgumentException("Illegal initial capacity: " +
	                                           initialCapacity);
	    if (initialCapacity > MAXIMUM_CAPACITY)
	        initialCapacity = MAXIMUM_CAPACITY;
	    //装载因子合法性校验
	    if (loadFactor <= 0 || Float.isNaN(loadFactor))
	        throw new IllegalArgumentException("Illegal load factor: " +
	                                           loadFactor);
	    this.loadFactor = loadFactor;
	    // 扩容门槛计算
	    this.threshold = tableSizeFor(initialCapacity);//threshold 计算方式，传入的初始容量往上取最近的2的n次方
	}
	//传入默认装载因子
	public HashMap(int initialCapacity) {
	    this(initialCapacity, DEFAULT_LOAD_FACTOR);
	}
	//  默认无参构造方法， 所有属性使用默认值
	public HashMap() {
	    this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
	}
	//拷贝构造
	public HashMap(Map<? extends K, ? extends V> m) {
	    this.loadFactor = DEFAULT_LOAD_FACTOR;
	    putMapEntries(m, false);
	}

## put方法

```java
//参数一：键的哈希值
//参数二：键
//参数三：值
//参数四：如果键重复了是否保留
//       true，表示老元素的值保留，不会覆盖
//       false，表示老元素的值不保留，会进行覆盖
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,boolean evict) {
       //定义一个局部变量，用来记录哈希表中数组的地址值。
        Node<K,V>[] tab;
      
      //临时的第三方变量，用来记录键值对对象的地址值
        Node<K,V> p;
        
      //表示当前数组的长度
      int n;
      
      //表示索引
        int i;
      
      //把哈希表中数组的地址值，赋值给局部变量tab
      tab = table;
-----------------------------------------------------------------------------------
        if (tab == null || (n = tab.length) == 0){
         //1.如果当前是第一次添加数据，底层会创建一个默认长度为16，加载因子为0.75的数组
         //2.如果不是第一次添加数据，会看数组中的元素是否达到了扩容的条件
         //如果没有达到扩容条件，底层不会做任何操作
         //如果达到了扩容条件，底层会把数组扩容为原先的两倍，并把数据全部转移到新的哈希表中
         tab = resize();
         //表示把当前数组的长度赋值给n
            n = tab.length;
        }
-----------------------------------------------------------------------------------
      //因为n=2^n,所以(n - 1) & hash等于 hash % n
      i = (n - 1) & hash;//index
-----------------------------------------------------------------------------------     
      //获取数组中对应元素的数据
      p = tab[i];
      
      
        if (p == null){
         //底层会创建一个键值对对象，直接放到数组当中
            tab[i] = newNode(hash, key, value, null);
        }else {
            Node<K,V> e;
            K k;
         
         //等号的左边：数组中键值对的哈希值
         //等号的右边：当前要添加键值对的哈希值
         //如果键不一样，此时返回false
         //如果键一样，返回true
         boolean b1 = p.hash == hash;
         
            if (b1 && ((k = p.key) == key || (key != null && key.equals(k)))){
                e = p;
            } else if (p instanceof TreeNode){
            //判断数组中获取出来的键值对是不是红黑树中的节点
            //如果是，则调用方法putTreeVal，把当前的节点按照红黑树的规则添加到树当中。
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            } else {
            //如果从数组中获取出来的键值对不是红黑树中的节点
            //表示此时下面挂的是链表
                for (int binCount = 0; ; ++binCount) {
                    if ((e = p.next) == null) {
                  //此时就会创建一个新的节点，挂在下面形成链表
                        p.next = newNode(hash, key, value, null);
                  //判断当前链表长度是否超过8，如果超过8，就会调用方法treeifyBin
                  //treeifyBin方法的底层还会继续判断
                  //判断数组的长度是否大于等于64
                  //如果同时满足这两个条件，就会把这个链表转成红黑树
                        if (binCount >= TREEIFY_THRESHOLD - 1)
                            treeifyBin(tab, hash);
                        break;
                    }
               //e：           0x0044  ddd  444
               //要添加的元素： 0x0055   ddd   555
               //如果哈希值一样，就会调用equals方法比较内部的属性值是否相同
                    if (e.hash == hash && ((k = e.key) == key || (key != null && key.equals(k)))){
                   break;
               }

                    p = e;
                }
            }
------------------------------------------------------------------------------         
         //如果e为null，表示当前不需要覆盖任何元素
         //如果e不为null，表示当前的键是一样的，值会被覆盖
         //e:0x0044  ddd  555
         //要添加的元素： 0x0055   ddd   555
            if (e != null) {
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null){
               
               //等号的右边：当前要添加的值
               //等号的左边：0x0044的值
               e.value = value;
            }
                afterNodeAccess(e);
                return oldValue;
            }
        }
---------------------------------------------------------------------------      
        //threshold：记录的就是数组的长度 * 0.75，哈希表的扩容时机  16 * 0.75 = 12
        if (++size > threshold){
          resize();
      }
        
      //表示当前没有覆盖任何元素，返回null
        return null;
    }
```

- 扩容
  - 如果当前是第一次添加数据，底层会创建一个默认长度为16，加载因子为0.75的数组
  - 如果不是第一次添加数据，会看数组中的元素是否达到了扩容的条件（大于容量*负载因子）
    - 如果没有达到扩容条件，底层不会做任何操作
    - 如果达到了扩容条件，底层会把数组扩容为原先的两倍，并把数据全部转移到新的哈希表中
  
- 根据hash查找对应桶的位置

- 插入桶中
  - 数组位置为null，直接插入
  - 数组位置不为null，键不重复
    - 桶中存的是树结点
      - 插入红黑树
      - 如果有键值冲突，结束循环
    - 桶中存的是Node结点（链表）
      - 插入链表
      - 如果有键值冲突，结束循环
      - 是否需要扩容为红黑树（如果当前链表长度是否超过8，数组的长度大于等于64）
  
- 如果有键值冲突
  - 如果需要替换旧值
    - 替换旧值（只替换值，不换节点）
  - 返回旧值
  
- 如果没有键值冲突
  
  - 是否需要扩容
  
  - 返回null

# TreeMap

## 继承关系

HashMap继承了AbstractMap，实现了NavigableMap、**Cloneable**和Serializable

## 存储结构

TreeMap的存储结构为红黑树

NOTE：红黑树的规则

1. 每一个节点或是红色的,或者是黑色的

2. 根节点必须是黑色

3. 如果一个节点没有子节点或者父节点,则该节点相应的指针属性值为Nil,这些Nil视为叶节点,每个叶节点(Nil)是黑色的

4. 如果某一个节点是红色,那么它的子节点必须是黑色(不能出现两个红色节点相连 的情况)

5. 对每一个节点,从该节点到其所有后代叶节点的简单路径上,均包含相同数目的黑色节点

## Field

```
//比较器对象
   private final Comparator<? super K> comparator;

//根节点
   private transient Entry<K,V> root;

//集合的长度
   private transient int size = 0;
```

## Entry<K,V>节点（红黑树节点）

继承了Map.Entry

```
  K key;             //键
  V value;            //值
  Entry<K,V> left;      //左子节点
  Entry<K,V> right;     //右子节点
  Entry<K,V> parent;    //父节点
  boolean color;       //节点的颜色
```

## 构造函数

```
空参构造
   //空参构造就是没有传递比较器对象
    public TreeMap() {
        comparator = null;
    }
   
   
   
带参构造
   //带参构造就是传递了比较器对象。
   public TreeMap(Comparator<? super K> comparator) {
        this.comparator = comparator;
    }
```

## put方法

```
    public V put(K key, V value) {
        return put(key, value, true);
    }

参数一：键
参数二：值
参数三：当键重复的时候，是否需要覆盖值
      true：覆盖
      false：不覆盖
      
   private V put(K key, V value, boolean replaceOld) {
      //获取根节点的地址值，赋值给局部变量t
        Entry<K,V> t = root;
      //判断根节点是否为null
      //如果为null，表示当前是第一次添加，会把当前要添加的元素，当做根节点
      //如果不为null，表示当前不是第一次添加，跳过这个判断继续执行下面的代码
        if (t == null) {
         //方法的底层，会创建一个Entry对象，把他当做根节点
            addEntryToEmptyMap(key, value);
         //表示此时没有覆盖任何的元素
            return null;
        }
      //表示两个元素的键比较之后的结果
        int cmp;
      //表示当前要添加节点的父节点
        Entry<K,V> parent;
      
      //表示当前的比较规则
      //如果我们是采取默认的自然排序，那么此时comparator记录的是null，cpr记录的也是null
      //如果我们是采取比较去排序方式，那么此时comparator记录的是就是比较器
        Comparator<? super K> cpr = comparator;
      //表示判断当前是否有比较器对象
      //如果传递了比较器对象，就执行if里面的代码，此时以比较器的规则为准
      //如果没有传递比较器对象，就执行else里面的代码，此时以自然排序的规则为准
        if (cpr != null) {
            do {
                parent = t;
                cmp = cpr.compare(key, t.key);
                if (cmp < 0)
                    t = t.left;
                else if (cmp > 0)
                    t = t.right;
                else {
                    V oldValue = t.value;
                    if (replaceOld || oldValue == null) {
                        t.value = value;
                    }
                    return oldValue;
                }
            } while (t != null);
        } else {
         //把键进行强转，强转成Comparable类型的
         //要求：键必须要实现Comparable接口，如果没有实现这个接口
         //此时在强转的时候，就会报错。
            Comparable<? super K> k = (Comparable<? super K>) key;
            do {
            //把根节点当做当前节点的父节点
                parent = t;
            //调用compareTo方法，比较根节点和当前要添加节点的大小关系
                cmp = k.compareTo(t.key);
            
                if (cmp < 0)
               //如果比较的结果为负数
               //那么继续到根节点的左边去找
                    t = t.left;
                else if (cmp > 0)
               //如果比较的结果为正数
               //那么继续到根节点的右边去找
                    t = t.right;
                else {
               //如果比较的结果为0，会覆盖
                    V oldValue = t.value;
                    if (replaceOld || oldValue == null) {
                        t.value = value;
                    }
                    return oldValue;
                }
            } while (t != null);
        }
      //就会把当前节点按照指定的规则进行添加
        addEntry(key, value, parent, cmp < 0);
        return null;
    }  
```

   ### addEntry方法

   ```
    private void addEntry(K key, V value, Entry<K, V> parent, boolean addToLeft) {
        Entry<K,V> e = new Entry<>(key, value, parent);
        if (addToLeft)
            parent.left = e;
        else
            parent.right = e;
      //添加完毕之后，需要按照红黑树的规则进行调整
        fixAfterInsertion(e);
        size++;
        modCount++;
    }
   ```

   #### fixAfterInsertion方法

   ```
   private void fixAfterInsertion(Entry<K,V> x) {
      //因为红黑树的节点默认就是红色的
        x.color = RED;

      //按照红黑规则进行调整
      
      //parentOf:获取x的父节点
      //parentOf(parentOf(x)):获取x的爷爷节点
      //leftOf:获取左子节点
        while (x != null && x != root && x.parent.color == RED) {
         
         
         //判断当前节点的父节点是爷爷节点的左子节点还是右子节点
         //目的：为了获取当前节点的叔叔节点
            if (parentOf(x) == leftOf(parentOf(parentOf(x)))) {
            //表示当前节点的父节点是爷爷节点的左子节点
            //那么下面就可以用rightOf获取到当前节点的叔叔节点
                Entry<K,V> y = rightOf(parentOf(parentOf(x)));
                if (colorOf(y) == RED) {
               //叔叔节点为红色的处理方案
               
               //把父节点设置为黑色
                    setColor(parentOf(x), BLACK);
               //把叔叔节点设置为黑色
                    setColor(y, BLACK);
               //把爷爷节点设置为红色
                    setColor(parentOf(parentOf(x)), RED);
               
               //把爷爷节点设置为当前节点
                    x = parentOf(parentOf(x));
                } else {
               
               //叔叔节点为黑色的处理方案
               
               
               //表示判断当前节点是否为父节点的右子节点
                    if (x == rightOf(parentOf(x))) {
                  
                  //表示当前节点是父节点的右子节点
                        x = parentOf(x);
                  //左旋
                        rotateLeft(x);
                    }
                    setColor(parentOf(x), BLACK);
                    setColor(parentOf(parentOf(x)), RED);
                    rotateRight(parentOf(parentOf(x)));
                }
            } else {
            //表示当前节点的父节点是爷爷节点的右子节点
            //那么下面就可以用leftOf获取到当前节点的叔叔节点
                Entry<K,V> y = leftOf(parentOf(parentOf(x)));
                if (colorOf(y) == RED) {
                    setColor(parentOf(x), BLACK);
                    setColor(y, BLACK);
                    setColor(parentOf(parentOf(x)), RED);
                    x = parentOf(parentOf(x));
                } else {
                    if (x == leftOf(parentOf(x))) {
                        x = parentOf(x);
                        rotateRight(x);
                    }
                    setColor(parentOf(x), BLACK);
                    setColor(parentOf(parentOf(x)) , RED);
                    rotateLeft(parentOf(parentOf(x)));
                }
            }
        }
      
      //把根节点设置为黑色
        root.color = BLACK;
    }
   ```

- put方法，先找出当为二叉排序树时预计插入的位置的父节点

  - addEntry方法，插入对应父节点的右/左孩子

    - **fixAfterInsertion方法**，根据红黑规则进行调整

      - 根节点位置

        - 直接变为黑色

      - 非根节点位置

        - 父节点为黑色

          - 不需要任何操作,默认红色即可

        - 父节点为红色

          - 叔叔节点为红色

            1. 将"父节点"设为黑色,将"叔叔节点"设为黑色
            2. 将"祖父节点"设为红色
            3. 则将祖父节点设为当前节点，回到**fixAfterInsertion方法**，再次进行判断

          - 叔叔节点为黑色

            - （当前节点为父的右孩子），则将父设为当前节点并进行左旋

            - 将当前节点的"父节点"设为黑色

            - 将当前节点的"祖父节点"设为红色

            - 以当前节点的"祖父节点"为支点进行右旋

# ConcurrentHashMap

## 整体架构

与HashMap一致

## 保证并发安全

- jdk7将一个ConcurrentHashMap的table分割成多个小的table来进行加锁，每个小的table，分别用不同的segment进行加锁，共有16个segment，其中segment是一种可重入锁
- jdk8使用synchronized锁住对table数组的对应Node节点

## 对比HashMap

- HashMap多线程下是不安全的，扩容时可能会形成闭环

- HashMap的get/put所有相关操作都是synchronized，ConcurrentHashMap使用的是分段锁，容器中有多把锁，每一把锁锁一段数据，这样在多线程访问时不同段的数据时，就不会存在锁竞争了

## ConcurrentHashMap为什么put操作使用synchronized

- 因为使用其他如Reentrantlock等API锁，每个节点都需要通过继承AQS来获得同步支持，浪费内存
- synchronized则是JVM直接支持的，JVM能够在运行时作出相应的优化措施：锁粗化、锁消除、锁自旋等等。这就使得synchronized能够随着JDK版本的升级而不改动代码的前提下获得性能上的提升。

# PriorityQueue

## 继承关系

HashMap继承了AbstractQueue，实现了Serializable

## 存储结构

PriorityQueue使用最小(大)二叉堆来管理队列中的元素，元素的大小比较方法由用户指定的Comparator指定。

NOTE：最小二叉堆

最小二叉堆满足如下约束:

- 完全二叉树；
- 所有非叶子节点的根节点value均小于其左、右子节点的value。

## Field


```
private static final int DEFAULT_INITIAL_CAPACITY = 11;//默认容量大小，数组大小

transient Object[] queue; // 存放元素的数组

private int size = 0;

private final Comparator<? super E> comparator;//自定义的比较规则，有该规则时优先使用，否则使用元素实现的Comparable接口方法

transient int modCount = 0; 
```

## 构造函数

```
public PriorityQueue(int initialCapacity,
                     Comparator<? super E> comparator) {
    // Note: This restriction of at least one is not actually needed,
    // but continues for 1.5 compatibility
    if (initialCapacity < 1)
        throw new IllegalArgumentException();
    this.queue = new Object[initialCapacity];
    this.comparator = comparator;
}
其余略
```

## offer方法

```public boolean offer(E e) {
public boolean offer(E e) {
	// 若添加的元素为null，则直接抛出空指针异常
	if (e == null)
		throw new NullPointerException();
	// 操作数+1
	modCount++;
	int i = size;
	// 如果元素个数已大于或等于数组的长度，则执行扩容操作

	if (i >= queue.length)
		grow(i + 1);// 若现有容量<64，则新容量变为现有容量的2倍+2;若现有容量>64，则新容量变为现有容量的1.5倍
	// 将待添加元素添加到数组最后，并执行元素上移过程
	siftUp(i, e);
	// 元素添加完成后，元素个数+1
	size = i + 1;
	// 返回true，代表添加元素成功
	return true;
}
```

### siftUp方法（寻找上浮）

```
private void siftUp(int k, E x) {
	// 判断是否有传入的comparator
	// 若有则执行siftUpUsingComparator，否则执行siftUpComparable
	if (comparator != null)
		siftUpUsingComparator(k, x, queue, comparator);
	else
		siftUpComparable(k, x, queue);
}

//自然排序
private static <T> void siftUpComparable(int k, T x, Object[] es) {
	Comparable<? super T> key = (Comparable<? super T>) x;
	while (k > 0) {
		// 获取父节点的index=(k-1)/2
		int parent = (k - 1) >>> 1;
		// 拿到父节点的value
		Object e = es[parent];
		// 若当前节点value>=父节点，停止上移，直接break返回
		if (key.compareTo((T) e) >= 0)
			break;
		//父节点下沉，预期插入位置上浮
		es[k] = e;
		k = parent;
	}
	es[k] = key;
}

//比较器排序
// 过程基本与siftUpComparable相同，区别就是在元素上移的过程中，siftUpUsingComparator方法使用传入的Comparator来进行大小比较
private static <T> void siftUpUsingComparator(
	int k, T x, Object[] es, Comparator<? super T> cmp) {
	while (k > 0) {
		int parent = (k - 1) >>> 1;
		Object e = es[parent];
		if (cmp.compare(x, (T) e) >= 0)
			break;
		es[k] = e;
		k = parent;
	}
	es[k] = x;
}
```

- 扩容
  - 会看数组中的元素是否达到了容量上限
    - 若现有容量<64，则新容量变为现有容量的2倍+2
    - 若现有容量>64，则新容量变为现有容量的1.5倍
- 插入
  - 判断插入方式？自然排序：比较器排序
  - 将数据预期插入位置置于表尾
  - 进行上浮
    - 寻找父节点
    - 判断父与子的大小关系
      - 满足上浮条件
        - 父节点下沉
        - 预期插入位置上升
      - 不满足上浮条件
        - 退出，子节点插入预期插入位置，返回true

## peek方法

```
public E peek() {
	return (E) queue[0];
}
```

## poll方法
```
public E poll() {
	final Object[] es;
	final E result;
	// 若数组的首元素不为null
	if ((result = (E) ((es = queue)[0])) != null) {
		// 操作数+1
		modCount++;
		final int n;
		// 获取数组的末尾元素，并将数组大小-1
		final E x = (E) es[(n = --size)];
		// 将数组末尾置为null
		es[n] = null;
		if (n > 0) {
			final Comparator<? super E> cmp;
			// 若无传入的Comparator，则执行siftDownComparable
			// 若有传入的Comparator，则执行siftDownUsingComparator
			if ((cmp = comparator) == null)
				// 传参为0，说明从首元素开始下移
				siftDownComparable(0, x, es, n);
			else
				siftDownUsingComparator(0, x, es, n, cmp);
		}
	}
	return result;
}
```

### sigtDown方法（寻找下沉）

```
private static <T> void siftDownComparable(int k, T x, Object[] es, int n) {
	// assert n > 0;
	Comparable<? super T> key = (Comparable<? super T>)x;
	int half = n >>> 1;           // loop while a non-leaf
	// 从上到下，从左到右不断循环遍历非叶子节点
	while (k < half) {
		// 获取左子节点的index
		int child = (k << 1) + 1; // assume left child is least
		// 获取左子节点的value
		Object c = es[child];
		// 获取右子节点的index
		int right = child + 1;
		// 若右子节点存在，且右子节点的值小于左子节点，则将左右子节点的最小值更新为右子节点的值
		if (right < n &&
			((Comparable<? super T>) c).compareTo((T) es[right]) > 0)
			c = es[child = right];
		// 若该元素小于左、右子节点中的偏小者，停止下移，直接break
		if (key.compareTo((T) c) <= 0)
			break;
		// 否则，将该节点与左、右子节点中的偏小者调换，继续执行下移过程
		es[k] = c;
		k = child;
	}
	es[k] = key;
}

// 与siftDownComparable方法类似，只是元素下移的过程中使用了传入的Comparator来进行元素大小比较
private static <T> void siftDownUsingComparator(
	int k, T x, Object[] es, int n, Comparator<? super T> cmp) {
	// assert n > 0;
	int half = n >>> 1;
	while (k < half) {
		int child = (k << 1) + 1;
		Object c = es[child];
		int right = child + 1;
		if (right < n && cmp.compare((T) c, (T) es[right]) > 0)
			c = es[child = right];
		if (cmp.compare(x, (T) c) <= 0)
			break;
		es[k] = c;
		k = child;
	}
	es[k] = x;
}

```

末尾数据重插入

- 判断插入方式？自然排序：比较器排序
- 将末尾数据预期插入位置置于表头
- 进行上浮
  - 寻找父节点
  - 寻找左右孩子中较小的节点
  - 判断父与较小孩子的大小关系
    - 满足下沉条件
      - 较小孩子节点上升
      - 预期插入位置下沉到较小孩子节点位置
    - 不满足下沉条件
      - 退出，末尾数据插入预期插入位置，返回未操作之前的数组的表头

## initFromCollection方法（堆排序）

```
private void initFromCollection(Collection<? extends E> c) {
	// 拷贝元素创建初始堆
	initElementsFromCollection(c);
	// 将初始堆调整为最小(大)二叉堆
	heapify();
}

private void heapify() {
	final Object[] es = queue;
	// 获取最后1个非叶子节点的index=(n/2)-1
	int n = size, i = (n >>> 1) - 1;
	final Comparator<? super E> cmp;
	// 由最后1个非叶子节点开始往上遍历，逐渐执行节点的下移过程
	if ((cmp = comparator) == null)
		for (; i >= 0; i--)
			siftDownComparable(i, (E) es[i], es, n);
	else
		for (; i >= 0; i--)
			siftDownUsingComparator(i, (E) es[i], es, n, cmp);
}
```

