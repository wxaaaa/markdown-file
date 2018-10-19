---
title: Hibernate基本使用
date: 2018-10-19 21:20:00
tags: [数据库, Hibernate]
categories: [数据库]

---

# Hibernate基本使用
## 1 使用Hibernate的步骤
1. 创建Hibernate的配置文件:hibernate.properties或hibernate.cfg.xml
2. 创建需要存入数据库的持久化类
3. 创建对象-关系映射文件（mapping）
4. 通过Hibernate API进行对数据库的访问


## 2 Hibernate配置
### 2.1 maven包引入
```xml
    <!--hibernate引入-->
    <dependency>
        <groupId>org.hibernate</groupId>
        <artifactId>hibernate-core</artifactId>
        <version>5.3.6.Final</version>
    </dependency>
    
    <!--jdbc驱动，如果使用的是mysql则引入-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>6.0.6</version>
    </dependency>
```

<!--more-->

### 2.2 创建配置文件
#### 2.2.1 配置文件格式
Hibernate的配置文件有两种形式，一种是以hibernate.properties命名的配置文件；另一种是以hibernate.cfg.xml命名的xml文件。两者虽然在写法上有所不同，但是具体的配置和取值都是相同的，两者均需要位于应用的Classpath中（或者src文件目录下）。  

```
# hibernate.properties文件形式

hibernate.connection.driver_class=com.mysql.cj.jdbc.Driver
hibernate.connection.url=jdbc:mysql://127.0.0.1:3306/test?useSSL=false&useUnicode=true&characterEncoding=utf8
hibernate.connection.username=root
hibernate.connection.password=root
hibernate.connection.pool_size=5
hibernate.connection.autocommit=true
hibernate.dialect=org.hibernate.dialect.MySQL5Dialect
hibernate.hbm2ddl.auto=update
```
<span id="mapping"></span>

```xml
# hibernate.cfg.xml文件形式

<hibernate-configuration>
    <session-factory >
        <property name="hibernate.connection.driver_class">com.mysql.cj.jdbc.Driver</property>
        <property name="hibernate.connection.url">jdbc:mysql://127.0.0.1:3306/test?useSSL=false&amp;useUnicode=true&amp;characterEncoding=utf8</property>
        <property name="hibernate.connection.username">root</property>
        <property name="hibernate.connection.password">root</property>
        <property name="hibernate.dialect">org.hibernate.dialect.MySQL5Dialect</property>
        <property name="hibernate.hbm2ddl.auto">update</property>

        <!--注意mapping可以在这里进行注册，没有注册的mapping文件需要在代码中导入。-->
        <mapping resource="hbm/User.hbm.xml"/>
    </session-factory>
</hibernate-configuration>

```
#### 2.2.2 配置项
一些需要定义的配置：  
`hibernate.connection.driver_class`--JDBC 驱动程序类。 
`hibernate.connection.url`--数据库实例的 JDBC URL，注意xml格式中的特殊字符转码。  
`hibernate.connection.username`--数据库用户名。 
`hibernate.connection.password`--数据库密码。   
`hibernate.dialect`--数据库方言。 
`hibernate.hbm2ddl.auto`--数据库同步方式，在SessionFactory创建时，自动检查数据库结构。  
 
上述设置能保证实现数据库管理的基本功能，下面是一些可选的功能更炫酷的配置。 
`hibernate.show_sql`--输出所有SQL语句到控制台。建议开发过程中设置为true，便于跟踪Hibernate状态。   
`hibernate.format_sql`--将按照一定格式输出的SQL语句。 
`hibernate.connection.autocommit`--允许被缓存的JDBC连接开启自动提交。 

当然还有更多的配置，可以上网查找。

## 3 创建持久化类
持久化类是指需要被Hibernate持久化到数据库中的类。持久化类通常是域模型中的实体域类。持久化类应符合JavaBean规范，具有一些属性，并具有对应的set和get方法。如下是一个持久化类。  

```java
import lombok.Data;

@Data       // @Data注解为项目所有属性在编译时添加set和get方法
public class User {
    private String id;
    private String name;
    private String password;
    private int age;
}

```
> 注意持久化类中的set和get方法可以是任意的访问级别。如果不希望其中的某些属性被业务逻辑访问（比如id属性），可以将其的set方法设置为private。  

Hibernate要求持久化类必须提供一个不带参数的默认构造方法用来对该对象进行动态代理(返回一个实体类对象时会用到，比如使用hql的query)。一般情况下这个构造方法的访问级别可以是任意的。

## 4 创建对象-关系映射文件
Hibernate使用xml格式文件指定对象和关系数据间的映射。一般文件命名为`类名.hbm.xml`。例如创建如下User.hbm.xml:

```java
<!--DTD,可以查看该xml文件的规范-->
<?xml version="1.0"?>
<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">

<hibernate-mapping>
    <!--这里将User类映射到数据库中的user表中, 如果没有设置表名，则用类名作为表名。下同-->
    <class name="pack.User" table="user">
        <!--以属性id座位数据库中的主键-->
        <id name="id">
            <!--对象标识符生成器-->
            <generator class="uuid"/>
        </id>

        <!--其余属性的映射-->
        <property name="name" column="name" not-null="true" type="string"/>
        <property name="password" column="password" not-null="true"/>
        <property name="age" column="age"/>
    </class>
</hibernate-mapping>

```
当不指定数据库中字段的名字时，将取属性名字代替；当不指定type时，将按照属性的类型做相应的转化。

## 5 操纵数据库
当我们完成了配置文件，持久化类和对应的mapping映射文件后，我们就可以通过Hibernate API来实现与数据库的通信。
### 5.1 Hibernate初始化
1. 创建一个Configuration实例。Configuration类的构造方法会把classpath下的hibernate.properties文件中的配置信息读入内存。

    ```java
    //读取classpath下的hibernate.properties文件。
Configuration cfg = new Configuration();

    //读取classpath下的hibernate.cfg.xml文件。
Configuration cfg = new Configuration().configure()

    //读取指定路径下(这里时resource目录)的hibernate.cfg.xml文件。
Configuration cfg = new Configuration().configure("resource/hibernate.cfg.xml")

    ```
2. 将mapping文件导入。如果是采用在[hibernate.cfg.xml](#mapping)文件中设置则无需另外导入。

    ```java
    //加载classpath下的User.hbm.xml文件
    cfg.addClass(User.class);
    
    //加载指定目录下的User.hbm.xml文件(这里是hbm文件夹)
    cfg.addResource("hbm/User.hbm.xml");
    ```
3. 调用Configuration类的buildSessionFactory方法，创建SessionFactory实例
### 5.2 获得SessionFactory实例并获得Session

    ```java
    SessionFactory factory = cfg.buildSessionFactory();
    Session session = factory.openSession();
    ```
### 5.3 Session接口
    ```java
    //开始一个事务
    Transaction tx = session.beginTransaction();
    ...
    //保存user对象
    session.save(user);
    //更新数据库中的user对象
    session.update(user);
    //删除数据库中对应的user对象
    session.delete(user);
    
    //数据库查询语句
    Query query = session.createQuery("HQL语句");
    List resultList = query.getResultList();
    或
    NativeQuery nativeQuery = session.createNativeQuery("SQL语句");
    List resultList = nativeQuery.getResultList();
    
    //提交这个事务
    tx.commit();
    
    //回滚事务
    tx.rollback();
    
    //关闭session
    session.close();
    ```
