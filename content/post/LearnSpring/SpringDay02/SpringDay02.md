---
title: "SpringDay02"
date: 2023-03-17T16:55:36+08:00
slug: Spring学习 02
categories:
    - 学习
    - Spring
---

# 基于XML的Bean配置

## 基础属性

### BeanName与别名

bean标签除了id属性指定唯一标识外，还允许使用name属性指定多个别名

```xml
<bean id="userDao"  name="aaa,bbb,ccc" class="org.itHeima.Dao.impl.userDaoImpl" />
```

此时，除了使用userDao可以获取Bean之外，还可以使用aaa、bbb、ccc来获取，此时这些别名都将保存在单例池中的别名集合中

```java
UserDao userDao = (UserDao) applicationContext.getBean("aaa");
```

![image-20230317174627405](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg@master/2023/03/upgit_20230317_1679046389_image-20230317174627405.png)

假如在声明时并未指定id、name属性，那么此时通过类路径也就是class属性中的内容来获取，除此以外，也可以直接通过类型匹配来获取

```java
//通过类路径
UserDao userDao = (UserDao) applicationContext.getBean("org.itHeima.Dao.impl.userDaoImpl");
//通过类型匹配
UserDao userDao = applicationContext.getBean(UserDao.class);
```



### Bean的作用范围

初始引入`spring-context`包时，Bean的作用范围只有两个，分别为`singleton(单例)`与`prototype(原型)`

#### singleton(单例)

顾名思义，在整个Bean的生命周期内，只会产生一个实例化对象，每次使用getBean都获取的是同一个对象，这也是spring默认的Bean作用域。

```java
UserService userService = (UserService) applicationContext.getBean("userService");
UserService userService2 = (UserService) applicationContext.getBean("userService");
```

![image-20230317182250723](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg@master/2023/03/upgit_20230317_1679048574_image-20230317182250723.png)

#### prototype(原型)

其表示一个Bean对应多个实例对象，并且在读取配置文件时并不会创建Bean对象，而是在每次使用getBean方法获取Bean实例时才会创建，并且每次getBean都会创建并返回一个新的Bean对象。

![image-20230317182555421](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg@master/2023/03/upgit_20230317_1679048756_image-20230317182555421.png)

### Bean的延迟加载

如果使用的是BeanFactory方式创建bean对象时，Bean是在使用getBean方法时实例化，而在使用ApplicationContext方式创建时，Bean是在读取配置文件时实例化，通过`lazy-init`属性则可以控制这一过程。其取值有`default`、`false`与`true`三种。

延迟加载只能修改ApplicationContext方式，对BeanFactory方式不起效。

```java
<bean id="userService" class="org.itHeima.service.impl.UserServiceImpl" lazy-init="true" />
```

运行后，调试发现在读取配置文件时并没有实例化对象和存入单例池。

![image-20230317184410698](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg@master/2023/03/upgit_20230317_1679049851_image-20230317184410698.png)

只有当调用getBean方法时，才会实例化并放入单例池。

### 初始化方法和销毁方法

Spring支持在Bean实例化时执行一些初始方法，并在其销毁前执行销毁方法，使用`init-method`与`destroy-method`属性来指定执行的方法名称。

在UserServiceImpl类中我们添加两个方法。

```java
public UserServiceImpl() {System.out.println("无参构造执行...");}
public void init() {System.out.println("init方法执行...");}
public void destroy() {System.out.println("destroy方法执行...");}
```

在xml配置文件中，我们使用`init-method`与`destroy-method`属性来指定方法。

```xml
<bean id="userService" class="org.itHeima.service.impl.UserServiceImpl" init-method="init" destroy-method="destroy">
```

执行后可以看到以下输出：

![image-20230317185136006](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg@master/2023/03/upgit_20230317_1679050297_image-20230317185136006.png)

在结果中我们可以得知init方法是在构造方法之后才会执行，并且并没有输出销毁方法。这是为什么呢，这是因为程序并没有显式的关闭，而是自动停止运行，就好像突然停电，电脑内的一部分数据都没来得及进行保存。所以我们要显式的关闭才会执行销毁方法。

修改main方法中的代码，由于ApplicationContext并没有声明显式关闭方法，所以我们直接使用它的接口实现类`ClassPathXmlApplicationContext`中的`close`方法。

```java
ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
applicationContext.close();
```

运行后就可以看到以下输出

![image-20230317185641804](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg@master/2023/03/upgit_20230317_1679050604_image-20230317185641804.png)

### initializingBean方法初始化

除了使用`init-method`属性指定初始化方法外，Spring还允许对象通过实现`InitializingBean`接口中的`afterPropertiesSet`方法来进行初始化。

```java
public class UserServiceImpl implements UserService, InitializingBean{
    //...其他方法
	@Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("afterPropertiesSet方法执行....");

    }
}
```

执行后可以有以下输出：

![image-20230317191342073](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg@master/2023/03/upgit_20230317_1679051623_image-20230317191342073.png)

这里我们也可以看到，`afterPropertiesSet`方法的执行是在`init-method`属性指定的初始化方法前执行的。

### 实例化Bean的方法

#### 静态工厂方法

类似于单例工厂模式

新建一个`MyBeanFactory`类，此类有一个静态方法`getBean`来获取一个`UserServiceImpl`实例化对象

```java
public class MyBeanFactory {
    public static UserService getBean() {
        return new UserServiceImpl();
    }
}
```

在xml文件中进行配置

```xml
<bean id="userServiceFactory" class="org.itHeima.factory.MyBeanFactory" factory-method="getBean" />
```

此配置并不会将`MyBeanFactory`加入单例池，而是会自动执行工厂内的getBean方法来返回UserServiceImpl实例化对象

![image-20230317201052885](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg@master/2023/03/upgit_20230317_1679055056_image-20230317201052885.png)

#### 实例工厂方法

修改上述`MyBeanFactory`类，将静态方法修改为普通方法。

同时修改xml配置为

```xml
<bean id="userServiceFactory" class="org.itHeima.factory.MyBeanFactory" />
<bean id="userService" factory-bean="userServiceFactory" factory-method="getBean"/>
```

再次运行会产生和上述一样的结果,区别在于将我们的`MyBeanFactory`类也加入了单例池中

![image-20230317201814909](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg@master/2023/03/upgit_20230317_1679055497_image-20230317201814909.png)

#### 带参静态工厂与实例工厂方法

如果我们的实例化对象在构造时存在参数的传入(带参构造，Bean实例化必须存在一个无参构造)，此时我们可以使用`constructor-arg`标签来指定参数名称(`name`属性)与数值(`value`属性)。

修改上述自定义工厂的getBean方法为带参方法

```java
public UserService getBean(String name, int age) {
    System.out.println("name:" + name + "\n" + "age:" + age);
    return new UserServiceImpl();
}
```

修改xml文件，在获取对应实例对象的bean标签下添加

```xml
<bean id="userService" factory-bean="userServiceFactory" factory-method="getBean">
    <constructor-arg name="name" value="test"/>
    <constructor-arg name="age" value="18" />
</bean>
```

运行后即可正常输出

## 小结

Spring博大精深啊

