---
title: Spring AOP（@AspectJ）
date: 2018-09-26 14:10:05
tags: [spring,java,AOP]
categories: [spring] 

---
# AOP 面向切面编程

## 分类
目前Spring上使用两种代理：基于接口的JDK动态代理、基于CGLib字节码动态代理
* 基于接口的代理：声明需要被增强的接口，并将增强逻辑织入类中具体实现该接口的方法。
* 基于CGLib的代理：生成一个继承原来类的代理类，将增强逻辑写入新的代理类的具体方法。

## 组成：
* 连接点：类中的方法入口
* 切点：连接点中被选择切入的那部分点
* 增强：细化切入的方位信息，如before，after，around，并实现相应的切面逻辑代码
* 引介：一种特殊的增强，如果说普通的增强是修改了方法的运行时逻辑，那么引介则是向类中添加新的方法。通过引介我们能动态为一个类实现新的接口方法，让这个类变成该接口的实现类
* 切面：通过切点和增强（引介）结合组成，用于对一系列方法进行修改和加工
* 代理：当一个类被AOP增强后产生的结果类，在Spring中对该类的访问将通过代理进行。

<!--more-->

## 增强类型：
* 前置增强：`beforeAdvice`，在切点执行之前执行的增强逻辑
* 后置增强：`afterAdvice`，在切点执行之后执行的增强逻辑
* 环绕增强：`aroundAdvice`，包括了`beforeAdvice`和`afterAdvice`，通过调用连接点的执行方法（一般是`proceed()`）执行方法原本逻辑
* 异常抛出增强：`throwsAdvice`，在方法抛出异常之后执行的增强逻辑
* 引介增强：特殊的增强类型，用于引入新的接口方法，不太常用

## 切点类型：
* 静态方法切点： 应用 -> 静态普通方法名切面、静态正则表达式切面
* 动态方法切点：与静态方法切点的区别是其除了要匹配方法名之外，还会对入参做一定的校验，只有匹配方法名并且入参满足要求的方法会被捕捉，对性能影响比较大。应用 -> 动态切面
* 注解切点
* 表达式切点
* 流程切点：当使用某个流程切点时，其流程切面的目标是该切点方法中所有调用的方法，即我们通过该切点方法调用的其他方法会被织入增强。应用 -> 流程切面。
* 复合切点：多个不同类型切点的结合，比较复杂。

## 切面类型
* 一般切面：匹配所有连接点，一般不直接使用
* 具有切点的切面：通过特定的切点执行切面逻辑，灵活性强
* 引介切面：对应引介增强的特殊切面，应用在类的层面上

# @AspectJ的使用
## @Aspect
类注解。将该注解所标注的类标识为一个切面。必须加在包含增强的类上，否则类中的增强将无法使用。
## @Pointcut
方法注解。作用是命名切点。在确定切点的时候可以用带该注释的方法代替。通过命名切点可以定义切点的使用范围(public等)。  

```java
@Pointcut("execution(........)")
public void aspect(){}

@Pointcut("@annotation(........)")
public void aspect(){}

@Pointcut("whthin(........)")
public void aspect(){}
```

## 切点表达式函数
>
>在表达一个切点的时候可以使用以下通配符：
> > `*` 代表任意长度的字符串，仅表示一个元素  
> > `..` 代表任意多个上下文中的元素，在表示入参时可单独使用，表示类路径时必须和*同时使用  
> > `+` 跟在类后面，表示继承和扩展该类的所有类（包括该类） 
> 
> ececution和within支持所有通配符    
> @开头的不支持任何通配符  
> 其余的仅支持+通配符

### 方法切点函数
* `execution()`：入参为匹配模式字符串，筛选方法名符合模式串的所有方法。
其表达式为execution(<修饰符>? <返回类型> <方法名>(<参数>)  <异常>? )  

	```java
	execution(public * *(..)) 		//筛选所有公共的方法
	execution(String *Service(int)) 		//筛选所有返回值为String，以Service为结尾	的函数名和入参为一个int的方法
	note：?代表这个修饰符可以有也可以没有，没有则筛选所有类型的（相当于填入*）
	```
	
* `@annotation()`：入参为方法注解类，筛选所有带该注解的方法。  

	```java
	@annotation(cn.demo.MethodAnnotation)
	```

### 方法入参切点函数
* `args()`：参数为入参的类名，筛选入参中存在该类和所有子类的方法。  

	```java
	args(cn.demo.DemoClass)
	```

