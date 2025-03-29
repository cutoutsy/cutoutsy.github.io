---
layout: post
title: Maven、IDEA构建SSH基本项目结构
date: 2017-05-26 21:49:53
categories: Java 学习
tags:
	- Maven
	- Idea
	- SSH
---
### 写在前面

最近在学习Java SSH，SSH相信学过Java的应该都知道吧，SSH是目前比较流行的一种Java Web应用程序开源框架，主要包括Spring、Struts2和Hibernate三个框架，这三个框架既可以单独使用，也可以整合在一起使用。Spring主要对应web开发中的service层，Struts2主要对应web层，Hibernate主要对应dao层。关于这三个框架的详细介绍我会在其他的博客文章中介绍，本文主要是在IntelliJ IDEA中，使用maven进行项目管理，搭建SSH基本项目结构。
<!-- more -->

### 主要软件版本

> * IntelliJ IDEA: 2016.2.4
> * Apache Maven: 3.3.3
> * Java version: 1.8.0_60
> * Spring: 4.2.4.RELEASE
> * Struts2: 2.3.24
> * Hibernate: 5.0.7.Final

### 创建Maven项目

![new project](http://7oxhal.com1.z0.glb.clouddn.com/create_project_1.png)

![new maven project](http://7oxhal.com1.z0.glb.clouddn.com/create_project_2.png)

点击next，接着填一些信息，创建一个新的web项目，项目的目录结构如下图所示：
![code_structure_1](http://7oxhal.com1.z0.glb.clouddn.com/code_struture_1.png)

我们发现没有代码目录和测试代码目录，我们需要手动创建，在main下创建java目录，并让目录为源代码目录，在src下创建test目录，并在test目录下创建java目录，把这个java目录作为测试代码目录，创建后项目目录结构如下：
![code_structure_2](http://7oxhal.com1.z0.glb.clouddn.com/code_struture_2.png)

之后我们在main/java写项目源代码，在test/java下写测试代码。

接下来我们在pom.xml中引入项目依赖，这些我都经过测试，中途也有缺少依赖导致项目运行报错，我都一一解决了，最终的pom.xml如下，直接复制即可：

### 编写pom.xml及web.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>io.cutoutsy</groupId>
  <artifactId>ssh</artifactId>
  <packaging>war</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>ssh Maven Webapp</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
      <!-- struts2-->
      <dependency>
          <groupId>org.apache.struts</groupId>
          <artifactId>struts2-core</artifactId>
          <version>2.3.24</version>
      </dependency>
      
      <!-- Hibernate -->
      <dependency>
          <groupId>org.hibernate</groupId>
          <artifactId>hibernate-core</artifactId>
          <version>5.0.7.Final</version>
      </dependency>

      <!-- spring -->
      <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-orm</artifactId>
          <version>4.2.4.RELEASE</version>
      </dependency>

      <!-- mysql lib-->
      <dependency>
          <groupId>mysql</groupId>
          <artifactId>mysql-connector-java</artifactId>
          <version>5.1.38</version>
      </dependency>

      <dependency>
          <groupId>com.mchange</groupId>
          <artifactId>c3p0</artifactId>
          <version>0.9.2.1</version>
      </dependency>

      <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-web</artifactId>
          <version>4.2.4.RELEASE</version>
      </dependency>

      <dependency>
          <groupId>org.apache.struts</groupId>
          <artifactId>struts2-spring-plugin</artifactId>
          <version>2.3.24</version>
      </dependency>

      <dependency>
          <groupId>org.hibernate</groupId>
          <artifactId>hibernate-entitymanager</artifactId>
          <version>5.0.7.Final</version>
      </dependency>

  </dependencies>
  <build>
    <finalName>ssh</finalName>
  </build>
</project>
```
接下来我们需要配置webapp/WEB-INF/web.xml,

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>ssh</display-name>

  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value>
  </context-param>
	<!-- 配置struts2过滤器 -->
  <filter>
    <filter-name>struts2</filter-name>
    <filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
  </filter>

  <filter-mapping>
    <filter-name>struts2</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
	<!-- 配置监听器 -->
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>

  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>index.jsp</welcome-file>
  </welcome-file-list>
</web-app>

```
这个配置文件中，除了我们平时熟悉的配置struts2的过滤器外，我还配置了监听器，指定spring配置文件位置，这样在项目启动的时候，服务器就会创建sessionFactory以及其他对象，这样网站初始访问时候就不会存在速度慢的情况了。

### 编写代码
接着我们来编写项目基本测试代码，在编写测试代码前，由于我们使用Hibernate，所以肯定会用到数据库，我们在数据库里面创建一个名为ssh的数据库，方便后面使用。我们首先在main/java/下创建4个包，分别为action、dao、entity、service。在entity下创建User.java类，代码如下：

```Java
package entity;

public class User {
    private int id;
    private String name;
    private String password;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}

```
同时我们在这个目录下，编写User的关系映射文件User.hbm.xml，内容如下：

```xml
<?xml version="1.0"?>
<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<hibernate-mapping>
    <class name="entity.User" table="t_user">
        <id name="id" column="id">
            <generator class="native"></generator>
        </id>

        <property name="name" column="name"></property>
        <property name="password" column="password"></property>
    </class>
</hibernate-mapping>
```

在dao下面创建接口UserDao，代码如下：

```Java
package dao;

import entity.User;

public interface UserDao {
    public String addUser(User user);
}

```
同时在这个目录下，编写它的实现类，先创建impl的包，然后创建UserDaoImpl类，代码如下，

```Java
package dao.impl;

import dao.UserDao;
import entity.User;
import org.springframework.orm.hibernate5.support.HibernateDaoSupport;

public class UserDaoImpl extends HibernateDaoSupport implements UserDao{

    public String addUser(User user) {
        getHibernateTemplate().save(user);
        return "success";
    }
}

```

在service下创建UserService类，代码如下：

```Java
package service;

import dao.UserDao;
import entity.User;
import org.springframework.transaction.annotation.Transactional;

@Transactional
public class UserService {

    private UserDao userDao;

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    public String addUser(User user){
        return userDao.addUser(user);
    }
}

```
在action下创建UserAction类，代码如下：

```Java
package action;

import com.opensymphony.xwork2.ActionSupport;
import entity.User;
import service.UserService;

public class UserAction extends ActionSupport{
    private UserService userService;
    public void setUserService(UserService userService) {
        this.userService = userService;
    }

    public String add(){
        User user = new User();
        user.setName("bob");
        user.setPassword("12345");
        userService.addUser(user);
        return "success";
    }
}
```


现在，我们代码部分已经编写完了，接下来就是三个框架的配置文件，关于struts2和spring整合主要是把struts2里面action对象的创建交给Spring进行管理，而hibernate和spring进行整合是把数据库信息配置到Spring中，且把Hibernate中sessionFactory的创建也交给Spring进行管理，同时让项目启动的时候由服务器进行创建，按照上述的原则，在resource下编写配置文件如下:
#### structs.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
        "-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"
        "http://struts.apache.org/dtds/struts-2.3.dtd">
<struts>
    <package name="demo1" extends="struts-default" namespace="/">
        <action name="user_*" class="userAction" method="{1}">
            <result name="success">/success.html</result>
        </action>
    </package>

</struts>
```

#### hibernate.cfg.xml

```xml
<!DOCTYPE hibernate-configuration PUBLIC
        "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>

        <property name="dialect">org.hibernate.dialect.MySQLDialect</property>
        <property name="show_sql">false</property>
        <property name="format_sql">true</property>
        <property name="hbm2ddl.auto">update</property>
        <property name="connection.useUnicode">true</property>
        <property name="connection.characterEncoding">utf8</property>

        <mapping resource="entity/User.hbm.xml"/>
    </session-factory>
</hibernate-configuration>

```

#### applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans.xsd
	http://www.springframework.org/schema/context
	http://www.springframework.org/schema/context/spring-context.xsd
	http://www.springframework.org/schema/aop
	http://www.springframework.org/schema/aop/spring-aop.xsd
	http://www.springframework.org/schema/tx
	http://www.springframework.org/schema/tx/spring-tx.xsd">

    <!-- 配置c3p0连接池 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
        <property name="jdbcUrl" value="jdbc:mysql://127.0.0.1:3306/ssh"></property>
        <property name="user" value="root"></property>
        <property name="password" value="123456"></property>
    </bean>

    <!-- sessionfactory创建 -->
    <bean id="sessionFactory" class="org.springframework.orm.hibernate5.LocalSessionFactoryBean">
        <property name="dataSource" ref="dataSource"></property>
        <property name="configLocations" value="classpath:hibernate.cfg.xml"></property>
    </bean>

    <!-- 事务管理器 -->
    <bean id="transactionManager" class="org.springframework.orm.hibernate5.HibernateTransactionManager">
        <property name="sessionFactory" ref="sessionFactory"></property>
    </bean>

    <tx:annotation-driven transaction-manager="transactionManager"/>

    <bean id="userDao" class="dao.impl.UserDaoImpl">
        <property name="sessionFactory" ref="sessionFactory"></property>
    </bean>
    <bean id="userService" class="service.UserService">
        <property name="userDao" ref="userDao"></property>
    </bean>

    <bean id="userAction" class="action.UserAction">
        <property name="userService" ref="userService"></property>
    </bean>
</beans>

```

上面的数据库信息是我本地的数据库信息，你要按照你的数据库信息进行配置。至此我们的配置文件也编写完成了，我们的测试代码是向数据库里面添加一条用户记录，添加成功则说明我们ssh整合配置没有问题，在添加成功后添加到页面success.html，所以我们需要在webapp下编写success.html，代码如下：

```html
<html>
<body>
<h2>Add Success!</h2>
</body>
</html>
```
### 测试
接下来我们就来测试我们的框架搭建是否是成功的，我们需要给项目配置一个tomcat，如果不清楚怎么在IDEA中给web项目配置tomcat服务器的，可以自行查询，或者在评论里面留言，这个不属于本文的内容。配置好了之后我们启动tomcat服务器，启动的时候不要忘了开启数据库服务，否则会报错，
![tomcat_start](http://7oxhal.com1.z0.glb.clouddn.com/tomcat_start.png)

由于我们在服务器启动的时候就会创建数据库sessionFactory，所以这个时候，服务器应该已经帮我们创建好t_user这个表了，到数据库里面看下
![mysql_table](http://7oxhal.com1.z0.glb.clouddn.com/mysql_table.png)

如果你的数据库里面没有这个表，请查看启动的时候是否有报错信息，然后我们去浏览器打开：[http://localhost:8080/](http://localhost:8080/)进行访问
![browser_index](http://7oxhal.com1.z0.glb.clouddn.com/browser_index.png)

接下来我们调用我们编写的action，添加用户，通过[http://localhost:8080/user_add](http://localhost:8080/)，成功结果如下：
![browser_add](http://7oxhal.com1.z0.glb.clouddn.com/browser_add.png)

然后我们去数据库里面查看用户是否添加成功，
![mysql_bob](http://7oxhal.com1.z0.glb.clouddn.com/mysql_bob.png)

可见，数据已经成功插入到数据库中。

### 结束
到此，我们已经成功在IDEA中使用maven搭建了SSH整合框架。接下来我们就可以编写我们的业务代码了，当然还有用maven打包项目，但这不属于本文要讲的内容，可能在后面的博客中我会写到。





