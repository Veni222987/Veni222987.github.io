---
title: 【课程笔记】Java程序设计
author: Veni
categories: 笔记
tags:
  - Java
date: 2023-09-07 09:20:57
---

本文是Java程序设计课程整理的笔记，记录了一些易错点。<!--more-->

# Java程序设计

[TOC]

环境和基础语法就略过了，直接从类开始讲起。可以说，C++只是半面向对象的，而Java是真正面向对象的语言。

## Java面向对象程序设计

### 类和对象的使用

Java的所有代码都必须写在类中，包括main函数，所以说，Java是完全面向对象（OOP）的。类的基本概念和使用和C++类似。

#### 对象在内存中的布局

对象和数组都是引用类型，所以都在栈中有名字和地址，**对象真正的信息存储在堆中**。

![image-20231215152323766](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312151523999.webp)

String，数组和类的声明后默认是null。int,double等默认为0，boolean默认为false。

> Java内存的结构：
>
> - 栈区：存放局部变量，基本数据类型
> - 堆区：存放对象和数组
> - 方法区：包括常量池（字符串常量等），类加载信息（类信息只会加载一次，相当于加载类的定义）

Java的变量置空，实际上是把栈区的变量地址置空，并没有删除堆中的内存。如果没有变量指向内存中的某个类，那么这段空间就是垃圾，即未被引用的对象。

![image-20231215160811902](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312151608106.webp)

#### 方法的调用机制

- 调用方法会开辟一个独立的栈空间。所以，递归调用的时候最容易出现的错误是爆栈。
- 函数return之后，该方法创建的栈就会被销毁，数据返回到调用方法的地方。

可变参数的使用：int... 表示多个同类型的变量，可当数组使用。可变参数可以和其他的类型参数一起使用，但是必须保证可变参数位于参数列表的最后一位。一个形参列表中只能出现一个可变参数。

```java
public int sum(String s, int... nums){
    
}
```

#### 类的初始化与清理

**类的初始化顺序**：

- 初始化静态定义

- 初始化非静态定义

- 初始化构造器

如下图，先初始化静态定义，所以先有bowl4,5，然后初始化其他定义，最后初始化构造器。

