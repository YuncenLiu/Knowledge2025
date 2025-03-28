[TOC]



# 1、MyBatis 简介

1. Mybatis 开源免费框架，原名叫 iBatis，2010 在 google code，2013年迁移到 github 
2. 作用：数据访问层
     1. 底层是对 JDBC 的封装。

2、在src下新建全局配置文件（编写JDBC四个变量）

​	1、没有名称和地址要求

​	2、在全局配置文件中引入 DTD 或 schema   （这就是提示）

## 1.1、环境搭建

创建一个 JavaEE 的项目，把Mybatis3 的jar包导入到项目中

### 1.1.1、全局配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 配置环境 -->
    <environments default="mysql">
        <!-- 配置mysql的环境-->
        <environment id="mysql">
            <!-- 配置事务的类型-->
            <transactionManager type="JDBC"></transactionManager>
            <!-- 配置数据源（连接池） -->
            <dataSource type="POOLED">
                <!-- 配置连接数据库的4个基本信息 -->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/ssm"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="com/bjsxt/mapper/FlowerMapper.xml"/>
    </mappers>
</configuration>
```

### 1.1.2、 新建Mapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="a.b">
    <!-- id 方法名
        parameterType:定义参数类型
        resultType返回值类型

        如果方法返回是list，在resultType中写List 的泛型
        因为mybatis对jdbc封装，一行一行读取数据
    -->
    <select id="selAll" resultType="com.bjsxt.pojo.Flower">
        select * from flower
    </select>
</mapper>
```

### 1.1.3、测试

```java
InputStream is = Resources.getResourceAsStream("mybatis.xml");
// 使用工厂设计模式
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(is);
// 生产SqlSession
SqlSession session = factory.openSession();
List<Flower> list = session.selectList("a.b.selAll");
for (Flower flower : list) {
    System.out.println(flower.toString());
}
session.close();
```

> ==剖析一下 Mapper.xml==
>
```xml
<select id="selAll" resultType="com.bjsxt.pojo.Flower">
        select * from flower
</select>
```
>
> ​	  我们通过语句查询的数据，会被Mybatis  自动映射到 resultType 里，这时，我们的 resultType 里的属性要和 数据库名字完全相同，否则哪一个不同，查询出来的那一项就会为空值。     
>
> ​		举个栗子    ：  我们把 Flower 的  name  属性改为  name123   不做任何改变，直接执行，我们得到的name值是 null
> ​				要想解决这个办法，我们可以在  `Mapper.xml` 上里 把语句改成    
>
```xml
<select id="selAll" resultType="com.bjsxt.pojo.Flower">
        select id,name name123,price,production  from flower
</select>
```
>
> ​	利用别名的方式，把值映射进去，有别名就映射别名，没有别名，就不变



## 1.2、环境搭建详解

### 1.2.1、全局配置文件中内容

1. <transactionManager>     ==(来源于API)==

     - JDBC – 这个配置直接简单使用了 JDBC 的提交和回滚设置。它依赖于从数据源得到的连接来管理事务范围。 

     - MANAGED – 这个配置几乎没做什么。它从来不提交或回滚一个连接。而它会让容器来管理事务的整个生命周期(比如 Spring 或 JEE  应用服务器的上下文) 默认情况下它会关闭连接。然而一些容器并不希望这样, 因此如果你需要从连接中停止它,将 closeConnection 属性设置为  false。例如: 

```xml
<transactionManager type="MANAGED">
<property name="closeConnection" value="false"/>
</transactionManager>
```

          > 如果用了这个，我们只能把 事务管理交给 Spring 来管理

2. <dataSource>      ==(来源于API)==

     + POOLED  使用数据连接池
     + UNPOOLED  不使用数据链接池，和直接使用 JDBC 一样
     + JNDI    java命名目录接口技术

## 1.3、数据库连接池

1. 在内存中开辟一块空间，存放多个数据库连接对象

2. JDBC Tomcat Pool，直接由 tocmat 参数数据库连接池

3. 状态：

     1. active  当前连接对象被应用程序使用
     2. ldle     等待应用程序使用

4. 使用数据库连接池的目的

     在高评率访问数据库时，使用数据库连接池可以降低服务器系统压力，提示程序运行效率

     小项目不使用数据库连接池技术
     
5. 实现 JDBC tomcat pool 的步骤

     1. 在web项目的 META-INF 中的 

          1. 使用 context  + JNDI 技术 获取到数据库内容在数据库中编写相关属性：

```xml
<?xml version="1.0" encoding="utf-8"?>
<Content>
    <Resource
            driver="com.mysql.jdbc.Driver"
            url="jdbc:mysql://localhost:3306/ssm"
            username="root"
            password="root"
            maxActive="50"
            maxIdle="20"
            name="test"
            auth="Container"
            maxWait="10000"
            type="javax.sql.DataSource"
        />
</Content>
```

     2. 把项目发不到的tomcat中，数据库就产生了

6. 可以在java中使用 jndi 获取数据库连接池对象

     1. Context：上下文接口   context.xml 文件对象类型

     2. 代码：

 ```java
 Context ctx = new InitialContext();
 DataSource ds = (DataSource)ctx.lookup("java:comp/env/test");
 Connection conn = ds.getConnection();
 PreparedStatement ps = conn.prepareStatement("select * from flower");
 ResultSet rs = ps.executeQuery();
 resp.setContentType("text/html;charset=utf-8");
 PrintWriter out = resp.getWriter();
 while (rs.next()){
     out.print(rs.getInt(1)+"&nbsp;&nbsp;&nbsp;&nbsp;"+rs.getString(2)+"<br/>");
 }
 
 out.print("success");
 out.flush();
 out.close();
 rs.close();
 ```

          > java:comp/env/  是固定字符串 后面 test 是固定名称

     3. 当关闭连接对象时，把连接对象归还给数据库连接池，把状态改成  ldle

## 1.4、三种查询方式

```xml
<mapper namespace="a.b">
<!-- id 方法名
    parameterType:定义参数类型
    resultType返回值类型

    如果方法返回是list，在resultType中写List 的泛型
    因为mybatis对jdbc封装，一行一行读取数据
-->
    <select id="selAll" resultType="com.bjsxt.pojo.Flower">
        select * from flower
    </select>

    <select id="selById" resultType="int">
        select count(*) from flower
    </select>

    <select id="c" resultType="map">
         select * from flower
    </select>
</mapper>
```

1. selectList()  返回值为 List<resultType 属性控制>  

     1. 适用与查询结果都需要遍历的需求

     ```java
     List<Flower> list = session.selectList("a.b.selAll");
     for (Flower flower : list) {
         System.out.println(flower.toString());
     }
     ```

2. selectOne() 返回值 object
     1. 适用与繁华结果只是一个变量或一行数据时

     ```java
     int count = session.selectOne("a.b.selById");
     System.out.println(count);
     ```

3. SelectMap()  返回值 Map

     1. 适用于需要在查询结果中通过某个值取到这行数据的需求
     2. Map <Key, resultType 控制>

     ```java
     Map<Object, Object> map = session.selectMap("a.b.c", "name123");
     System.out.println(map);
     ```

     > 经过我的发现，这样也行
     >
     > ```java
     > Map<Object, Flower> map = session.selectMap("a.b.c", "name");
     > System.out.println(map);
     > ```

## 1.5、实际案例

​	见 files 文件内 的 People 项目

