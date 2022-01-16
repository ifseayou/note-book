# Spring Data

## 是什么

Spring Data是Spring的一个子项目，用于简化数据库访问，支持**NoSQL** 和关系型数据库，其主要的目标是使得数据库的访问更加的快捷方便。支持 `redis,mongodb,hbase等`，支持的关系数据存储结束有：

* JDBC
* JPA

### JDBC

如下图：一开始，市面上出现了很多数据库，例如MySQL，Oracle，等，Java程序如果想要访问数据库，需要这些数据库提供一套API，让Java程序进行访问，此时产生一个问题就是操作不同的数据库的方法是不一样的，特别的麻烦，此时SUN公司说：这样做不好，于是他就出了一个规范，这个规范叫做JDBC，JDBC实际上就是定义了一些接口，其实并没有实现，然后Java程序如果在想访问数据库，直接调用JDBC的接口就行了。而这些接口的实现由各个数据库的厂商来提供接口的实现类。这些实现类打成了一个***jar包***，也就是***JDBC驱动*** 。所以说：

**JDBC是一种规范，统一了Java程序访问数据库的规范**

![1564241062060](img/sdt/1.png)

**例子：使用Java连接MySQL数据库**

查询的目标数据库信息：

![](img/sdt/3.png)

首先，Java如果要连接MySQL需要驱动包，在Maven下，导入依赖即可：

~~~xml
<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.47</version>
</dependency>
~~~

代码：

~~~java
package com.isea.learn.jdbc;

import java.sql.*;

/**
 * @author isea_you
 * @date 2019/8/22
 * @time 16:50
 * @target:
 */
public class JDBCDemo {
    // MySQL 8.0 以下版本 - JDBC 驱动名及数据库 URL
    static final String JDBC_DRIVER = "com.mysql.jdbc.Driver";
    static final String DB_URL = "jdbc:mysql://localhost:3306/test";

/*     MySQL 8.0 以上版本 - JDBC 驱动名及数据库 URL
    static final String JDBC_DRIVER = "com.mysql.cj.jdbc.Driver";
    static final String DB_URL = "jdbc:mysql://localhost:3306/test?useSSL=false&serverTimezone=UTC";*/

    // 数据库的用户名与密码，需要根据自己的设置
    static final String USER = "root";
    static final String PASS = "hailong1996";

    public static void main(String[] args) {
        Connection conn = null;
        Statement statement = null;
        try{
            // 注册 JDBC 驱动
            Class.forName(JDBC_DRIVER);

            // 打开链接
            System.out.println("连接数据库...");
            conn = DriverManager.getConnection(DB_URL,USER,PASS);

            // 执行查询
            System.out.println(" 实例化Statement对象...");
            statement = conn.createStatement();
            String sql;
            sql = "SELECT bookid,card FROM book WHERE card = 1;";
            ResultSet resultSet = statement.executeQuery(sql);

            // 展开结果集数据库
            while(resultSet.next()){
                // 通过字段检索
                int bookid  = resultSet.getInt("bookid");
                int card = resultSet.getInt("card");

                // 输出数据
                System.out.print("bookID: " + bookid);
                System.out.print(",card : " + card);
                System.out.println();
            }
            // 完成后关闭
            resultSet.close();
            statement.close();
            conn.close();
        }catch(SQLException se){
            // 处理 JDBC 错误
            se.printStackTrace();
        }catch(Exception e){
            // 处理 Class.forName 错误
            e.printStackTrace();
        }finally{
            // 关闭资源
            try{
                if(statement!=null) statement.close();
            }catch(SQLException se2){
            }// 什么都不做
            try{
                if(conn!=null) conn.close();
            }catch(SQLException se){
                se.printStackTrace();
            }
        }
        System.out.println("Goodbye!");
    }
}
~~~

最后的结果：

![](img/sdt/4.png)

### JPA

**Java Persistence API** ： 用于对象持久化的API，JavaEE5.0平台的ORM规范，使得应用程序可以统一的方式访问持久层。**ORM** ： 也即为 对象关系映射。下图中展示了对于**JPA**规范的不同实现，就好比我们买车，车的接口就是能开，四个轮子，跑的快，这就有很多实现，有宝马，奔驰，大众等。

JPA只是一种ORM规范，提供了一些编程的接口，但是他具体的实现是有ORM厂商来实现的，

![1564242298495](img/sdt/2.png)



## JPA Spring Data

### 是什么

致力于减少数据访问层（DAO）的开发量，开发者唯一需要做的，就是声明持久层的接口，其他的都由Spring Data JPA来帮助完成。

