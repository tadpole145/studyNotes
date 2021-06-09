[java成神源文档](https://hub.fastgit.org/hollischuang/toBeTopJavaer)

[OnJava8](https://lingcoder.github.io/OnJava8/#/)

#### 基础篇

##### 面向对象的三大基本特征

- **封装**, 把客观事物封装成抽象的类,通过对类里面的方法和数据添加访问权限从而达到对内部数据提供不同级别的保护.
- **继承**,通过继承现有类,获得其功能,同时支持扩展其功能.即所谓的子类比父类更强大.
- **多态**,指为不同数据类型的实体提供统一的接口。简单来说，所谓多态意指相同的消息给予不同的对象会引发不同的动作。它是一种运行时期的状态.它需要满足以下三个条件:
   	- 有类继承或者接口实现
   	- 子类要重写父类方法
   	- 父类引用指向子类对象

##### 五大基本原则

- **单一职责原则**: 一个类尽量只做一件事,能引起它变化的因素尽可能少.从而降低耦合度.
- **开放封闭原则**: 软件实体对扩展开放,对修改封闭.简单点说就是不要修改老代码,尽量使用扩展方式实现新功能.
- **里氏替换原则:** 核心思想是,子类必须能够替换其基类。因为子类比父类强大,当我们用子类替换父类时候,程序原本功能不会发生变化,还能扩展新功能.
- **依赖倒置原则:** 面向接口编程,依赖于抽象而不依赖于具体.高层模块不依赖于底层模块，二者都同依赖于抽象；
- **接口隔离原则:** 核心思想是,使用多个小的专门的接口，而不要使用一个大的总接口。一个类对另外一个类的依赖应该建立在最小的接口上，不要强迫依赖不用的方法，这是一种接口污染。

**迪米特法则**: 又叫最少知道原则,强调类之间的松耦合.一个对象应该对其他对象保持最少的了解。如果说的六大基本原则,则加上这个.

##### 重载与重写

重载: 函数同名不同参

	- 参数必须改变
	- 返回值类型,修饰符,异常均可以被改变

重写: 同名同参, 必须是父子类,子类可以覆盖父类.

	- 方法名,参数,返回值类型,必须相同
	- 访问权限修饰符,异常类型只能比原来的弱
	- final方法不能被重写

区别:

- 重载是一个编译期概念、重写是一个运行期间概念。
- 因为在编译期已经确定调用哪个方法，所以重载并不是多态。而重写是多态。重载只是一种语言特性，是一种语法规则，与多态无关，与面向对象也无关。

##### 继承与实现

继承（Inheritance）：如果多个类的某个部分的功能相同，那么可以抽象出一个类出来，把他们的相同部分都放到父类里，让他们都继承这个类。

实现（Implement）：如果多个类处理的目标是一样的，但是处理的方法方式不同，那么就定义一个接口，也就是一个标准，让他们的实现这个接口，各自实现自己具体的处理方法来处理那个目标

继承的根本原因是因为要复用，而实现的根本原因是需要定义一个标准。

###### java为啥不支持多继承

![image-20210524114838204](https://i.loli.net/2021/05/24/9n76DwXlRSdgKhQ.png)

如果D同时继承了B和C，并且B和C又同时继承了A，那么，D中就会因为多重继承，继承到两份来自A中的属性和方法。这时候，在使用D的时候，如果想要调用一个定义在A中的方法时，就会出现歧义。这样的问题被形象的称为菱形继承问题.

##### 继承与组合

继承： 类与类或者接口与接口之间最常见的一种关系；继承是一种==is-a==关系；

组合： 整体与部分、拥有的关系，即==has-a==的关系。

###### 区别与联系

在`继承`结构中，父类的内部细节对于子类是可见的。所以我们通常也可以说通过继承的代码复用是一种`白盒式代码复用`。在写代码的时候就要指名具体继承哪个类，所以，在`编译期`就确定了关系。

`组合`是通过对现有的对象进行拼装（组合）产生新的、更复杂的功能。因为在对象之间，各自的内部细节是不可见的，所以我们也说这种方式的代码复用是`黑盒式代码复用`。在写代码的时候可以采用面向接口编程。所以，类的组合关系一般在`运行期`确定。

**`建议在同样可行的情况下，优先使用组合而不是继承。  因为组合更安全，更简单，更灵活，更高效。`**

##### java类

一个java类,包含构造函数,变量,方法,静态代码块等.

###### 构造函数

是一种特殊的==方法==。 主要用来在创建对象时初始化对象， 即为对象成员变量赋初始值，总与new运算符一起使用在创建对象的语句中。

构造函数可以有多个重载函数，与普通实例方法不同的是，它没有返回值。构造器的函数名称必须和它所属的类的名称相同。如果一个类没有专门编写构造函数，那么会自动生成一个默认无参构造函数，初始变量全部为默认值。如果有构造函数，那么无参构造函数只能手动添加。

###### 变量

类变量

成员变量

局部变量

```java

    public class Variables {
    
        /**
         * 类变量, 作用域是当前类
         */
        private static int a;
    
        /**
         * 成员变量, 作用域是当前对象
         */
        private int b;
    
        /**
         * 局部变量,作用域是当前方法
         * @param c
         */
        public void test(int c){
            int d;
        }
    }
```

###### 权限修饰符

`public` : 表明该成员变量或者方法是对所有类或者对象都是可见的,所有类或者对象都可以直接访问

`private` : 表明该成员变量或者方法是私有的,只有当前类对其具有访问权限,除此之外其他类或者对象都没有访问权限.子类也没有访问权限.

`protected` : 表明成员变量或者方法对类自身,与同在一个包中的其他类可见,其他包下的类不可访问,除非是他的子类

`default` : 表明该成员变量或者方法只有自己和其位于同一个包的内可见,其他包内的类不能访问,即便是它的子类

##### 平台无关性

平台无关性就是一种语言在计算机上的运行不受平台的约束，一次编译，到处执行. java编译成字节码,然后通过虚拟机编译成机器语言,运行在不同的系统上..java虽然平台无关,但是它所依赖的jvm是平台有关的.

对于Java的平台无关性的支持是分布在整个Java体系结构中的。其中扮演着重要角色的有Java语言规范、Class文件、Java虚拟机等

- Java语言规范
  - 通过规定Java语言中基本数据类型的取值范围和行为
- Class文件
  - 所有Java文件要编译成统一的Class文件
- Java虚拟机
  - 通过Java虚拟机将Class文件转成对应平台的二进制文件等

Java的平台无关性是建立在Java虚拟机的平台有关性基础之上的，是因为Java虚拟机屏蔽了底层操作系统和硬件的差异。JVM其实并不是和Java文件进行交互的，而是和Class文件，如Kotlin、Groovy、JRuby、Jython、Scala等也可以被编译成class文件,因此也可以在jvm上面运行.

##### 值传递与引用传递

**值传递**（pass by value）是指在调用函数时将实际参数`复制`一份传递到函数中，这样在函数中如果对`参数`进行修改，将不会影响到实际参数。

**引用传递**（pass by reference）是指在调用函数时将实际参数的地址`直接`传递到函数中，那么在函数中对`参数`所进行的修改，将影响到实际参数。

![image-20210528113448010](https://i.loli.net/2021/05/28/BwaQXIh2qKAUCyd.png)

java有基本数据类型和引用数据类型二种,关于java中到底是值传递还是引用传递的争议主要集中在引用数据类型上, ==java中的参数传递其实只有值传递==,在 《The Java™ Tutorials》一书中写有:**用数据类型参数(如对象)也按值传递给方法。这意味着，当方法返回时，传入的引用仍然引用与以前相同的对象。**

**其实Java中使用的求值策略就是传共享对象调用，也就是说，==Java会将对象的地址的拷贝传递给被调函数的形式参数==。**因为传共享对象调用其实是传值调用的一个特例。

###### 数据的存放位置

在方法中声明的变量，即该变量是局部变量，每当程序调用方法时，系统都会为该方法建立一个方法栈，其所在方法中声明的变量就放在方法栈中，当方法结束系统会释放方法栈，其对应在该方法中声明的变量随着栈的销毁而结束，这就局部变量只能在方法中有效的原因.

- 变量为基本类型, 变量名, 值均存放在方法栈中.
- 变量为引用类型时, 变量名在方法中,值(变量所指向的对象)存放在堆中

在类中声明的变量是成员变量，也叫全局变量，放在堆中的（因为全局变量不会随着某个方法执行结束而销毁）。

- 当该成员变量是基本类型时,变量其变量名及其值放在堆内存中的.
- 当变量为引用类型时,其变量名, 值(引用对象)也是放在堆内存中.

当方法传参是基本类型时,它的变量名,值都在当前方法栈中,自然无法影响到原参数中的值.



#### java基础

##### 基本数据类型

也叫做内置类型，是 Java 中不同于类(Class)的特殊类型。它直接在栈内存中存储，读取更高效.

- 字符型  char

- 布尔型  boolean

- 数值型

  	- 整形 byte(1字节), short (2字节),int(4字节), long(8字节)
  	- 浮点型, float(4字节), double(8字节)


实际上，Java 中还存在另外一种基本类型 `void`，它也有对应的包装类 `java.lang.Void`，不过我们无法直接对它们进行操作。

###### 浮点型

简单来说就是表示带有小数的数据,由于二进制中,小数无法精确表示,只能通过一种近视值表示小数的方式来表示,由此引入了精度的概念.一个浮点数a由两个数m和e来表示：a = m × b^e。

###### 整数存储方式

- 用2整除十进制整数，可以得到一个商和余数；
- 再用2去除商，又会得到一个商和余数，如此进行，直到商为小于1时为止
- 然后把先得到的余数作为二进制数的低位有效位，后得到的余数作为二进制数的高位有效位，依次排列起来

![-w624](https://www.hollischuang.com/wp-content/uploads/2020/10/16024170911973.jpg)

###### 小数存储方式

- 用2乘十进制小数，可以得到积
- 将积的整数部分取出，再用2乘余下的小数部分，又得到一个积
- 再将积的整数部分取出，如此进行，直到积中的小数部分为零，此时0或1为二进制的最后一位。或者达到所要求的精度为止。

![-w624](https://i.loli.net/2021/06/02/eAtfmSsJ8qDKdEh.jpg)

0.1的二进制表示中出现了无限循环的情况,计算机无法精确表示,所以出了精度这个概念.

###### 单双精度浮点型

![单双精度浮点型](https://i.loli.net/2021/06/02/ci4xUd2rwG37D1a.png)

由于浮点型只是小数的近视值,所以并不能用于表示金额, 如果涉及到金额的计算,建议使用bigDecimal或者以分为单位的long整型值.

###### BigDecimal为什么能做到高精度计算

十进制整数在转化成二进制数时不会有精度问题，那么把十进制小数扩大N倍让它在整数的维度上进行计算，并保留相应的精度信息。

#### 包装类型

基本类型数据的包装类.

| 基本数据类型 | 包装类    |
| ------------ | --------- |
| byte         | Byte      |
| boolean      | Boolean   |
| short        | Short     |
| char         | Character |
| int          | Integer   |
| long         | Long      |
| float        | Float     |
| double       | Double    |

##### 为什么需要包装类?

因为 Java 是一种面向对象语言，很多地方都需要使用对象而不是基本数据类型。比如，在集合类中，我们是无法将 int 、double 等类型放进去的。因为集合的容器要求元素是 Object 类型。

为了让基本类型也具有对象的特征，就出现了包装类型，它相当于将基本类型“包装起来”，使得它具有了对象的性质，并且为其添加了属性和方法，丰富了基本类型的操作。

##### 装箱与拆箱

装箱 boxing:  基本类型转换成包装类型

拆箱unboxing: 包装类型---->基本类型

```java

Integer i = 10;  //可以替代 Integer i = new Integer(10);，这就是因为 Java 帮我们提供了自动装箱的功能
int b = i;     //自动拆箱
```

###### 原理

自动装箱都是通过包装类的 `valueOf()` 方法来实现的.自动拆箱都是通过包装类对象的 `xxxValue()` 来实现的。

###### 使用场景

- 将基本数据类型放入集合类, 集合只能接收引用类型数据.
- 包装类型数据与基本类型数据比较大小.
- 包装类型的运算.
- 三目运算,当第二，第三位操作数分别为基本类型和对象时，其中的对象就会拆箱为基本类型进行操作。
- 函数参数与返回值.
- Java SE 的自动拆装箱还提供了一个和缓存有关的功能

```java
    public static void main(String... strings) {

        Integer integer1 = 3;
        Integer integer2 = 3;

        if (integer1 == integer2)
            System.out.println("integer1 == integer2");//true
        else
            System.out.println("integer1 != integer2");

        Integer integer3 = 300;
        Integer integer4 = 300;

        if (integer3 == integer4)
            System.out.println("integer3 == integer4");
        else
            System.out.println("integer3 != integer4");//true
    }

//在 Java 中，== 比较的是对象引用，而 equals 比较的是值。
//只适用于自动装箱。使用构造函数创建对象不适用。因为使用构造函数,会直接创造对象.而不会去缓存里面寻找数字.
```

在 Java 5 中，在 Integer 的操作上引入了一个新功能来节省内存和提高性能。整型对象通过使用相同的对象引用实现了缓存和重用。 Java 5 中引入的时候,范围是固定的 -128 至 +127。后来在 Java 6 中，可以通过 `java.lang.Integer.IntegerCache.high` 设置最大值。

到底是什么原因选择这个 -128 到 127 范围呢？因为这个范围的数字是最被广泛使用的。 在程序中，第一次使用 Integer 的时候也需要一定的额外时间来初始化这个缓存。

这种缓存行为不仅适用于Integer对象。我们针对所有的整数类型的类都有类似的缓存机制。与Integer不同的是,他们的范围均不能改变.

- 有ByteCache用于缓存Byte对象   范围: -128 到 127
- 有ShortCache用于缓存Short对象  范围: -128 到 127
- 有LongCache用于缓存Long对象  范围: -128 到 127
- 有CharacterCache用于缓存Character对象  范围是 0 到 127

###### 正确使用boolean类型的返回值

一般情况下，我们可以有以下四种方式来定义一个布尔类型的成员变量：

```java
boolean success
boolean isSuccess
Boolean success
Boolean isSuccess
```

阿里巴巴Java开发手册中关于这一点，有过一个『强制性』规定：

POJO类中布尔类型变量,都不要加is, 否则部分框架解析会引起序列化错误.例如, 定义变量boolean isSuccess,方法也是  isSuccess(), RPC框架方向解析的时候,”以为”属性的名称就是“success”, 导致属性获取不到,从而抛出异常.

那么，到底应该是用Boolean还是boolean来给定一个布尔类型的变量呢？

阿里巴巴手册建议是: 强制所有的POJO类属性必须使用包装类型.局部变量建议使用基本类型.理由是因为基本类型会有默认值0, 在计费系统中,如果费率没做处理,就会使用默认值,导致bug无法被发现,而包装类型,当费率未被返回时,会出现空指针异常(NPE),从而快速发现该bug.



#### String

String是通过字符数组实现的,一个string对象在内存(堆)中被创建出来，它就无法被修改。String类的所有方法都没有改变字符串本身的值，都是返回了一个新的对象。

```java
//final修饰,也说明了其是不可以被更改的.
public final class String implements java.io.Serializable, Comparable<String>, CharSequence {
    
}
```

##### substring在不同jdk中的实现

```java
//JDK 6
String(int offset, int count, char value[]) {
    this.value = value; //字符数组
    this.offset = offset;  //数组的第一个字符索引位置
    this.count = count;		//字符串中包含的字符个数
}

public String substring(int beginIndex, int endIndex) {
    //check boundary
    return  new String(offset + beginIndex, endIndex - beginIndex, value);
}
```

导致的问题: 当一个很长的字符串,使用substring进行切割的时候你只需要很短的一段,但是你一直引用了整个数组,导致内存无法回收,进而可能导致内存泄露.当时建议的解决方案是

```java
x = x.substring(x, y) + ""//生成一个新的字符串并引用它
```

在jdk 7 中，解决了该问题, substring方法会在堆内存中创建一个新的数组。

```java
//JDK 7
public String(char value[], int offset, int count) {
    //check boundary
    this.value = Arrays.copyOfRange(value, offset, offset + count);
}

public String substring(int beginIndex, int endIndex) {
    //check boundary
    int subLen = endIndex - beginIndex;
    return new String(value, beginIndex, subLen);
}
```

##### replaceFirst、replaceAll、replace区别

```java
replace(CharSequence target, CharSequence replacement)
 //用replacement替换所有的target，两个参数都是字符串。

replaceAll(String regex, String replacement) //用replacement替换所有的regex匹配项，regex很明显是个正则表达式，replacement是字符串。

replaceFirst(String regex, String replacement) //基本和replaceAll相同，区别是只替换第一个匹配项。

//可以看到，其中replaceAll以及replaceFirst是和正则表达式有关的，而replace和正则表达式无关。
```



##### 字符串拼接的几种方式和区别

1. 使用 + 拼接

> 原理: 通过查看反编译以后的代码可以发现，它其实是使用`StringBuilder.append`

2. **concat**拼接

```java
public String concat(String str) {
    int otherLen = str.length();
    if (otherLen == 0) {
        return this;
    }
    int len = value.length;
    char buf[] = Arrays.copyOf(value, len + otherLen);
    str.getChars(buf, len);
    return new String(buf, true);
}
```

> 原理: 创建了一个字符数组，长度是已有字符串和待拼接字符串的长度之和，再把两个字符串的值复制到新的字符数组中，并使用这个字符数组创建一个新的String对象并返回。

3. **StringBuffer**

> 和`String`类似，`StringBuffer`类也封装了一个字符数组，与`String`不同的是，它并不是`final`的，所以他是可以修改的。append会直接拷贝字符到内部的字符数组中，如果字符数组长度不够，会进行扩展。
>
> 线程安全,效率低下

```java
public synchronized StringBuffer append(String str) {
    toStringCache = null;
    super.append(str);
    return this;
}
```

4. **StringBuilder**

> 线程不安全,效率高.

5. **StringUtils.join**

> 通过`StringBuilder`来实现的。

###### 耗时比较(循环体)

StringBuilder < StringBuffer < concat< + < StringUtils.join

原因:  StringBuffer做了同步处理,耗时多一点,concat是新创建了一个字符串,+虽然底层使用的StringBuilder , 但是在循环过程中,每次都new StringBuilder 导致开销很大,StringUtils.join也是使用了StringBuilder，并且其中还是有很多其他操作，所以耗时较长

###### 建议

- 循环体内，字符串的连接方式，使用 `StringBuilder` 的 `append` 方法进行扩展。
- 2个字符串简单拼接,直接使用`+`就好了。
- 如果在并发场景中进行字符串拼接的话，要使用`StringBuffer`来代替`StringBuilder`。

##### int转String

```java
int i = 5;

//String i1 = (new StringBuilder()).append(i).toString();，首先创建一个StringBuilder对象，然后再调用append方法，再调用toString方法。
String i1 = "" + i;
//String.valueOf(i)也是调用Integer.toString(i)来实现的。
String i2 = String.valueOf(i);
String i3 = Integer.toString(i);

```

##### switch支持的数据类型

- java5以前, 只支持byte, short, char ,int;
- java5, 引入枚举类型和byte, short, char ,int的包装类型;
- java7新增string类型

**一共6种数据类型: 4个整型值的基本数据类型+枚举+String**

- byte, short 可以在不损失精度的情况下向上转型成int
- 包装类型会自动拆箱
- 对char类型进行比较的时候，实际上比较的是ascii码，编译器会把char型变量转换成对应的int型变量, 
-  枚举类型的支持是因为枚举类有一个ordinal方法,该方法实际上是返回一个int类型的数值。
- String类中因为有一个hashCode方法,结果也是返回int类型。为了防止hash碰撞,编译器编译后会新增一个`equals`方法

**本质上java中switch只支持一种数据类型,那就是int. long类型向下转型会有精度损失.**

```java
//源代码
public class switchDemoString {
    public static void main(String[] args) {
        String str = "world";
        switch (str) {
        case "hello":
            System.out.println("hello");
            break;
        case "world":
            System.out.println("world");
            break;
        default:
            break;
        }
    }
}
//编译后
public class switchDemoString{
    public switchDemoString()
    {
    }
    public static void main(String args[])
    {
        String str = "world";
        String s;
        switch((s = str).hashCode())
        {
        default:
            break;
        case 99162322:
            if(s.equals("hello")) //新增equals方法,防止hash碰撞
                System.out.println("hello");
            break;
        case 113318802:
            if(s.equals("world"))
                System.out.println("world");
            break;
        }
    }
}

```

##### Class常量池

暂且不研究,等学完了深入理解java虚拟机再补充这部分笔记.

##### string的长度限制

字符串有长度限制，在编译期，要求字符串常量池中的常量不能超过65535，并且在javac执行过程中控制了最大值为65534。

CONSTANT_String_info 用于表示 java.lang.String 类型的常量对象，格式如下：

```java
CONSTANT_String_info {
    u1 tag;
    u2 string_index;  //u2表示两个字节的无符号数，那么1个字节有8位，2个字节就有16位,其可表示的最大值位2^16 - 1 = 65535。
}
```



在运行期，长度不能超过Int的范围(2^31-1约等于4G)，否则会抛异常。

```java
//String 构造方法
// int的范围是4字节32位,最大值是2^31-1
public String(byte bytes[], int offset, int length) 
```





#### java中的关键字

##### transient

就是被transient修饰的成员变量，在序列化的时候其值会被忽略，在被反序列化后， transient 变量的值被设为初始值， 如 int 型的是 0，对象型的是 null。

##### instanceof

Java 的保留关键字。它的作用是测试它左边的对象是否是它右边的类的实例，返回 boolean 的数据类型。
