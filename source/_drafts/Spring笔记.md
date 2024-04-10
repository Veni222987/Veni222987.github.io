---
title: Spring笔记
tags:
  - Spring
categories: notes
cover: https://img0.baidu.com/it/u=1714670866,2789309595&fm=253&fmt=auto&app=120&f=PNG?w=783&h=305
highlight_shrink: true
date: 2023-12-25 21:29:37
---

# Spring笔记

## Spring家族和历史

在Spring.io中的project介绍了Spring大家族。包括Spring framework, Spring boot, Spring data, Spring cloud等。

Spring在第一代的时候，配置文件均为xml，到了2.0引入了注解的形式，3.0可以不使用xml。4.0更新JDK版本，5.0更新到JDK8。

## Spring Framework

Spring 4.0的架构图如下，最新的Spring 5.0并没有公布架构图，因此可以认为架构变化不大。

![image-20231226205931920](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312262059136.webp)



## Spring核心容器

### IoC/DI概念

为什么需要IoC控制反转？因为常规的实现耦合度较高。在平时使用对象的时候，都是new一个对象去使用，现在转换为由外部获取对象。这就是IoC（控制反转）的思想，即把对象的**创建权**交给容器。

Spring实现了IoC的思想，提供了一个容器，叫做IoC容器，又叫Spring容器，也就是架构图中的core container。被管理的对象称为Bean。

为什么需要DI依赖注入？当你把程序中嵌套关系的类都交给容器管理的时候，运行外层的Bean依赖于内部Bean，如果没有作初始化，就会抛出异常。如果具备依赖关系的Bean都交给Spring容器管理，那么Spring容器就会完成依赖关系的绑定，这就是DI。

> DI的实现方法：

### IoC实战

既然已经对Spring容器有了初步理解，接下来就可以用一个小小的案例体验一下Spring的容器管理了。

- 导入Maven包：需要导入的包如下

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.2.15.RELEASE</version>
</dependency>
```

- 在Resource文件夹中创建ApplicationContext.xml作为Spring的配置文件，在里面配置Bean。（熟悉的bean配置，让人回忆起Web开发课程）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--    配置bean-->
    <bean id="BookDao" class="tech.veni.dao.impl.BookDaoImpl"/>
    <bean id="BookService" class="tech.veni.service.impl.BookServiceImpl"/>
</beans>
```

- 获取容器：新建一个启动类，重写Main方法，新建一个ApplicationContext的对象，注意ApplicationContext是一个接口，需要new一个实现该接口的对象。

```java 
public static void main(String[] args){
    // 获取Spring容器
    ApplicationContext ctx= new ClassPathXmlApplicationContext("applicationContext.xml");
    // 获取Bean
    //        BookDao bookDao = (BookDao) ctx.getBean("BookDao");
    //        bookDao.save();
    var bookService = (BookService) ctx.getBean("BookService");
    bookService.save();
}
```

- 获取Bean：使用getBean方法，传入xml配置中的id即可。

获取Bean之后，就可以像使用一般类一样使用Bean了。



现在回想一下，Spring这个容器做了什么呢？很显然，在main函数中，通过使用getBean的方法获取到了类，而不需要自己new一个新的Bean。注意，不用写new的是Bean，Context还是要自己创建的，因为容器不管理本身。还有一点，在Service内部，还new了一个Dao对象，这个问题还没解决，需要用到依赖注入DI。



使用xxxApplicationContext可以创建一个Spring容器，并且也可以从Spring容器中获取某一个Bean，然后调用其中的方法。例如：

```java 
// 使用xml，在spring.xml定义了bean的扫描路径和一些bean
ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring.xml");
// 使用注解，目前常用的方法
AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
```

也可以自己实现一个ApplicationContext，最根本的思想就是：读取配置（xml或配置类）--> 扫描Bean --> 对外提供getBean接口。可以看看图灵学院手写Spring源码的课程。

```java 
public class VApplicationContext {
    private Class configClass;
    public VApplicationContext(Class configClass){
        this.configClass=configClass;
        // 解析配置类
        // 获取注解对象
        ComponentScan componentScan= (ComponentScan) configClass.getAnnotation(ComponentScan.class);
        String path= componentScan.value();
        // System.out.println(path);
        // 扫描目标路径的类，即扫描由Component注解的Bean-->生成Bean对象
    }
    public Object getBean(String name){
        return null;
    }
}
```

> Java类加载器有三种：
>
> - Bootstrap Class Loader: 所以类加载器的父类，负责核心类的加载，如rt.jar。
> - Extension Class Loader: 负责加载Java扩展的目录，如jre/lib/ext。
> - Application Class Loader: 是Extension Class Loader的子类，负责加载Java程序的类，如*.class，也包括其他第三方库。

### DI实战

在上面的实战中，Service的Dao的关系还没解决，具体而言就是两个问题：Service中的Dao对象如何进入Service中？Service和Dao的关系如何描述？

- 删除业务层中使用new创建的对象的语句，只需要声明即可。

- 在业务层中使用另外的类，需要对外提供set方法。

```java 
public class BookServiceImpl implements BookService {
    private BookDao bookDao;

    public void save(){
        System.out.println("book service save ...");
        bookDao.save();
    }

    public void setBookDao(BookDaoImpl bookDao) {
        this.bookDao=bookDao;
    }
}
```

> 这一步至关重要，是解决"Service中的Dao对象如何进入Service中"的问题的，同时回想一下JSP中对于JavaBean的定义，有一项就是公有化的get和set方法，这样才能配置。