* `@args()`：参数为入参的类型注解，筛选入参中存在被该注解标注的类(及方法定义中的其子类)的方法。比较难理解。  

	```java
	@args(cn.demo.ClassAnnotation)
	```

### 目标类切点函数
* `within()`：入参为匹配模式字符串，筛选类名符合模式串的所有类中的所有方法。

	```java
	within(cn.*.DemoClass)		//匹配所有以cn开头、中间任意的DemoClass类中的方法
	within(..DemoClass)			//匹配任意DemoClass类中的方法
	within(cn.demo..*)			//匹配demo包下的所有类中的方法
	```

* `target()`：入参为类名，筛选该类以及继承该类的子类中的所有方法。

	```java 
	target(cn.demo.DemoClass)
	```

* `@within()`：入参为类型注解，筛选被该注解标注的类以及这个类的所有子类中的所有方法。其比@target范围更广
* `@target()`：入参为类型注解，筛选被该注解标注的类的所有方法。

### 代理类切点函数
* `this()`：入参为类名，筛选目标类的所有连接点，与target()不同的是，如果目标类引介了接口，this()能访问到新接口中的方法，target()无法访问。


## 切点复合运算、逻辑运算符
&& == and，|| == or， ! == not (注意not不能在表达式头，需要空格)  

```java
@annotation(cn.demo.DemoAnnotation) && execution(* demoMethod(..)) 筛选所有被标注了DemoAnnotation竹节的demoMethod方法。
```
	

## 增强类型
* `@Before`：前置增强
* `@AfterReturning`：后置增强
* `@Around`：环绕增强
* `@AfterThrowing`：抛出增强
* `@After`：Final增强，不管是正常退出还是抛出异常，该增强都会得到执行，一般用来释放资源。
* `@DeclareParents`：引介增强   

	```java
	// 引介增强例子: 
	@DeclareParents(value=“waiter” 	// 为waiter类添加接口实现
   		  , defaultImpl = SmartSeller.class  // 默认的接口实现类
 			)
	public Seller seller;  <- 要添加的接口	
	```

```java
/*******增强的使用方法*********/

@Pointcut("execution(........)")
public void aspect(){}

@Before("aspect()")  ==  @Before("execution(........)")
```

# 访问连接点信息
任何一个切面的增强方法都可以将第一个入参声明为JoinPoint来获得连接点上下文信息。@Around可以使用`ProceedingJoinPoint`。

## 类JoinPoint
* `Object[] getArgs()`：获得连接点方法运行时的参数列表。
* `Signature getSignature()`：获取连接点的方法签名
* `Object getTarget()`：获取连接点所在的目标对象
* `Object getThis()`：获取代理对象本身

## ProceedingJoinPoint
* `Object proceed()`：通过反射执行连接点方法
* `Object proceed(Object args[])`：通过反射执行连接点方法，使用新的入参来代替原来的入参

## 绑定连接点方法入参/绑定代理对象/绑定类注解对象
当使用`args(), this(), target(), @args(), @within(), @ratget(), @annotation()`时，我们不仅可以指定类名，还可以指定参数名。若使用参数名，切点函数需要结合增强得到真实的切点表达式（参数类型实际上是通过增强的入参拿到的）。如args()可用于绑定连接点方法的入参，@args()用于绑定连接点方法入参的注解。  
`@Before(“args(name, value)”)`  这里的name和value为变量名
`Public void beforeAdvice(String name, int value){…}`  name和value的真实属性从增强的方法入参中得到

## 绑定返回值/绑定抛出的异常
后置增强中，可以通过`returning`绑定返回值，绑定的返回值名字要作为增强的入参，返回的类型也由入参类型决定。 

```java
@AfterReturning(value="target(cn.demo.DemoClass)", returning="ret")
public void invoke(int ret){}
```  
抛出异常增强中，可以通过`throwing`绑定异常变量。

```java
@AfterThrowing(value="target(cn.demo.DemoClass)", throwing="e")
public void invoke(Exception e){}
``` 

## 无法使用AOP代理的例子
对于基于接口代理的AOP来说，接口的方法必然是public的，这就要求实现类的实现方法也必须是public的，同时不能有static修饰符。其能实现接口代理的方法只能是使用public或public final修饰符的方法。
对于基于CGLib代理的AOP来说，只能被子类覆盖的方法能织入增强逻辑。使用final、static、private修饰符的方法无法被子类覆盖，也就不能被子类进行AOP增强。
