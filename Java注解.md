---
title: Java 注解
date: 2018-09-26 14:04:05
tags: [java, 注解]
categories: [java]

---

# Java 注解
## 什么是注解
注解相当于代码的标签，可以被加在代码中类、属性、方法、参数甚至和包中。注解标记了相应代码块的性质，使得这部分代码能被对应的注解捕获器捕获并进行逻辑操作，然而注解本身只是信息的载体，并无逻辑可言。（据说现在jdk已经支持注解中加入宏）  

有了注解，Java的反射支持更多的扩展。
<!--more-->
## 声明一个注解
```java
@Target({ElementType.TYPE,ElementType.METHOD})	//注解应用目标：类、方法
@Retention(RetentionPolicy.RUNTIME)		//注解的生命周期：持续到运行时
@Component
public @interface DemoAnnotation {
    String[] value();				//String数组型成员变量value
    int index() default 1;			//int型成员变量index，默认值为1
}
```
* 注解声明修饰符：`@interface`
* 注解成员声明：    例：`int value();`
	1. 无入参， `int value(int v)`是非法的
	2. 可以指定常量默认值， 如 `int value() default 1;`
	3. 成员类型只能定义为以下类型：
		* 原始类型：`int, double, char`等
		* 原始类型封装类：`Integer, Double`等
		* `String, Class(不是class)`, 枚举, 注解
		* 上述类型的数组类型
* 元注解：Java中预定义的注解，影响注解类的行为
	*  `@Retention`：定义注解的保留期限(生命周期)，保存在`RetentionPolicy`。
		1. `SOURCE`：注解仅保留在原码文件，对应的字节码文件不再保留。
		2. `CLASS`：注解保留到字节码中，但不会被加载到JVM中，运行期是访问不到该注解的。
		3. `RUNTIME`：注解保留至运行期，可在运行期通过反射获取类的该注解信息。
	* `@Target`：定义注解的应用目标，保存在ElementType。可以有多个值。
		1. `TYPE`(类型注解)：(类，接口，注解类，枚举)声明处。
		2. `FIELD`(域值注解)：类成员变量和常量声明处。
		3. `METHOD`(方法注解)：方法声明处。
		4. `PARAMETER`(参数注解)：参数声明处。
		5. `CONSTRUCTOR`(构造函数注解)：构造函数声明处。
		6. `LOCAL_VARIABLE`(局部变量注解)：局部变量声明处。
		7. `ANNOTATION_TYPE`(注解类注解)：注解类声明处。在TYPE中。
		8. `PACKAGE(包注解)`：包声明处。
* 如果注解类成员中有value成员变量，那么仅对value进行赋值时可以不写赋值符号。如`@Temp(value="1)`可以写成`@Temp("1")`。注解类中仅有一个成员变量时建议取名为value。
* 注解的使用主要通过反射进行。

## 注解的使用
```java
@DemoAnnotation({"hello","world"})
public void someMethod(){...}
```
> 针对注解的反射以及拦截请参考Spring AOP