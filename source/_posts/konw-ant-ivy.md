---
layout: post
title: "初识Ant和Ivy"
date: 2016-08-10 00:19:01
tags: 
		- Java 
		- Ant
		- Ivy
---

最近一段时间在IBM实习，做的是QA的工作，所用的测试工程是用Ant和Ivy来进行项目的依赖管理，所以有必要学习一下Ant和Ivy，有一个基本的了解。因此本篇博客不会介绍Ant和Ivy深入的知识和技术，只是对Ant和Ivy的基本介绍。
<!-- more -->
之前在学习Java的过程中，就听说过Ant和Ivy，当时还没有用工具来管理项目依赖的觉悟，一般的做法是需要什么Jar包，首先去官网或者其他地方下载下来， 然后在IDE中添加进去，相信现在大多数初学Java的同学还是这样做的。后面通过Maven了解到java的项目依赖管理，就把Maven作为项目管理构建工具，就没再去了解Ant和Ivy了。后面也会写一系列针对Maven的介绍和使用的文章。
 
### Ant
首先通过官网上来看，Ant是用Java编写的，现在发行最新的版本是1.9.7。Ant的主要用途是构建Java程序，内置一系列的编译、连接、测试和运行任务。同样也是可以高效的构建非Java程序，比如C或者C++程序。

安装Ant
（1）首先需要确保你安装了Java环境，并设置了环境变量。
（2）然后在官网上下载二进制版本。
（3）解压到安装的位置，然后设置环境变量ANT_HOME伟你解压的安装位置，并添加${ANT_HOME}/bin(Unix)或者%ANT_HOME%/bin到PATH中。
（4）打开cmd或者bash，输入ant –version，显示正确的Ant信息即安装成功。

Ant构建项目，主要是通过一个build.xml的文件来执行构建的。下面通过官网提供的一个简单的build.xml文件来大概分析下Buildfile的基本属性。

```xml
<project name="MyProject" default="dist" basedir=".">
  <description>
    simple example build file
  </description>
  <!-- set global properties for this build -->
  <property name="src" location="src"/>
  <property name="build" location="build"/>
  <property name="dist" location="dist"/>

  <target name="init">
    <!-- Create the time stamp -->
    <tstamp/>
    <!-- Create the build directory structure used by compile -->
    <mkdir dir="${build}"/>
  </target>

  <target name="compile" depends="init"
        description="compile the source">
    <!-- Compile the java code from ${src} into ${build} -->
    <javac srcdir="${src}" destdir="${build}"/>
  </target>

  <target name="dist" depends="compile"
        description="generate the distribution">
    <!-- Create the distribution directory -->
    <mkdir dir="${dist}/lib"/>

    <!-- Put everything in ${build} into the MyProject-${DSTAMP}.jar file -->
    <jar jarfile="${dist}/lib/MyProject-${DSTAMP}.jar" basedir="${build}"/>
  </target>

  <target name="clean"
        description="clean up">
    <!-- Delete the ${build} and ${dist} directory trees -->
    <delete dir="${build}"/>
    <delete dir="${dist}"/>
  </target>
</project>
```

由文件可看出，一个build.xml文件是由根标签project和众多的target标签组成的，一个Project有三个属性：name,default,basedir。name就不用说了，项目的名字，basedir表示项目的基准目录，default表示默认的运行目标，即指定默认的target（即任务）。
<target>标签
一个项目下可以有一个或者多个target标签。一个target标签可以依赖其他的target标签，depend后面填写所依赖的target名字就好了。
build.xml文件里面还有一些其他的标签，比如<mkdir>标签，表示创建一个目录，<jar>标签表示生成一个JAR文件。<javac>标签用于编译一个或一组java文件。<delete>标签用于删除一个或一组文件。

### Ivy
从Ivy的官方文档可以看出Ivy是一个灵活的且可配置的项目依赖管理工具，且和Apache Ant紧紧结合在一起。在使用Ivy前，需要安装Ant 1.6.0以上版本和JDK。Ivy默认使用maven 2的仓库来解决依赖。

```xml
<ivy-module version="2.0">
    <info organisation="org.apache" module="hello-ivy"/>
    <dependencies>
        <dependency org="commons-lang" name="commons-lang" rev="2.0"/>
        <dependency org="commons-cli" name="commons-cli" rev="1.0"/>
    </dependencies>
</ivy-module>
```

这个根节点ivy-module是表示使用的Ivy版本是2.0。
info标签表示我们定义的是那个模块的依赖。这儿定义了组织名为org.apache和项目模块名字hello-ivy。接下来就是定义依赖，这儿这个模块依赖两个jar:commons-lang和commons-cli。Ivy使用标签org和name来表示这个jar的组织名和模块名，rev表示需要的版本。对于Maven来说配置如下：

```xml
<dependency>
    <groupId>commons-lang</groupId>
    <artifactId>commons-lang</artifactId>
    <version>2.0</version>
</dependency>
```

我们不难发现，其实Ivy的配置和Maven是类似的，如果要把Maven的配置转换为Ivy的配置，只需把groupId换成org,artifactId换成name，version换成rev就可以了。

### Ant和Ivy的关系

简单来说，Ivy主要是解决Java项目的依赖JAR的问题，而Ant是来解决项目的构建、部署等问题。

在Ant和Ivy配合使用时，一定要把Ivy的jar放到Ant的lib目录下，不然执行Ant时会报错。
