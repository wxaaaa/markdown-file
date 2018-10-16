# Hibernate学习--对象持久化
## 对象持久化技术
### 直接通过JDBC API进行对数据库的访问
Java应用访问数据库最直接的方式就是通过JDBC API来访问数据库。java.sql包提供了JDBC API接口，包括以下内容。  
`DriveManager`--驱动程序管理器，负责创建数据库的连接。  
`Connection`--代表数据库连接。  
`Statement`--负责执行数据库语句。  
`PrepareStatement`--负责执行数据库语句。  
`ResultSet`--代表SQL语句的查询结果集

然而直接使用JDBC会导致业务层代码被大量数据库操作，不仅导致代码结构不清晰、可读性差，也导致了后期的维护变得更加困难。
### ORM--对象关系映射
ORM(Object-Relation Mapping)，在单个组件中负责所有实体域对象的持久化，封装数据访问细节。
#### 对象-关系映射概念：
ORM解决的要问题就是对象-关系的映射。域模型和关系数据模都分别建立在概念模型的基础上。域模型是面向对象的，关系数据模型是面向关系的，一般情况下，一个持久化类和一个表对应，类的每个实例对应表中的一条记录。表间列举了面向对象概念和面向关系概念之间的基本映射。    
![ORM](https://github.com/wxaaaa/img/blob/master/orm.png?raw=true)  
关于如何实现具体的关系（如类与类之间的包含，继承等关系）映射，稍后再讲。
#### ORM中间件--mapping映射
ORM中间件采用元数据来描述对象-关系映射细节，元数据通常采用XML格式，并且存放在专门的对象．关系映射文件中。如果希望把ORM软件集成到自己的Java应用中，用户首先要提供对象-关系映射文件@。同ORM软件的元据的
语法不一样，以下是利用Hibernate来映射Customer类和CUSTOMERS表的元数据代码：  

```xml
<?xml version="1.0"?>
<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">

<hibernate-mapping>
    <!--对user类映射到数据库中的user表-->
    <class name="User" table="user">
        <!--主键映射-->
        <id name="id">
            <generator class="uuid"/>
        </id>
        
        <!--以下均是类的属性映射-->
        <property name="name">
            <column name="name" not-null="true"/>
        </property>

        <property name="password">
            <column name="password" not-null="true" />
        </property>

        <property name="age">
            <column name="age"/>
        </property>
    </class>
</hibernate-mapping>
```
#### Session会话管理
Session接口向业务逻辑层提供了读、写和删除域对象的方法．它不公开任何数据访问细节，Sessionlmpl类实现了该接口。SessionFactory类负责创建Session实例。Hibemate在初始化阶段把对象-关系映射文件中的映射元数据读入到SessionFactory的缓存中。  
至此开发人员可以无需编写具体的SQL语句，而通过Session来查询或更新数据库。
#### ORM软件列表
![ORM Software](https://github.com/wxaaaa/img/blob/master/ORMSoftware.png?raw=true)

### 主动域对象模式
在对象中封装该对象所对应的数据库的访问细节。业务中进行数据库访问只需要调用类中封装的方法。若关系数据模型发生改变，只需要修改对应类中的方法即可，无需修改业务代码。  
然而还是需要涉及SQL语句的编写，由于每个类负责自身的SQL实现，其中通用的SQL语句无法提取，会造成大量代码冗余。

### JDO模式
![JDO](https://github.com/wxaaaa/img/blob/master/JDO.png?raw=true)