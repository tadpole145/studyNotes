https://www.bilibili.com/video/BV184411x7XA?from=search&seid=3554401513523942997

#### Lambda表达式

##### 介绍

Lambda是一个匿名函数, 可以理解为一段可以传递的代码, 使用它可以写出更简单灵活的代码.

Lambda只能作用于只有一个方法的接口类或者抽象类

##### 语法规则

(parameters) -> expression 或 (parameters) ->{ statements; }

-  左边:  参数类型均可以省略(类型推断),
	-  单参数 ,可以省略小括号
	- 无参数,或者多参数,不可以省略小括号
- 右边,lambda体应该有{}包裹
	- 单条执行语句, 可省略{} 和return关键字
	- 多条执行语句, {}+return关键字都不可以省略

##### 代码示例

```java
 Runnable r0 = new Runnable() {
            @Override
            public void run() {
                System.out.println("无参,无返回值");
            }
        };
        r0.run();

        Runnable r1 = () -> System.out.println("Lambda----无参,无返回值");
        r1.run();

        System.out.println("--------------------------分割线-------------------------------");
        //格式二, 单参数+无返回值
        Consumer<String> con0 = new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        };
        con0.accept("单参数+无返回值");

        Consumer<String> con1 = (String s) -> System.out.println(s);
        con1.accept("Lambda--->单参数+无返回值");
        System.out.println("---------------------------分割线------------------------------");

        //格式三, 单参数+类型推断+无返回值


        Consumer<String> con2 = (s) -> System.out.println(s);
        con2.accept("Lambda--->单参数+类型推断+无返回值");
        System.out.println("---------------------------分割线------------------------------");

        //格式四, 单参数+无返回值, 可省略参数的小括号

        Consumer<String> con3 = s -> System.out.println(s);
        con3.accept("Lambda--->单参数+类型推断+无返回值");
        System.out.println("---------------------------分割线------------------------------");

        //格式五, 多参数+有返回值+多执行语句
        Comparator<Integer> c1 = new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                System.out.println("参数o1,o2分别为" + o1 + "," + o2);
                return o1 - o2;
            }
        };
        c1.compare(10, 20);
        Comparator<Integer> c2 = (o1, o2) -> {
            System.out.println("lambda-->参数o1,o2分别为" + o1 + "," + o2);
            return o1 - o2;
        };
        c2.compare(10, 20);
        System.out.println("---------------------------分割线------------------------------");
        //格式六, 多参数+有返回值+单执行语句
        Comparator<Integer> c3 = (o1, o2) -> o1 - o2;
        c3.compare(10, 20);
```



#### 函数式接口

##### 定义

只包含一个抽象方法的接口,成为函数式接口.

##### 自定义函数式接口

```java
//只能包含一个方法
@FunctionalInterface
public interface MyFuncInterface {
    void test();
     
}
```

lambda的本质就是作为函数式接口的一个实例,能简化代码

##### java内置的四大函数式接口

- 消费型接口:      Consumer<T>     方法:  void accept(T t);
- 供应型接口:       Supplier<T>       方法:   T  get();
- 函数型接口:       Function<T, R>    方法  R  apply(T t) ;
- 断定型接口:       Predicate<T>      方法  boolean  test(T t);

总结起来,就是是否有参,是否有返回值,返回值是否为boolean型的组合.此外还有很多根据上述四种变形而来的接口

