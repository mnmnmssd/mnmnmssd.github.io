---
title: "SpringDay01"
date: 2023-03-16T17:45:29+08:00
slug: Spring学习
categories:
    - 学习
    - Spring
---

# Spring概述

Spring 是最受欢迎的企业级 Java 应用程序开发框架，数以百万的来自世界各地的开发人员使用 Spring 框架来创建性能好、易于测试、可重用的代码。

Spring 框架是一个开源的 Java 平台，它最初是由 Rod Johnson 编写的，并且于 2003 年 6 月首次在 Apache 2.0 许可下发布。

Spring 是轻量级的框架，其基础版本只有 2 MB 左右的大小。

Spring 框架的核心特性是可以用于开发任何 Java 应用程序，但是在 Java EE 平台上构建 web 应用程序是需要扩展的。 Spring 框架的目标是使 J2EE 开发变得更容易使用，通过启用基于 POJO 的编程模型来促进良好的编程实践。

# Spring快速入门

**本项目使用Maven构建** 

## Spring IOC容器

在常规Java开发中，Bean的管理常常是非常头疼的事情，常规一般随用随new，造成极大资源浪费，而且增大了代码的耦合程度，使定位问题和修改成为了极大的困难，Spring的出现解决了这一问题，他将开发者与Bean进行了抽离，在两者之间使用Bean工厂进行链接，使Bean的创建与管理极大的便利了起来。

常规方式管理Bean：开发者-->Bean

Spring方式管理Bean：开发者-->第三方工具-->Bean

Spring中提供了两种方式创建Bean对象，一种使用BeanFactory来创建，一种使用ApplicationContext来创建。

以上两种方式需要首先从配置文件中指定bean的名称等属性

### 创建xml配置文件

首先ApplicationContext有三种方式

- 系统路径获取配置文件 (FileSystemXmlApplicationContext)
- 直接获取配置文件 (ClassPathXmlApplicationContext)
- 注解方式获取Bean (AnnotationConfigApplicationContext)

我们这里使用第二种也是除了注解外最常用的一种方式，在项目的resource文件夹下创建`applicationContext.xml`文件。

文件初始内容如下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```

我们在项目中创建几个测试Bean对象，如下图所示，其中`UserServiceImpl`实现了`UserService`接口，并在内部引用了`UserDao`对象。

![image-20230316183943486](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg@master/2023/03/upgit_20230316_1678963189_image-20230316183943486.png)

UserServiceImpl内部书写

```java
    private UserDao userDao;
    
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
        System.out.println(userDao);
    }
```



### 使用BeanFactory创建Bean对象

首先我们使用BeanFactory方式创建Bean对象。

#### Bean标签形式

编辑我们创建的`applicationContext.xml`文件，配置如下

```xml
<bean id="userService" class="org.itHeima.service.impl.UserServiceImpl" />
```

其中`bean`标签表示为一个bean对象，`id`属性为唯一标识，在后续中可通过该标识获取Bean对象，`class`属性为实例化对象位置。

接下来我们在主包下创建BeanFactoryTest测试类来测试，其中main方法内书写如下

```java
		//创建Bean工厂
        DefaultListableBeanFactory beanFactory = new DefaultListableBeanFactory();
        //创建读取器
        XmlBeanDefinitionReader reader = new XmlBeanDefinitionReader(beanFactory);
        //读取配置文件
        reader.loadBeanDefinitions("applicationContext.xml");
        //通过getBean获取Bean对象
        UserService userService = (UserService) beanFactory.getBean("userService");

        System.out.println(userService);
```

在这种方法下`userService`中并没有实例化`userDao`对象，运行后可以观测到输出

`org.itHeima.service.impl.UserServiceImpl@28f67ac7`。

#### 自动注入形式

修改xml配置文件中内容为

```xml
<bean id="userService" class="org.itHeima.service.impl.UserServiceImpl">
        <property name="userDao" ref="userDao"/>
</bean>
<bean id="userDao" class="org.itHeima.Dao.impl.UserDaoImpl"/>
```

其中`property`标签表示其为当前bean的一个属性内容，这里的`name`属性应该与`userServiceImpl`中的`setUserDao`方法中**set后的**名称(**UserDao->userDao**)吻合,并不是下方的`id`属性，后面的`ref`属性才应为要自动注入的bean的`id`属性。

main方法不变。

运行后应输出以下内容

```
org.itHeima.Dao.impl.UserDaoImpl@17550481   //userDao对象实例地址
org.itHeima.service.impl.UserServiceImpl@735f7ae5 //userService对象实例地址
```

以上为BeanFactory方法创建Bean对象，可以见到代码略多，不易记忆，而且功能较少，接下来我们将使用也是开发中常用的`ApplicationContext`方法来创建实例Bean。

### 使用ApplicationContext创建Bean

xml配置文件不用改变，我们创建一个`ApplicationContextTest`测试类来测试，在main方法中输入以下内容

```java
ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
UserService userService = (UserService) applicationContext.getBean("userService");
System.out.println(userService);
```

运行后将输出与上方BeanFactory方法运行相一致的结果。

### ApplicationContext与BeanFactory的区别

那么为什么在工作中常常使用`ApplicationContext`来进行Bean的创建呢，一个是代码简单易于记忆，而另外就是它与`BeanFactory`有着截然的不同。

- ApplicationContext脱身于BeanFactory，又青出于蓝而胜于蓝，比传统的BeanFactory多出了很多功能，包括监视器、国际化等。我们在它的类继承图中也可以看到，它实现了`BeanFactory`接口，又实现了其他一些功能性的接口，加强了自身的功能。

![image-20230316193239887](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg@master/2023/03/upgit_20230316_1678966398_appliactionContext继承关系图.png)

- BeanFactory更应该称之为一个Spring工厂，负责生产和管理Bean，而ApplicationContext更高级一些，可以被称之为Spring容器。
- Bean的初始化时机不同，在默认情况下，ApplicationContext在读取配置文件的同时就创建好了Bean并添加到了Bean池中，而BeanFactory是在首次`getBean`时才会对对象实例并初始化，这里可以进行调试验证，分别在`ApplicationContextTest`测试类中的读取配置文件与`BeanFactoryTest`测试类中读取配置文件处打上断点，进行调试。会发现在`ApplicationContextTest`执行完毕配置文件读取后，单例Bean池内已经存在了`userService`对象

​	![image-20230316194433029](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg@master/2023/03/upgit_20230316_1678967097_applicationContext加载Bean时机.png)

​	而调试`BeanFactoryTest`测试类时我们发现，再执行完毕加载配置文件后，单例池内并没有出现`userService`对象，而在执行`getBean`方法时，才正式加到了单例池内。如下图

​	![image-20230316195349951](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg@master/2023/03/upgit_20230316_1678968914_BeanFactory方法加载时机1.png)

​	执行`getBean`方法后

​	![image-20230316195506908](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg@master/2023/03/upgit_20230316_1678968904_BeanFactory方法加载时机2.png)

## 小结

今天学习了Spring中常见的Bean生产方式，分为`BeanFactory`与`ApplicationContext`方式，后者更为常用与强大。

虽然如今SpringBoot与SpringCould如火如荼，但其底层仍为Spring框架，之前只是使用SpringBoot进行项目开发，并未从源码角度进行思考，只是会用的级别，此次从新学习Spring有了更多感悟。
