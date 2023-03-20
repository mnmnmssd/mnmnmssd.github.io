---
title: "SpringDay03"
description: 
date: 2023-03-20T17:19:05+08:00
image: https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg@master/2023/03/upgit_20230320_1679308851_6.jpg
math: true
license: true
hidden: false
comments: true
draft: false
categories:
    - 学习
    - Spring
---

# 基于xml的Spring Bean配置

## 实现FactoryBean的规范延迟实例化Bean

之前都是使用自定义的工厂方法来实现静态工厂，其实SPring提供了一个工厂Bean的规范接口，只要实现这个接口就可以实现静态工厂，不够此接口只有在调用getBean方法时才会实例化Bean并放入缓存池中。

创建一个类并实现`FactoryBean`接口中的两个方法`getObject`与`getObjectType`

```java
public class MyBeanFactory2 implements FactoryBean<UserDao> {

    @Override
    public UserDao getObject() throws Exception {
        System.out.println("getObject被调用。。。");
        return new UserDaoImpl();
    }

    @Override
    public Class<?> getObjectType() {
        return UserDao.class;
    }
}
```

在xml中正常配置bean标签,`此时不在配置userDao对象的bean，而是配置对应的工厂对象bean`：

```xml
<bean id="userDao" class="org.itHeima.factory.MyBeanFactory2" />
```

运行后将产生如下结果：

![image-20230320172911013](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg@master/2023/03/upgit_20230320_1679304553_image-20230320172911013.png)

进入调试模式我们可以发现在读取xml配置文件时，并没有实例化`userDao`对象，而是将工厂对象进行实例化并放入单例池中：

![image-20230320173123069](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg@master/2023/03/upgit_20230320_1679304684_image-20230320173123069.png)

注意此时`userDao`对应的value为`MyBeanFactory2`。在调用getBean方法后，`userDao`对象将被实例化并放入缓存池中，下一次调用时将直接获取：

![image-20230320173311913](https://cdn.jsdelivr.net/gh/mnmnmssd/hexoBlogimg@master/2023/03/upgit_20230320_1679304793_image-20230320173311913.png)

## 注入方式和注入数据类型

常见注入方式包括构造器注入和setter方法注入，对应的标签也别分为两种

### constructor-arg

适用于构造方法注入或静态工厂方法注入

### property

适用于setter方法注入，name属性值为set后面内容(首字母小写)

```xml
//如setAge，xml中应写
<property name="age" value="18" />
```

### 数据类型与对应属性

#### 普通数据类型

如String、int、bool等数据类型，使用value属性即可

```xml
<constructor-arg name="name" value="xm"/>
<property name="age" value="18"/>
```

#### 引用数据类型

具体实例化对象等，使用ref属性(对应在配置文件中声明的bean的id属性)

```xml
<constructor-arg name="userService" ref="userService" />
```

## 注入集合数据类型

对应集合类型，使用对应的标签

```xml
//list
 <list>
        <value>dddd</value>
        <value>aaaa</value>
        <value>xxxx</value>
 </list>
//set
<set>
    <value>ddd</value>
    <value>xxx</value>
    <value>yyy</value>
</set>
//map
<map>
    <entry key="d1" value="userService"/>
    <entry key="d2" value="userService"/>
    <entry key="d3" value="userService"/>
</map>
//....
```



## 自动装配

在SpringBoot中常常使用自动注入@AutoWire注解，这个在xml配置中也是存在的。有两种常用属性，通过名字(id)注入或通过类型注入，在通过类型注入中，如果有超过两种以上相同类型的bean实例化，就会报错。

```xml
<bean id="userDao" class="org.itHeima.Dao.impl.UserDaoImpl" autowire="byName" />
//or
<bean id="userDao" class="org.itHeima.Dao.impl.UserDaoImpl" autowire="byType" />
```

## 命名空间的种类

在xml中可以指定多个命名空间,使用xmlns属性指定，其中`:`后内容为命名空间别名

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
	   xmlns:context="http://www.springframework.org/schema/context" <!-- 指定context为别名 -->
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans 
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context <!-- 设置路径 -->
       http://www.springframework.org/schema/context/spring-context.xsd">
</beans>
```

也可设置第三方的命名空间，只有有路径和地址即可。

## 默认命名空间的默认标签

### beans标签

最核心的标签，可以通过profile属性指定运行环境，在系统运行时可指定运行环境，即可选择对应环境内的bean

```xml
<beans profile="test">
	<bean id="userDao1" class="org.itHeima.dao.impl.userDaoImpl" />
</beans>
```

可在运行时指定环境

```java
System.setProperty("spring.profiles.active", "test");
```

其会加载默认空间的bean和test空间的bean。

### import标签

导入外部xml配置文件

```xml
<import resource="classpath:xxx.xml" />
```

### alias标签

起别名

```xml
 <alias name="userDao" alias="aaa,bbb,ccc"/>
```