- 在xml中配置关系：property标签配置的是当前bean的成员（属性），name标识配置的成员（属性）名，ref表示参照哪一个Bean，也就是什么类型。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--    配置bean-->
    <bean id="BookDao" class="tech.veni.dao.impl.BookDaoImpl"/>
    <bean id="BookService" class="tech.veni.service.impl.BookServiceImpl">
        <property name="BookDao" ref="BookDao"/>
    </bean>
</beans>
```

## Spring中的Bean

### Bean的配置

- Bean的别名

使用name配置bean的别名，可以使用逗号"," 分号";"或者空格" "。完全可以当bean的id使用，包括依赖和getBean。

```xml 
<bean id="BookDao" name = "book1;book2" class="tech.veni.dao.impl.BookDaoImpl"/>
```

- Bean的作用范围

Spring默认创建的Bean是一个单例的Bean。使用scope属性可以配置bean的作用范围，有两种属性：singleton和prototype，前者是单例的，后者是非单例的。

```xml 
<bean id="BookDao" class="tech.veni.dao.impl.BookDaoImpl" scope="prototype"/>
```

> 为什么Bean默认单例？Spring默认管理单例的Bean，如果是非单例的，每次使用都创建一个Bean，丢给Spring管理会消耗很大的内存空间。
>
> 哪些Bean适合交给Spring呢？表现层、业务层、数据层和工具对象。封装实体的对象不适合交给Spring。

### Bean的实例化

#### 构造方法

Bean本质上是对象，可以使用构造方法创建。想验证的话就可以直接重写无参构造函数，打印一行字，就可以看Spring是否使用构造函数创建Bean了。不管是私有的还是公有的构造函数都可以。注意，Spring默认调用的是无参构造函数。

> 私有的构造函数是通过反射实现的。

#### 静态工厂

使用工厂也可以造对象。工厂是一个特殊的类，具备创建新对象的功能，例如：

```java 
public class OrderDaoFactory {
    public static OrderDao getOrderDao(){
        return new OrderDaoImpl();
    }
}

// 调用方法
// OrderDao o = OrderDaoFactory.getOrderDao();
```

那么如何定义Bean使用静态工厂呢？使用class使用工厂类名，指定factory-method调用静态工厂的函数。（了解即可，比较少用）

```xml
<bean id="BookDao" class="tech.veni.factory.OrderDaoFactory" factory-method="getOrderDao"/>
```

#### 实例工厂

创建一个实例工厂类，也就是把静态工厂里面的static关键字去掉。使用的时候需要先new一个新的实例工厂然后调用创建类方法。

```java 
public class OrderDaoFactory {
    public OrderDao getOrderDao(){
        return new OrderDaoImpl();
    }
}
```

Bean配置方法：需要先配置工厂bean，再配置工厂创建Bean的方法。

```xml
<bean id="userFactory" class="tech.veni.factory.OrderDaoFactory"/>

<bean id="userDao" factory-method="getOrderDao" factory-bean="userFactory"/>
```

#### BeanFactory（需要掌握）

首先创建一个FactoryBean，实现FactoryBean\<T>范型的接口，类型就是需要创建的Bean类型。

```java 
public class BookDaoFactoryBean implements FactoryBean<BookDao> {
    // 代替原始实例工厂中的get***的方法
    @Override
    public BookDao getObject() throws Exception {
        return new BookDaoImpl();
    }

    @Override
    public Class<?> getObjectType() {
        return BookDao.class;
    }
    
    @Override   //可以不重写
    public boolean isSingleton() {
        return true;
    }
}
```

有啥用呢？可以简化xml中的配置。发现了只需要指定class就可以了。是否单例、获取方法，都交给接口实现。

```xml
<bean id="BookDao" class="tech.veni.factory.BookDaoFactoryBean"/>
```

### Bean的生命周期

生命周期：初始化、使用、销毁阶段。

Bean生命周期的控制，也就是在生命周期的过程中执行的操作，如读取一些配置等等。这些业务逻辑写在Bean的定义中，并且使用xml配置：

```java 
public class BookDaoImpl implements BookDao {
    @Override
    public void save() {
        System.out.println("dao save it");
    }

    // 表示bean初始化的时候执行的操作
    public void init(){
        System.out.println("dao init");
    }

    public void destroy(){
        System.out.println("dao destroy");
    }
}
```

配置的时候配置两个method属性就好。

```xml
<bean id="BookDao" class="tech.veni.dao.impl.BookDaoImpl" init-method="init" destroy-method="destroy"/>
```

一般不会执行destroy，因为销毁的时候虚拟机已经关闭了。如果需要手动关闭，需要调用ApplicationContext的close()方法，或者注册关闭钩子。

```java 
// 获取Spring容器
ClassPathXmlApplicationContext ctx= new ClassPathXmlApplicationContext("applicationContext.xml");
// 获取Bean
BookDao bookDao = (BookDao) ctx.getBean("BookDao");
bookDao.save();
// 下列代码二选一
ctx.registerShutdownHook();
ctx.close();
```

但是实际上很少用，因为Web应用，一般都由Tomcat实现。

也可以使用接口实现，需要在Bean类上实现Initialization和DisposableBean两个接口，xml就可以不用写init-method和destroy-method。

## 依赖注入

依赖注入的方式主要有：setter注入、构造器注入。Spring框架推荐使用构造器注入。

基于xml配置的DI就不啰嗦了，后面推荐使用注解配置。

### 自动装配

自动装配的方式有以下几种：

1. 按类型
2. 按名称
3. 按构造方法
4. 不启动自动装配

自动装配的方式推荐使用按类型注入。自动装配的优先级低于setter注入和构造器注入，同时使用的时候会导致自动装配失效。



