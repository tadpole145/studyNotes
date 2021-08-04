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





#### 方法引用/构造器引用

#### 强大的Stream API

#### Optional类

#### 接口增强

#### 新的时间与日期API

#### 重复注解与类型注解

#### 其它新特性

新增Nashorn引擎,可以运行js代码.