![image-20210805100214926](https://gitee.com/tadpole145/images/raw/main/20210805100215.png)

##### 代码示例

```java
public class LambdaDemo2 {

    @Test
    public void test(){
        get(500,money-> System.out.println("今天搬砖赚了"+money));
    }


    public void getMoney(double money, Consumer<Double> consumer){
        consumer.accept(money);
    }
}
```



#### 方法引用/构造器引用

##### 是什么

可以理解为Lambda表达式的一个语法糖,通过方法的名字来指向一个方法,使表达更简单.

PS:  虽然表达更精简了,但是可读性实在很差....

##### 基本语法

类(或对象) :: 方法名  ,  具体来说,分为以下3种

- 对象 :: 实例方法名
- 类 :: 静态方法名
- 类 :: 实例方法名

##### 使用前提

实现抽象方法的参数列表和返回值类型，必须与方法引用的方法
的参数列表和返回值类型保持一致！

##### 代码示例

```java
  //匿名类写法
        Consumer<String> con0 = new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        };
        con0.accept("单参数+无返回值");
		//Lambda表达式写法
        Consumer<String> con1 = (String s) -> System.out.println(s);
        con1.accept("Lambda--->单参数+无返回值");
        System.out.println("---------------------------分割线------------------------------");
        Consumer<String> con2 = System.out::println;
		con2..accept("方法引用--->单参数+无返回值");

//这段示例代码中, 实现抽象方法为accept(String s), 返回值为void;
//方法引用的方法为println(String s), 返回值也是void, 2者都一致,符合方法引用的前提条件,才可以使用方法引用.
```

PS: 个人写代码的时候,如果可以用方法引用替代Lambda,IDEA会提示,没必要自己刻意写成方法引用模式, 可以不用,但是必须要能看得懂...语法糖虽然看起来简洁了,但是可读性差,需要时间去适应.

##### 构造器引用

构造方法其实也是一种特殊的方法, 跟方法引用类似, 其语法规则如下:

类 :: new

###### 使用前提

- 构造器参数列表要与接口中抽象方法的参数列表一致！

- 方法的返回值即为构造器对应类的对象。

###### 代码示例

```java
//匿名内写法
Function<Integer,Integer[]> function=new Function<Integer, Integer[]>() {
            @Override
            public Integer[] apply(Integer n) {
                return new Integer[n];
            }
        };
function.apply(2);
//Lambda写法
Function<Integer,Integer[]> fun1= n -> new Integer[n];
fun1.apply(3);
//构造函数引用写法
Function<Integer,Integer[]> fun2= Integer[]::new;
fun1.apply(4);

```





#### 强大的Stream API

##### 是什么

Stream 是 Java8 中用于操作数据源（集合、数组等）所生成的元素序列，可以执行非常复杂的查找、过滤和映射数据等操作。

- Stream 自己不会存储元素。
- Stream 不会改变源对象。相反，他们会返回一个持有结果的新Stream。
- Stream 操作是延迟执行的。这意味着他们会等到需要结果的时候才执行。

##### 为什么使用

Stream API可以极大提供Java程序员的生产力，让程序员写出高效率、干净、简洁的代码。

##### 操作步骤

1. 创建Stream,通过数据源

	- 通过集合,Java8 中的 Collection 接口被扩展，提供了两个获取流的方法

	```java
	default Stream<E> stream()// 返回一个顺序(串行)流
	default Stream<E> parallelStream() // 返回一个并行流
	    
	//并行流就是把一个内容(数组或集合)分成多个数据块，并用不同的线程分别处理每个数据块的流。这样一来，你就可以自动把给定操作的工作负荷分配给多核处理器的所有内核，让他们都忙起来。整个过程无需程序员显示实现优化。
	    
	 //示例
	List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);
	Stream<Integer> stream1 = list.stream();
	Stream<Integer> parallelStream = list.parallelStream();
	```

	- 通过数组,Java8 中的 Arrays 的静态方法 stream() 可以获取数组流

	```java
	 static <T> Stream<T> stream(T[] array)// 返回一个流
	 //示例
	int[] aar = {1, 2, 3, 4};
	IntStream stream = Arrays.stream(aar);
	```

	- 调用Stream类静态方法 of(), 通过显示值创建一个流。它可以接收任意数量的参数

	```java
	public static<T> Stream<T> of(T... values) //  返回一个流
	//示例
	Stream<String> stringStream = Stream.of("1", "2", "3", "4");
	```

	- 用静态方法 Stream.iterate() 和 Stream.generate(), 创建无限流。

	```java
	//创建10个以0开始的连续偶数
	 Stream.iterate(0, integer -> integer + 2).limit(10).forEach(System.out::println);
	//创建5个随机数
	Stream.generate(Math::random).limit(5).forEach(System.out::println);
	```

	

2. 中间操作,对数据源进行处理,返回的仍然是Stream

	- 筛选与切片,也就是根据条件过滤

	![image-20210805145528892](https://gitee.com/tadpole145/images/raw/main/20210805145529.png)

	- 映射

	![image-20210805153336295](https://gitee.com/tadpole145/images/raw/main/20210805153336.png)

	- 排序

	![image-20210805154158052](https://gitee.com/tadpole145/images/raw/main/20210805154158.png)

3. 终止操作,它执行才会执行中间操作链,产生结果

	- 匹配与查找

	![image-20210805155818051](https://gitee.com/tadpole145/images/raw/main/20210805155818.png)

	![image-20210805155854565](https://gitee.com/tadpole145/images/raw/main/20210805155854.png)

	- 规约

	![image-20210805163921933](https://gitee.com/tadpole145/images/raw/main/20210805163921.png)

	- 收集

![image-20210805164723310](https://gitee.com/tadpole145/images/raw/main/20210805164723.png)

 Collectors 实用类提供了很多静态方法，可以方便地创建常见收集器实例 

![image-20210805164943842](https://gitee.com/tadpole145/images/raw/main/20210805164943.png)



#### Optional类

##### 是什么

 是一个容器类，代表一个值存在或不存在，原来用 null 表示一个值不存在，现在 Optional 可以更好的表达这个概念。

##### 常用API

- Optional.empty() : 创建一个空的 Optional 实例
- Optional.of(T t) : 创建一个 Optional 实例
- Optional.ofNullable(T t): 若  t  不为  null, 创建  Optional  实例 , 否则创建空实例
- isPresent() : 判断是否包含值
- T get(): 如果调用对象包含值，返回该值，否则抛异常
- orElse(T t) :   如果调用对象包含值，返回该值，否则返回 t
- orElseGet(Supplier s) :如果调用对象包含值，返回该值，否则返回 s 获取的值
- map(Function f):  如果有值对其处理，并返回处理后的Optional，否则返回 Optional.empty()
- flatMap(Function mapper):与 map 类似，要求返回值必须是Optional



#### 接口增强

Java 8中，你可以为接口添加静态方法和默认方法。

##### 静态方法

使用 static 关键字修饰。可以通过接口直接调用静态方法，并执行其方法体。

##### 默认方法

使用 default 关键字修饰。可以通过实现类对象来调用。

若一个接口中定义了一个默认方法，而另外一个父类或接口中又定义了一个同名的方法时,

- 父类方法有实现, 则直接使用父类中的方法, 简称"类优先"
- 如果2个接口定义了相同的方法,则需要子类实现来解决冲突.



#### 新的时间与日期API

##### 以前时间日期存在的问题

JDK 1.0中包含了一个java.util.Date类，但是它的大多数方法已经在JDK 1.1引入Calendar类之后被弃用了. 它们面临的问题是:

- 可变性：像日期和时间这样的类应该是不可变的。
- 偏移性：Date中的年份是从1900开始的，而月份都从0开始。
- 格式化：格式化只对Date有用，Calendar则不行。
- 它们也不是线程安全的；
- 不能处理闰秒等。

##### 新时间日期API

- java.time – 包含值对象的基础包

```java
/** 常用类----本地时间
 *   LocalTime  : 本地时间
 *   LocalDate : 本地日期
 *   LocalDateTime : 本地时间日期   
 */

	//代码示例	
	@Test
    public void  test(){
        //获取当前时间
        LocalDateTime now = LocalDateTime.now();
        System.out.println(now);  //2021-08-06T09:24:25.172
        //指定时间
        LocalDateTime dateTime = LocalDateTime.of(2020, 5, 20, 13, 14, 20);
        System.out.println(dateTime);
        //对日期时间做加减
        System.out.println(dateTime.plusYears(1));
        System.out.println(dateTime.minusMonths(2));

    }
 /** 机器时间
 *   Instant :表示自1970年1月1日0时0分0秒（UTC--格林尼治时间）开始的秒数的对象,主要提供给计算机使用。
 */
// now()   静态方法，返回默认UTC时区的Instant类的对象

//ofEpochMilli(long epochMilli)  静态方法，返回在1970-01-01 00:00:00基础上加上指定毫秒数之后的Instant类的对象

//atOffset(ZoneOffset offset)  结合即时的偏移来创建一个 OffsetDateTime

//示例:  获取格林尼治时间以来的秒数/毫秒数, 等价于 System.currentTimeMillis()
Instant instant = Instant.now();
long second = instant.getEpochSecond();
long milli = instant.toEpochMilli();
System.out.println("second="+second+",  毫秒="+milli);

//时间计算类:  Duration, Duration
//Duration  基于时间的时间量，例如“34.5 秒”。   此类以秒和纳秒为单位对数量或时间进行建模
//Period  ISO-8601 日历系统中基于日期的时间量，例如“2 年、3 个月和 4 天”。   此类以年、月和日为单位对数量或时间进行建模


```



- java.time.chrono – 提供对不同的日历系统的访问

```java
//涉及到不同国家的不同日历系统,一般用不到.
```

- java.time.format – 格式化和解析时间和日期

```java
//使用api提供的格式化
DateTimeFormatter formatter = DateTimeFormatter.ISO_LOCAL_DATE_TIME;
LocalDateTime now = LocalDateTime.now();
System.out.println("格式化时间 = " + formatter.format(now));
//自定义时间格式
DateTimeFormatter formatter1 = DateTimeFormatter.ofPattern("yyyy年MM月dd日 HH:mm:ss");
String formatTime = formatter1.format(now);
System.out.println(formatTime);
//反向解析String类时间到原来格式
LocalDateTime parseTime = LocalDateTime.parse(formatTime, formatter1);
System.out.println("parseTime = " + parseTime);
```



- java.time.temporal – 包括底层框架和扩展特性

```java
//TemporalAdjuster 时间校正器,设置到指定的某个日期时间. 函数式接口类,其实现类为TemporalAdjusters, 通常配合LocalDateTime的with方法使用
//示例, 设置时间为本地时间的当前月的第一天.
LocalDateTime.now().with(TemporalAdjusters.firstDayOfMonth())
```



- java.time.zone – 包含时区支持的类

```java
//ZoneId为时区的包装类, 在需要获取指定时区的时间时候用到
//获取上海时区
ZoneId sh=ZoneId.of("Asia/Shanghai");
LocalDateTime.now(sh);
```



#### 注解增强

Java 8对注解处理提供了两点改进：可重复的注解及可用于类型的注解。

##### 可重复注解

![image-20210806111451059](https://gitee.com/tadpole145/images/raw/main/20210806111451.png)

##### 可用于类型的注解

![image-20210806112204109](https://gitee.com/tadpole145/images/raw/main/20210806112204.png)

#### 其它新特性

- 新增Nashorn引擎,可以运行js代码.
- jvm中的Metaspace取代PermGen空间
- HashMap优化等

