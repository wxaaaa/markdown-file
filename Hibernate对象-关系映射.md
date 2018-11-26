---
title: Hibernate基本使用
date: 2018-10-19 21:20:00
tags: [数据库, Hibernate]
categories: [数据库]

---

# Hibernate对象-关系映射
对象关系映射主要是靠*.hbm.xml映射文件进行设置。
## 类和属性的映射
### 属性映射的访问策略
`<propery>`标签中的access属性指定属性的访问方式，取值有以下两种：  

* `property`：默认值，hibernate通过反射调用与name字段相关的set()和get()方法进行值的存取。被映射的类中需要存在相应的set()和get()方法。这样做的好处是可以在存取的过程中执行其他的逻辑操作，甚至不需要类中存在相应的属性。
* `field`：hibernate直接通过反射属性进行属性的存取，简单暴力无需其他方法依赖。  

虽然可以用基本类型映射到数据库，由于数据库中相关的字段可能为空，而java中的基本类型无法用null赋值，所以还是建议用封装类型进行映射。

<!--more-->

### 派生属性
派生属性指该属性依赖于其他属性，在数据库中无此属性字段。针对派生属性的映射有三种方式：  

1. 与数据库无关，通过代码逻辑在后期进行设置。
2. 映射文件中不进行映射，通过依赖的属性的set()和get()方法进行设置。
3. 在映射文件设置formula表达式进行查询。例如在属性中获取表中有多少条数据：`<property name="sum" formula="(select sum(*) from table t where t.a=A)" //这里的A为数据库的列名>`

### 预定义的sql语句
在hibernate中，插入、更新、删除和基础的查询语句都是预先定义好，存放在SessionFactory的缓存中，执行操作时从缓存直接拉取模板，填入值。在调用时拿插入来说，hibernate会默认将参与映射的所有属性都进行赋值（没有值的则为null），这也就是为什么当设置数据库某个属性为`not null default 0`时，不设置该字段的值会出现数据库插入异常，而不是0，入需要规避这种情况，请设置dynamic-insert为true。  

此外，可通过在映射文件中控制是否需要将该字段加入预先定义的sql。  
`设置price字段不可被更新：<property name="price" update="false" column="price">`  

![映射属性](https://raw.githubusercontent.com/wxaaaa/img/master/hibernate_attribute_mapping.png)
### 标识符
在表示列名的时候，如果列名种有特殊字符（或者空格），可使用标识符将其包括起来。
```<property name="price" update="false" column="`pr i_ce`">```
## 命名策略
通过继承`org.hibernate.cfg.DefaultNamingStragegy`或者`org.hibernate.cfg.ImprovedNamingStragegy`覆盖其部分方法即可实现自己的命名策略。
## 设置数据库schema
如果在数据库连接中并没有设置指定的数据库名，则可以在`<hibernate-mapping>`标签中设置schema属性来指定映射的数据库名。对于`<class>`标签也可以设置schema属性来指定映射的数据库，并且与hibernate-mapping可同时设置。
## 设置类的包名
如果说schema指定了选择数据库的来源，那么package则指定了class的来源。如果代码中有不同的包同时存在时（甚至有同名类时），设置package就能很便利的进行区分。设置方式和上面的schema差不多。