![img](https://qn-st0.yuketang.cn/FhpSa4OBxoNV_bO-0kXvGpwFJjtz)

**多个类的初始化顺序**：

1. 主类静态定义
2. 主类main方法
3. 声明主类对象（非静态定义和构造器）
4. 其他类的对象（非静态定义和构造器）

```java
class Insect {
    private int i = 9;
    protected int j;
    public Insect() {
        System.out.println("i=" + i + ", j=" + j);
        j = 39;
    }
    private static int x1 = printInit("static Insect.x1 initialized");
    static int printInit(String s) {
        System.out.println(s);
        return 47;
    }
}

public class Beetle extends Insect {
    private int k = printInit("Beetle.k initialized");
    private static int x2 = printInit("static Beetle.x2 initialized");
    public Beetle() {
        System.out.println("k=" + k);
        System.out.println("j=" + j);
    }
    public static void main(String[] args) {
        System.out.println("Beetle constructor");
        Beetle b = new Beetle();
    }
}
```

<p style="color: orange;">读上面的代码，主要考察类的初始化顺序，输出结果为：</p>

```
static Insect.x1 initialized
static Beetle.x2 initialized
Beetle constructor
i=9, j=0
Beetle.k initialized
k=47
j=39
```

**对象的回收**：

Java对象的回收是通过垃圾回收器来完成的。垃圾回收器是Java的一部分，负责自动管理内存，释放不再使用的对象的内存空间。

Java使用的是基于"可达性分析"的垃圾回收算法。当一个对象不再被任何活动的引用所引用时，它就被判定为"不可达"，即无法通过程序的任何路径访问到它。当垃圾回收器运行时，它会扫描堆中的对象，标记所有可达的对象，然后清理掉所有不可达的对象，最后压缩内存碎片，产生一块连续的空闲内存。

> 工作过程简记：扫描->标记->清理->压缩

#### 类的使用注意事项

- "public"（公共）：具有最高的访问级别，可以被任何其他类访问。
- "protected"（受保护）：**只能被同一包内的类访问，或者是该类的子类访问**。
- "default"（默认）：如果没有明确指定访问修饰符，即没有使用任何修饰符，默认为"default"访问级别。"default"访问级别限制了**同一包内的类**可以访问。
- "private"（私有）：具有最低的访问级别，只能被定义该成员的类内部访问。

下表可以更清晰地反映各个权限修饰符的可访问范围：

| 访问权限  | 同一个类 | 同一个包（含子类） | 不同包子类 | 不同包非子类 |
| --------- | -------- | ------------------ | ---------- | ------------ |
| private   | ✔        |                    |            |              |
| default   | ✔        | ✔                  |            |              |
| protected | ✔        | ✔                  | ✔          |              |
| public    | ✔        | ✔                  | ✔          | ✔            |

在Java语言中，函数的参数传递方式有两种：

- 值传递：基本数据类型是使用值传递的
- 引用传递：对象引用类型（如数组，类对象等）是通过引用传递的进行的。

需要注意的是，Java 中的所有数据类型都是按值传递的，即使是引用类型，也是通过将引用的值（即地址）进行传递。区别在于，引用传递可以修改对象的状态，但无法修改引用本身（即无法将引用指向其他对象）。

```java
class Employee {
    private int salary;
    private String name;
    public Employee(String name, int salary) {
        this.name = name;
        this.salary = salary;
    }
    public static void swap(Employee x, Employee y) {
        Employee temp = x;
        x = y;
        y = temp;
    }
    public static void main(String[] args) {
        Employee a = new Employee("Alice", 5000);
        Employee b = new Employee("Bob", 6000);
        swap(a, b);
        return;
    }
}
```

结果是a仍为Alice，b仍为Bob。但是如果改成下面的函数，则两人的名字会互换：

```java
public static void swap(Employee x, Employee y) {
    String temp=x.name;
    x.name=y.name;
    y.name=temp;
}
```

#### 所有类的基类

类java.lang.Object处于java开发环境的类层次的根部，其它所有的类（包括自己写的类）都是直接或间接地继承了此类。该类定义了一些最基本的状态和行为。下面，我们介绍一些常用的方法。

![image-20230915142555938](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202309151426565.webp)

例如：

```java
A A1=new A(1, 2, 3);
A A2=new A(0, 0, 0);
System.out.println(A1.equals(A2));
```

#### 包

当类很多的时候，可以使用包管理类，方式命名冲突等。

打包的关键字为`package <包名>`。

引入包的关键字`import <包名>(<类名>)`

> 注意，使用`import java.util/*`，只导入该包的一级目录，不推荐使用*的方式导入。

### 单例模式

1. 概念：单例模式（Singleton Pattern）是一种创建对象的设计模式，它确保一个类只有一个实例，并提供全局访问该实例的方式。
2. 实现方法：在单例模式中，类的构造函数被私有化，使得该类无法通过常规方式进行实例化。同时，该类提供一个静态方法或静态属性，用于获取类的唯一实例。这样，无论在何处调用该静态方法或属性，都会返回同一个对象实例
3. 主要特点：**静态实例、全局访问、私有构造**
4. 单例模式的应用场景：线程池、数据库连接池、日志记录器、全局缓存等等。

下面是一个单例模式设计类的案例：

```java
public class Singleton {
    private static Singleton instance;
    private String message;
    private Singleton() {
        // 私有构造函数
    }
    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
    public String getMessage() {
        return message;
    }
    public void setMessage(String message) {
        this.message = message;
    }
}
```

> 单例模式和静态声明的对比：
>
> 单例模式使用类来保存自身对象，当调用getInstance()的时候，如果已经有实例，那么就会返回实例，这一点就决定了这个模式能保证只有一个对象，而使用静态声明则并不能达到该目标。

在上述代码中，我们写一个主函数来测试这个单例模式的工作过程：

```java
public static void main(String[] args) {
    Singleton s1 = Singleton.getInstance();
    Singleton s2 = Singleton.getInstance();
    System.out.println(s1 == s2);
    s1.getInstance().setMessage("hello");
    System.out.println(s2.message);
}
```

运行上述代码，你会发现，s1和s2的message都变成了hello，这是因为两者共享同一个实例。

![image-20230915212709643](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202309152132186.webp)

更多知识可以查阅设计模式的相关书籍。

### 内部类

内部类分为四种：成员内部类，嵌套内部类，静态内部类，匿名内部类。

一个类的定义嵌套在另一个类中：

```java
public class GroupTwo {
    private int count;
    public class Student {
        String name;
        public Student(String name) {
            this.name = name;
            count++; // 访问外部类的成员变量
        }
        public void output() {
            System.out.println(this.name);
        }
    }
    public void output() {
        Student s1 = new Student("Johnson");
        s1.output(); // 通过 s1 调用内部类的成员方法
        System.out.println("count=" + this.count);
    }
    public static void main(String[] args) {
        GroupTwo g2 = new GroupTwo();
        g2.output();
    }
}
```

内部类具有如下特性：

•一般用在定义它的类或语句块之内,在外部引用它时必须给出完整的名称。

•可以使用包含它的外部类的静态成员变量和实例成员变量,也可以使用所在方法的局部变量。

•可以定义为abstract。

•可以声明为public, private或protected。

•若被声明为static,就变成了顶层类,不能再使用实例成员变量和局部变量。

•若想在内部类中声明任何static成员,则该内部类必须声明为static。

外部类不一定has a内部类。

> 因此外部类与内部类的访问原则是：在外部类中，通过一个内部类的对象引用内部类中的成员；反之，在内部类中可以直接引用它的外部类的成员，包括静态成员、实例成员。



#### 匿名内部类

![image-20231220232213715](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312202322840.webp)

![image-20231220232147418](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312202321627.webp)

> 6、关于匿名内部类叙述正确的是？ (  )
>
> A、匿名内部类可以继承一个基类，不可以实现一个接口
>
> B、匿名内部类不可以定义构造器
>
> C、匿名内部类不能用于形参
>
> D、以上说法都不正确
>
>  答案：B，因为匿名内部类不能有构造器，需要将构造参数传给超类构造器。C可以用于形参，见下面的代码。

```java 
package Exercise;

abstract class Animal {
    private String name;
    public abstract void fly();
    public String getName(){
        return name;
    }
    public void setName(String name){
        this.name=name;
    }
}
class TestInnerClass{
    public static void main(String[] args) {
        test(new Animal() {
            private String name;
            @Override
            public void fly() {
                System.out.println("可以飞");
            }
            public String getName(){
                return name;
            }
            public void setName(String name){
                this.name=name;
            }
        });
    }
    public static void test(Animal animal){
        animal.setName("鹦鹉");
        System.out.print(animal.getName());
        animal.fly();
    }
}
```



一般用于实现事件监听器和回调，搭配lamda表达式使用。

![image-20231220232556452](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312202325586.webp)

> 1、匿名内部类和嵌套类的概念和注意事项
>
> 匿名内部类：
>
> 只创建这个类的一个对象，不用为它命名。在定义类的同时，就生成该类的一个实例，并且不会在其他地方听到这个类。
>
> 用于构造对象的任何参数都要被放在超类名后面的括号内。
>
> 匿名内部类不能有构造器。
>
> 匿名内部类既可以拓展类，也可以拓展接口。同时只能且必须实现一个类或者是一个接口。
>
> 匿名内部类是局部内部类的一种。
>
>  
>
> 嵌套类：
>
> 在一个类中定义另外一个类。
>
> 嵌套类的范围受其封闭类的范围限制。
>
> 嵌套类可以访问封闭类的成员，包括私有成员。
>
> 嵌套类可以被声明为private public protected或package private。
>
> 内部类是非静态嵌套类。



### 继承

#### 子类的构造函数

在子类中需要指定父类的构造函数的时候，使用`super(var args)`调用父类的指定构造函数，如下图：
```java
public class TempClass extends BaseClass{
    String name;
    TempClass (){
        super("specific name");
        name="default son";
    }
}
```

类的继承中构造函数的调用顺序：先执行父类构造函数。当然，在手动调用父类的构造函数的时候，父类的构造函数也必须位于子类构造函数的第一行，否则编译器会报错：`call to super() must be first statement in constructor body`。

![image-20231216185911260](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312161859458.webp)



#### 向上转型

向上转型是指将一个**子类的实例赋值给其父类的引用变量**。这种转型是安全的，因为子类对象包含了父类对象的所有属性和方法，所以可以通过父类引用变量来统一使用不同的子类。所以，当使用父类引用（指针）管理子类对象的时候，访问的变量是**父类变量**，但是调用的方法是**子类的方法**。

能覆盖的是基类的域（变量）、静态方法，不能覆盖的是非静态方法。

```java
class Super {
    public int field = 0;
    public int getField() {
        return field;
    }
}

class Sub extends Super {
    public int field = 1;
    public int getField() {
        return field;
    }
    public int getSuperField() {
        return super.field;
    }
}

public class FieldAccess {
    public static void main(String[] args) {
        Super sup = new Sub(); // 向上转型，使用父类引用指向子类
        System.out.println("sup.field = " + sup.field + ", sup.getField() = " + sup.getField());
        Sub sub = new Sub();
        System.out.println("sub.field = " + sub.field + ", sub.getField() = " + sub.getField() + ", sub.getSuperField() = " + sub.getSuperField());
    }
}
```

输出结果：

```shell
sup.field = 0, sup.getField() = 1
sub.field = 1, sub.getField() = 1, sub.getSuperField() = 0
```

#### 静态绑定与动态绑定

静态绑定也就是编译时绑定，动态绑定是运行时绑定。

静态方法、私有方法、final方法使用静态绑定，其他方法使用动态绑定。和向上转型类似，都是为了实现多态。如下面的例子：

```java
class StaticSuper {
    public static String staticGet() {
        return "Base staticGet()";
    }
    public String dynamicGet() {
        return "Base dynamicGet()";
    }
}

class StaticSub extends StaticSuper {
    public static String staticGet() {
        return "Derived staticGet()";
    }
    public String dynamicGet() {
        return "Derived dynamicGet()";
    }
}

public class Test {
    public static void main(String[] args) {
        StaticSuper sup = new StaticSub(); // Upcast
        System.out.println(sup.staticGet());
        System.out.println(sup.dynamicGet());
    }
}
```

输出结果：

```shell
Base staticGet()
Derived dynamicGet()
```

### 接口

Java 不支持多继承性,即一个类只能有一个父类。单继承性使得Java类层次简单,易于程序的管理。为了克服单继承的缺点,Java使用了接口,一个类可以实现多个接口。使用关键字interface 来定义一个接口。接口的定义和类的定义很相似,分为接口声明和接口体两部分。在接口中，抽象方法可以省略

#### 接口声明

我们曾使用class关键字来声明类，接口通过使用关键字interface来声明。完整的接口定义格式如下：

```java
[public] interface interfaceName [extends listOfSuperInterface]{
}
```

其中public修饰符指明任意类均可以使用这个接口，缺省情况下，只有与该接口定义在同一个包中的类才可以访问这个接口。extends子句与类声明中的extends子句基本相同，不同的是一个接口可以有多个父接口，用逗号隔开，而一个类只能有一个父类。子接口继承父接口中所有的常量和方法。

通常接口名称以able或ible结尾，表明接口能完成一定的行为，例如Runnable、Serializable。

#### 接口体

接口体中包含常量定义和方法定义两部分。其中常量具有public、static和final属性。

接口中只能进行方法的声明，而不提供方法的实现，所以，方法定义没有方法体，且用分号(;)结尾，在接口中声明的方法具有public和abstract属性。另外，如果在子接口中定义了和父接口同名的常量，则父接口中的常量被隐藏。

```java
interface Summaryable {
    final int MAX=50; // 接口中的属性具有public、static、final属性
    void  printone(float x);
    float sum(float x ,float y);
    
    default void hello(){
        //jdk8以后，接口可以有默认实现，需要使用default实现
    }
    
    static void hello2(){
        // jdk8以后接口可以有静态函数
    }
}
```

#### 接口的使用

一个类通过使用关键字implements 声明自己使用（或实现）一个或多个接口。如果使用多个接口,用逗号隔开接口名。如：

```java
class Calculate extends Computer implements Summary,Substractable{
    ……
}
```

类Calculate使用了Summary 和Substractable接口,继承了Computer类。



如果一个类使用了某个接口,那么这个类必须实现该接口的所有方法,即为这些方法提供方法体。需要注意的如下：

1）在类中实现接口的方法时,方法的名字,返回类型,参数个数及类型必须与接口中的完全一致。

2）接口中的方法被默认是public ,所以类在实现接口方法时,一定要用public 来修饰。

3）另外,如果接口的方法的返回类型如果不是void 的,那么在类中实现该接口方法时,方法体至少要有一个return 语句。

4）抽象类实现接口的时候，可以不实现所有接口。



接口的调用方法：
```java
public class Computer {
    public void work(UsbInterface usbInterface){
        usbInterface.start();
        usbInterface.stop();
    }
}
```

#### 接口优点及与抽象类比较

从本质上讲，接口是一种特殊的抽象类，这种抽象类中只包含常量和方法的定义，而没有变量和方法的实现。通过接口使得处于不同层次，甚至互不相关的类可以具有相同的行为。接口其实就是方法定义和常量值的集合。

**优点：**

(1)通过接口可以实现**不相关类的相同行为**，而不需要考虑这些类之间的层次关系。

(2)通过接口可以指明类的多个需要实现的方法。

(3)通过接口可以了解对象的交互界面，而不需了解对象所对应的类。

接口把方法的定义和类的层次区分开来，通过它可以在运行时动态地定位所调用的方法。同时接口中可以实现“多重继承”，且一个类可以实现多个接口。正是这些机制使得接口提供了比多重继承（如C++等语言）更简单、更灵活、而且更强劲的功能。  

## Java异常

### 异常体系

Java异常分为两种：Error和Exception，都继承自Throwable。

- Error错误：Java虚拟机无法解决的严重问题，如OOM等，程序会崩溃，无法捕获。
- Exception异常：其他因为编程错误导致的一般性问题，可以捕获或抛出。包括运行时异常（如数组下标越界）和编译时异常（如FileNotFound异常），如不处理编译异常，则编译无法通过。

层级关系如下图所示：

![image-20231217113741371](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312171137589.webp)

异常处理有两种方式：捕获异常和抛出异常。

### 异常处理机制

try-catch-finnally处理机制：

```java
try{
    //代码可能有异常
} catch (Exception e){
    // 捕获到异常
    // 系统将异常对象封装成为Exception e
    // 程序员可以自己写处理逻辑
    return ++i
} finally {
    // 不管try代码块是否异常，始终要执行finally
    // 通常会放资源的关闭，如SQL连接，关闭文件等
    ++i;
}
```

finally无论是否错误，都会执行，即便在catch中执行return语句。如上图，两个++i都会执行，不过return的 i 比最终的 i 小1。

throws处理机制：

在函数调用堆栈中，如果当前方法不想处理异常，可以将异常抛出到调用者，调用者可以执行try-catch-finally捕获，也可以继续抛出。当异常到达JVM的时候，程序会被中断，输出异常信息。

![image-20231217122322601](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312171223815.webp)

一般来说，函数默认处理方式是抛出一个Exception异常，即处理形式如下：

![image-20231217122848041](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312171228177.webp)

子类继承父类的时候，所抛出的异常类型要么是父类的异常，要么是父类异常的子异常，例如父类抛出RuntimeException，子类抛出NullPointerException，是合理的异常。

throw和throws的区别如下：

![image-20231217135613297](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312171356553.webp)

### 自定义异常

自定义异常的本质是**自定义异常信息**以及**抛出异常的条件**。

自定义异常步骤：

1. 自定义类名，继承Exception和RuntimeException。
2. 如果继承Exception，属于编译异常。
3. 如果继承RuntimeException，属于运行异常，一般继承RuntimeException。

例如：
```Java
public class CustomException {
    public static void main(String[] args){
        int age=10;
        if (!(age>=18&&age<=120)){
            throw new AgeException("年龄需要在18~120之间");
        }
    }
}

class AgeException extends RuntimeException{
    public AgeException(String message){
        super(message);
    }
}
```

## Java特性

### 自动装箱和自动拆箱

在Java中，基本数据类型的自动装箱（autoboxing）和自动拆箱（unboxing）是编译器提供的便利功能，用于在基本数据类型和其对应的包装类型之间进行转换。

- 自动装箱（Autoboxing）：将基本数据类型自动转换为对应的包装类型
- 自动拆箱（Unboxing）：将包装类型自动转换为对应的基本数据类型

-128~127之间的数字，存在内存中加速访问。

## JavaIO

什么是流？可以从中读入**一个字节序列**的对象称作输入流，可以向其中写入**一个字节序列**的对象称作输出流。字节序列是任意长度的Byte，所以按照某个固定长度单位读写的对象不是流。

Java的流分为字符流和字节流两类，两者读写数据的单位不一样，但是操作方法类似，都是Read和Write两个方法。

> 字节流：单位是1Byte，不使用缓冲区
>
> 字符流：单位是2Byte的Unicode字符，使用缓冲区

### 字节流处理

Inputstream, outputstream, fileinputstream, fileoutputstream, InputStream和OutputStream都是抽象类，不能实例化，因此在实际应用中都使用的是他们的子类。Java通过系统类System实现标准输入输出的功能，定义了3个流变量，in，out和err。System.in作为字节输入流类InputStream的对象实现标准输入。System.out作为打印流类PrintStream的对象实现标准输出。

FileInputStream和FileOutputStream用于进行文件的输入输出处理，其数据源和接收器都是文件。 FileInputStream用于顺序访问本地文件，FileInputStream重写了抽象类InputStream的读取数据的方法。FileOutputStream用于向一个文本文件写数据，FileOutputStream重写了抽象类OutputStream的写数据的方法。

#### 标准输入输出流

Java通过System类实现了标准输入输出功能，定义了三个流：

- static PrintStream err 
- static InputStream in 
- static PrintStream out 

使用System.out可以获取输出流PrintStream，包括Println等函数，可以直接用于打印。System.err可以理解以报错形式呈现的System.out。使用System.in获取的输入流是字节流，封装的函数较少，如下如所示。因此，获取标准输入的时候，常用Scanner类组合操作。

![image-20231212193146247](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312121931488.webp)

使用`Scanner`类的`hasNext()`方法，它检查输入流中是否有下一个标记可供扫描。但是，如果没有更多的输入可用，`hasNext()`方法将会阻塞，即使输入流已经关闭。

**标准输入输出的示例：**

```java 
var scanner= new Scanner(System.in);
while(scanner.hasNext()){ // 这里的while会一直阻塞，类似while(cin>>str)，除非手动break
    String str=scanner.next();
    System.out.println(str);
}
```

**带退出功能的输入循环：**自己写一个退出标志，关闭scanner并且退出循环。可以直接break不close，但是不可以close不break。因为`scanner.close()`之后调用`scanner.hasNext()`会报错。

```java 
while(scanner.hasNext()){
    String str=scanner.next();
    System.out.println(str);
    if (str.equals("\exit")){
        scanner.close();
        break;
    }
}
```

### 字符流

Reader/Writer是Java用来处理字符流的类。它们是以字符为单位进行读写操作，适用于处理文本数据。FileReader和FileWriter提供了字符文件读写的。

### 文件流

InputStream和OutputStream都是抽象类，不能直接实例化，实际使用需要实例化流类的子类，例如标准输入输出流，文件流等等。

FileInputStream和FileOutputStream是文件处理流类，数据源和接收器都是文件。其中FileOutputStream的创建不依赖文件是否存在。例如下面的代码中，文件系统并不存在hello.test文件，但是实际可以直接写入，证明了FileOutputStream不依赖于文件是否存在。

**文件读示例代码：**

```Java
File f=new File("./src/FileIO/hello.test");
FileOutputStream oStream=new FileOutputStream(f);
String s="hello";
oStream.write(s.getBytes());
```

文件的读写需要使用File类和Stream类结合使用，File类提供文件级的操作，如创建，打开等等。Stream则提供字节级的操作，`s.getBytes()`的作用就是获取字符串的字节流并将其写入文件中。

## 线程

线程是啥就不多说了，下面直接讲java线程的使用。线程的使用主要依靠`java.lang.Thread`支持多线程编程。

### 线程的创建

线程的创建使用Thread()，有多种重载方式：

```java 
Thread();
Thread(Runnable target);
Thread(Runnable target,String name);
Thread(String name);
Thread(ThreadGroup group, Runnable target);
Thread(ThreadGroup group, Runnable target, String name);
Thread(ThreadGroup group, String name);
```

参数target是线程执行的目标对象，也就是目标代码，group是线程组，name是线程名称。

#### 采用继承创建线程

通过继承Thread类，并覆盖Thread类中的run()方法完成线程的创建。

Thread有两个最重要的方法：run()和start()，其中run()方法中写线程代码的逻辑，start()方法首先会进行多线程相关的初始化，然后再执行run()方法。

采用继承创建线程的示例代码如下，继承方式实现比较简单。
```java 
class MyThread extends Thread{
    private static int count;
    public MyThread(String name){
        super(name);
    }
    public void run(){
        count++;
        System.out.println(count);
    }
}
public class MultiThread {
    public static void main(String[] args){
        for (int i=0;i<10000;i++) {
            MyThread t=new MyThread("thread");
            t.start();
        }
    }
}
```

#### 实现接口创建线程

使用这种方法创建线程，需要实现java.lang.Runnable接口创建多线程。

注意，使用这种方法，需要先new一个自己实现的子线程对象，然后再new一个Thread对象，然后将子线程对象传入Thread中，如下图所示：
```java 
class MyThread2 implements Runnable{
    private static int count;
    @Override
    public void run() {
        count++;
        System.out.println(count);
    }
}
public class MultiThread {
    public static void main(String[] args){
        MyThread2 mt2=new MyThread2();
        for (int i=0;i<10000;i++) {
            Thread t = new Thread(mt2);
            t.start();
        }
    }
}
```

上述两段代码的输出结果：不确定，一般在9990~9999之间，思考为什么？

### 线程生命周期和调度

#### 线程生命周期

线程是动态的，生命周期有六个：NEW，RUNNABLE，BLOCKED，WAITING，TIMED WAITING，TERMINATED六种。

![image-20231217161324619](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312171613815.webp)

Java的线程调度策略是固定优先级的抢占式调度：高优先级的进程可以抢占低优先级的进程，同优先级的进程按照时间片轮转。

Java线程分为十个等级，为1~10，10是最高优先级，默认优先级为5。宏定义：

1. MIN_PRIORITY：1
2. NORMAL_PRIORITY：10
3. MAX_PRIORITY：5



#### 线程中断

线程中断的方法：

1. 设置特定变量，控制run()方法
2. 使用interrupt，中断当前状态，该线程会抛出一个InterruptException，可以在catch中加入自己的控制逻辑。



#### 守护线程

在Java中，线程可以分为两种类型：用户线程（User Thread）和守护线程（Daemon Thread）。

守护线程是一种在后台运行的线程，它的存在并不会阻止程序的终止。当所有的用户线程都结束时，守护线程会自动被终止，即使它还没有执行完毕。与之相对，用户线程的执行完毕是程序终止的一个条件。

守护线程通常被用于执行某些支持性任务，例如垃圾回收（Garbage Collection）线程就是一个守护线程。JVM的垃圾回收线程在后台运行，负责回收不再使用的对象，释放内存资源。

设置守护线程的方法：

```java 
dt.setDaemon(True);
```

如果将线程dt设置为守护线程，当所有线程结束后，dt也会自动结束。如果没有设置，即使main线程执行完毕，dt也不退出。

### 线程同步与互斥

什么是同步？线程同步是指有一个线程在对内存进行操作的时候，其他线程不可以对这个内存地址进行操作，直到该线程完成。这里的同步，是指**临界区的同步**，所有线程必须保证临界区的数据完全一致，所以同一时刻只能有一个线程写。

#### synchronized同步

想想上面的多线程代码，分开10000个线程，每个线程都对count+1，最终的结果却总不是10000，为什么？

这是因为count是临界区，多个线程对其进行访问，但是没有上互斥锁，导致丢失修改。为了解决这个问题，可以使用Java中的syncchronized关键字解决，基本语法为：
```java 
synchronized (互斥对象Object){
    // 临界代码段
}
```

注意，互斥对象是一个具体的对象，具备了临界代码的数据。在Java底层，每一个对象都对应于一个可称为“互斥锁”的标记，这个标记可以保证在任意时刻只有一个线程访问。因此，把类中的任意一个成员对象放入synchronized之后，所有的类访问的都是同一个对象，并且具备互斥访问的效果。
```java 
public class SynchronizedExample {
    private int count = 0;
    private Object lock = new Object();
    public void increment() {
        synchronized (lock) {
            count++;
        }
    }
    public int getCount() {
        return count;
    }
}
```

另一种写法如下：synchronized放在方法中，表示整个方法为同步方法。
```java 
class SynchronizedExample {
    private int count = 0;
    public synchronized void increment() {
        count++;
    }
    public int getCount() {
        return count;
    }
}

public class Synchronized {
    public static void main(String[] args) {
        final SynchronizedExample example = new SynchronizedExample();
        List<Thread> threads = new ArrayList<>();
        for (int i = 0; i < 10000; i++) {
            Thread thread = new Thread(() -> {
                example.increment();
            });
            threads.add(thread);
            thread.start();
        }
        // 等待所有线程执行完毕
        for (Thread thread : threads) {
            try {
                thread.join();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        // 输出最终的计数结果
        System.out.println("Final count: " + example.getCount());
    }
}
```

避免死锁的方法：

![image-20231217192724302](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312171928289.webp)

不要出现两个相互请求资源的synchronized代码块。

## Socket编程

一个完整的通讯程序应该包含如下的步骤：

1. 创建Socket。
2. 打开连接到Socket的输入/输出流。
3. 按照一定的协议对Socket进行读/写操作。
4. 关闭Socket。

### 服务器程序

服务器的任务就是等候建立一个连接，然后使用该连接的Socket建立一个InputStream（用于接收数据）和OutputStream（用于发送数据）。



首先，服务器需要先启动，监听一个端口，记为sp。然后调用accpt()函数开始工作，准备接受客户端的Socket。Socket s=accept()函数会阻塞线程，直到有一个新的Socket接入，然后将其赋值`Socket socket=serverSocket.accept();`一旦过了这句话，就表明建立了连接。

服务器程序代码如下：

```java
public class Server {
    public static void main(String[] args){
        try {
            ServerSocket serverSocket=new ServerSocket(5354);
            Socket socket=serverSocket.accept();
            try {
                BufferedReader in=new BufferedReader(new InputStreamReader(socket.getInputStream()));
                PrintWriter out=new PrintWriter(new BufferedWriter(new OutputStreamWriter(socket.getOutputStream())),true);
                while(true){
                    String str=in.readLine();
                    if(str.equals("END")){
                        break;
                    }
                    System.out.println(str);
                    out.println("服务器在鹦鹉学舌："+str);
                }
            } finally {
                System.out.println("关闭");
                socket.close();
            }
        } catch (IOException e) {
            System.out.println(e.getMessage());
        }
    }
}
```

注意点：accept会抛出异常，因此需要一个单独的try语句。后面的所有操作都应该包含在try中，以便处理异常。

### 客户端程序

客户端代码如下：
```java 
public class Client {
    public static void main(String[] args) throws IOException {
        Socket socket=new Socket("172.18.157.129",5354);
        try{
            System.out.println("Socket="+socket);
            BufferedReader in=new BufferedReader(new InputStreamReader(socket.getInputStream()));
            PrintWriter out=new PrintWriter(new OutputStreamWriter(socket.getOutputStream()),true);
            InputStreamReader stdin=new InputStreamReader(System.in);
            BufferedReader stdin_reader=new BufferedReader(stdin);
            while(true){
                String std=stdin_reader.readLine();
                out.println(std);
                System.out.println("发送了"+std);
                String str=in.readLine();
                System.out.println(str);
            }
        } finally {
            socket.close();
        }
    }
}
```

流程是创建一个Socket，指定需要连接的服务器ip:port，然后这个套接字就会尝试往服务器套入。当被accept()之后，双方建立连接开始通信。

工作过程可以简化为下面的图，单个Socket连接的过程就像打电话一样，客户端指定对方身份（ip+port），然后拨出，接通后，两边都显示对方的手机号（ip+port）。

![image-20231220214344844](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312202143037.webp)

### 多线程服务端

多线程的服务端代码如下，在下面的代码中，使用一个ServerWorker继承了Thread线程基类，然后重写run方法，运行新的线程。这里不用担心线程过多，因为客户端断开连接的时候，readLine函数会抛出异常，Connection reset，然后会执行finally后面的语句，也就是关闭socket连接。这就是前面要求可能抛出异常的地方都要使用try-catch-finally的原因。

```java 
class ServerWorker extends Thread{
    static int count=0;
    private Socket socket;
    private BufferedReader in;
    private PrintWriter out;
    private Integer number;
    public ServerWorker(Socket s) throws IOException{
        socket=s;
        in = new BufferedReader(new InputStreamReader(s.getInputStream()));
        out=new PrintWriter(new OutputStreamWriter(s.getOutputStream()),true);
        number=++ServerWorker.count;
        start();
    }
    public void run(){
        try{
            while (true){
                String str=in.readLine();
                if (str.equals("END")){
                    break;
                }
                System.out.println(number+"收到了："+str);
                out.println(number+"收到了："+str);
            }
        } catch (Exception e){
            System.out.println(e.getMessage());
        } finally {
            try {
                socket.close();
            } catch (IOException e) {
                System.out.println(e.getMessage());
            }
        }
    }
}
public class MultiThreadServer {
    static final int PORT=5354;
    public static void main(String[] args) throws IOException{
        ServerSocket s = new ServerSocket(PORT);
        try {
            while(true){
                //这里会阻塞，直到一个新的Socket连接
                Socket socket=s.accept();
                try{
                    new ServerWorker(socket);
                } catch (Exception e){
                    System.out.println(e.getMessage());
                    socket.close();
                }
            }
        } finally {
            s.close();
        }
    }
}
```





> **27**、下面这三条语句
>
>   System.out.println(“is ”+ 100 + 5)； 
>
>   System.out.println(100 + 5 +“ is”)；
>
>   System.out.println(“is ”+ (100 + 5))；
>
> 的输出结果分别是？ (  )
>
> A、is 1005, 1005 is, is 1005
>
> B、is 105, 105 is, is 105
>
> C、is 1005, 1005 is, is 105
>
> ==D、is 1005, 105 is, is 105==
>
> 考+号的左结合性。



> **7**、下列程序段执行后，运行结果为   AB,B   。
>
> public class Foo {
>
> public static void main (String [] args) { 
>
> StringBuffer a = new StringBuffer (“A”);
>
> StringBuffer b = new StringBuffer (“B”);
>
> operate(a,b);
>
> System.out.printIn(a + “,” +b);
>
> }
>
> static void operate (StringBuffer x, StringBuffer y) {
>
> ​        x.append(y);
>
> ​	y = x;
>
> ​    }
>
> }



> 静态内部类不可以直接访问外围类的数据，而非静态内部类可以直接访问外围类的数据，包括私有数据。（ 对）
