---
title: Hibernate API（概念） 
date: 2018-10-16 20:42:00
tags: [数据库, Hibernate]
categories: [数据库]

---
# Hibernate API（概念）
## Hibernate 接口类型
* 提供数据库操作接口（create, query, update等）
* 用于配置Hibernate的接口（Configuration）
* 使应用程序拦截Hibernate内部发生的事件，并做出相关回应，其实就是监听器（Interceptor, SaveEventListener等）
* 扩展Hibernate的接口  

Hibernate内部封装了JDBC，JTA和JDNI。

<!--more-->

## 核心接口
Hibernate的5个核心接口：  
### Configuration接口   
读取相关配置文件（hibernate配置文件和mapping）并导入配置。启动Hibernate。应用通过Configuration获得mapping文件中的元数据，创建SessionFactory对象。
### SessionFactory接口
一个SessionFactory对应一个数据存储源，换句话说连接多个数据库的场合需要相同数量的SessionFactory。应用从SessionFactory中获得Session。  
SessionFactory具有以下特点：  
1. 线程安全，一个SessionFactory实例（不是Session）可以被多个线程共享  
2. 具有很大的重量级，不能随意创建或销毁SessionFactory实例，并且需要一个很大的缓存来存放预定的SQL语句和映射元数据。

### Session接口
Session是Hibernate应用最广泛的接口，也被称作持久化管理器。提供了对象的增删查改等持久化操作。  
相对于SessionFactory，Session具有以下特点：  
1. 不是线程安全的。  
2. 不是重量级的。这意味着程序中可以经常创建和销毁一个Session，可以为每一个工作单元分配一个Session。

### Transaction接口
Transaction接口是Hibernate的数据库事务接口。Transaction对底层事务进行封装，使得Hibernate支持不同的数据库环境。

### Query和Criteria接口
是Hibernate的查询接口。Query包装了HQL（Hibernate Query Language）查询，HQL语句和SQL语句很相似，不同的是HQL语句是面向对象的，其用类名和对象名来代替表名和字段名。Criteria接口完全封装基于字符串形式的查询语句，擅长动态查询。

## 事件处理接口
* 事件及事件监听器。在Hibernate中，针对每一种事件都有其对应的监听器。如加载对象时会触发loadEvent事件，该事件由loadEventListener进行处理。
* 拦截器Interceptor。应用程序可以定义实现Interceptor的类，负责响应持久化类被加载，更新，删除等事件。

## 映射类型接口
org.hibernate.type.Type接口表示Hibernate的映射类型，用于把域对象映射为数据库中的关系数据。具体的Type类：  
    * PrimitiveType类--映射java的基本类型：ByteType、ShortType、IntegerType、LongType、FloatType、DoubleType、BooleanType、CharacterType。
    * DateType--映射日期类型。
    * BinaryType--映射Byte[]类型。  

## 可扩展接口
当需要配置更多高级的数据库功能时（如定制本地缓存机制，创建代理，定制事务管理等）需要用到。