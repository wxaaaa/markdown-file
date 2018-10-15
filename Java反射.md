---
title: java 反射
date: 2018-09-26 14:04:05
tags: [java, 反射]
categories: [java]

---

# Java 反射

## 反射的意义
在正常的编程流程中，一旦我们编写好了代码并编译成字节码文件运行，我们便无法获取运行中的对象的具体属性和方法。如果我们需要动态的获取或者修改这个类（切面经常这么搞）就只能通过反射去解决。

## 类反射
### 类获取
* 通过对象获取：`obj.getClass()`
* 通过类获取：`Object.class`
* 通过类路径名获取：`Class.forName("com.xxx.xxx”)`

<!--more-->

## 成员变量反射
### 获取成员变量
* 获取包括父类的public成员变量：`Class:: public Field[] getFields()`
* 根据变量名获取包括父类的public成员变量：`Class:: public Field getField(String name)`
* 获取当前类所有成员变量：`Class::public Field[] getDeclaredFields()`
* 根据变量名获取当前类所有成员变量：`Class::public Field getDeclaredField(String name)`
* 成员变量赋值：`Field::public void set(Object obj, Object value)`,		obj为类实例对象.   
	
	> 准备赋值的成员变量是private，需要设置访问：Field::public void setAccessible(boolean flag), 		flag为true

* 得到成员变量的描述信息：`Field::public String toGenericString()`


## 类方法反射
### 获取类方法
* 获取包括父类的public方法：`Class::public Method[] getMethods()`
* 根据变量名和入参获取包括父类的public方法：`Class::public Method getMethod(String name, Class<?>... parameterTypes)`
* 获取当前类的所有方法：`Class::public Method[] getDeclaredMethods()`
* 根据变量名和入参 获取当前类的所有方法：`Class::public Method getDeclaredMethod(String name, Class<?>... parameterTypes)`
* 方法调用:`Method::invoke(Object obj, Object... args)`,		obj为类实例对象，args为入参.   

	> 如果调用的方法是private，需要设置访问：Method::public void setAccessible(boolean flag), flag为true  

* 得到类方法的描述信息：`Method::public String toGenericString()`
* 获取类方法参数信息：`Method::public Parameter[] getParameters()`
	> 参数名：Parameter::public String getName()		如果需要输出具体的名称，需要加编译开关-parameters，并删除编译完成的项目文件  
	
	> 参数类型： Parameter::public Class<?> getType()

### 构造函数
* 公共无参构造器：`Class::public Constructor<?>[] getConstructors() `
* 公共带参构造器：`Class::public Constructor<T> getConstructor(Class<?>... parameterTypes)`
* 忽略修饰符无参构造器：`Class::public Constructor<?>[] getDeclaredConstructors()`
* 忽略修饰符带参构造器：`Class::public Constructor<T> getDeclaredConstructor(Class<?>... parameterTypes)`
* 构造函数调用：`Constructor::public T newInstance(Object... initargs)`  
	
	> 如果调用构造函数是private，需要设置访问：Constructor::public void setAccessible(boolean flag)， flag为true。

## 内部类获取
* 获取包括父类的内部定义的public类以及接口：`Class::public Class<?>[] getClasses()`
* 获取当前类内部定义的类以及接口：`Class::public Class<?>[] getDeclaredClasses()`

## 注解
* 获取当前数据的某一注解（包括继承：`(Class, Field, Method, Constructor)::public <T extends Annotation> T getAnnotation(Class<T> annotationClass)`
* 获取当前数据的直接定义某一的注解：`(Class, Field, Method, Constructor)::public <T extends Annotation> T getDeclaredAnnotation(Class<T> annotationClass)`
* 获取当前数据的所有注解（包括继承)：`(Class, Field, Method, Constructor)::public <T extends Annotation> T[] getAnnotations()`
* 获取当前数据的所有注解：`Class, Field, Method, Constructor::public <T extends Annotation> T[] getDeclaredAnnotations()`

# 反射实例：交换AB的值
## 描述
> 观察如下代码，编写`swap()`函数并在`swap()`函数中交换变量a, b的值。  

```java
public class Test{
	public static void main(String[] args){
		Integer a = 10;
		Integer b = 15;
		swap(a, b);
		System.out.println("a:" + a + ", b:" + b);
	}
	public void swap(Integer a, Integer b){...}
}
```

## 错误代码：  
```java
public void swap(Integer a, Integer b){
	Integer c = a;
	a = b;
	b = c;
}
```

> 错误原因：函数入参是对入参变量的引用。这个赋值操作其实只是将`swap`中指向a,b的引用(可以认为是C++中的指针)给指到b和c，作用范围仅在`swap`函数内。由于`main`函数中的a,b还是访问原来的地址，`main`中的a,b值并未发生任何改变。`Integer`是final类型的，用常规的方法无法改变其中的内容。  
我们观察`Integer`中存放值的属性，是`private`变量。我们需要更改`value`的值以达到交换两个变量值的目的。

	```java
	/**
     * The value of the {@code Integer}.
     *
     * @serial
     */
    private final int value;
	```

## 正确代码：  

```java
public void swap(Integer a, Integer b) throws Exception {
    Integer c = new Integer(a);             //后续会对a的值进行更改，c需要new一个新的空间存储，直接赋值会引用a的存储地址
        
    Class aClazz = a.getClass();            //获取a的Class
    Field aValue = aClazz.getDeclaredField("value");    //获得其中的value属性
    aValue.setAccessible(true);             //由于value属性是private，设置值前要开放对它的访问
    aValue.set(a, b);                       //对a的value属性设置b的值

    Class bClazz = b.getClass();            //b的操作同a
    Field bValue = bClazz.getDeclaredField("value");
    bValue.setAccessible(true);
    bValue.set(b, c);
}
```
