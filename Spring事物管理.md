---
title: Spring 事物管理
date: 2018-09-28 18:57:05
tags: [Spring]
categories: [java]

---

# Spring 事物管理

## 数据库事物基础知识

### 数据库并发问题
1. ***脏读***: A事务读取B事务尚未提交的更改数据，并在这个数据上进行操作。如果恰巧B事务将要发生回滚，则A读取到的事物是不被承认的。
2. ***不可重复读***: A事务和B事务处理同一个数据，A在查询数据的同时B在更改（或删除）数据，A事务内两次读取到的数据是不一样的。
3. ***幻象读***: A事务在查询数据，B事务在新增数据，如果B事务新增的数据恰好符合A事务的查询条件，则A事务进行两次查询的结果会不一致
4. ***第一类丢失更新***: A事务撤销时，把B事务已经提交的相同位置的更新数据覆盖
5. ***第二类丢失更新***: A事务提交时，把B事务已经提交的相同位置的更新数据覆盖

<!--more-->

### 数据库锁
锁定方式：共享锁定和独占锁定。  
共享锁定防止独占锁定，允许其他的共享锁定。独占锁定防止其他的共享锁定和独占锁定。  
具体可分为：行共享、行独占、表共享、表共享行独占、表独占。
一般来说数据库会根据用户输入的sql语句自动加锁。

### 事务隔离级别
事务隔离级别跟数据库并发性是对立的。越低级别的隔离策略拥有更高的并发性和吞吐量。
![](https://github.com/wxaaaa/img/blob/master/Transaction%20isolation%20level.png?raw=true)

# JDBC对事务的支持
## ThreadLocal
### ThreadLocal定义
`ThreadLocal`不是一个线程，而是线程的一个本地化对象。当处于多线程环境中的对象使用`ThreadLocal`维护变量时，`ThreadLocal`会为每个使用该变量的线程提供一个独立的副本。可以不通过线程同步在多线程环境下解决线程安全问题（可以看作是用线程为key的map）。

### ThreadLocal接口方法
* `void set(Object)` 		设置当前线程局部变量的值。
* `Object get()`			
* `void remove()`		删除该线程对应的局部变量。
* `Object initalValue()`	返回该线程变量的初始值。

> Spring中大多数Bean能使用singleton方式生成的原因是使用了ThreadLocal。

## 事务管理器
未看。。。

## 使用注解配置声明式事务
### @Transactional属性
* `propagation`：	事务传播行为，默认`PROPAGATION_REQUIRE`
* `isolation`：		事务隔离级别，默认`ISOLATION_DEFAULT`
* `readOnly`：		读写事务属性（bool），默认false （读/写）
* `timeout`：		超时时间（int），默认依赖底层事务系统的默认值
* `rollbackFor`：		一组异常类，遇到时进行回滚，多个异常之间用逗号分隔。默认所有运行期异常回滚，检查型异常不回滚
* `rollbackForClassName`：和`rollbackFor`不同的是这里用异常类名称。
* `noRollbackFor`：	一组异常类，遇到时不回滚
* `noRollbackForClassName`：参考上两条

> @Transaction可以被标注在接口定义和接口方法、类定义和类的public方法上。

## 事务传播机制
未看。。。