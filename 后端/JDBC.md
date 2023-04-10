# 概述

JDBC API 允许用户访问任何形式的表格数据，尤其是存储在关系数据库中的数据



执行流程：

- 连接数据源，如：数据库。
- 为数据库传递查询和更新指令。
- 处理数据库响应并返回的结果。



# 基本步骤



**加载驱动程序：**

~~~java
Class.forName(driverClass)
//加载MySql驱动
Class.forName("com.mysql.jdbc.Driver")
//加载Oracle驱动
Class.forName("oracle.jdbc.driver.OracleDriver")
~~~



获得数据库连接

~~~java
// jdbc:mysql:ip地址/端口/数据库名
DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/imooc", "root", "root");
~~~



创建Statement\PreparedStatement对象：

~~~java
conn.createStatement();
conn.prepareStatement(sql);
~~~



完整实例：

~~~java
public class UserDao {
    private String jdbcUrl = "jdbc:mysql://localhost:3306/students_db";
    private String username = "root";
    private String password = "123456";

    public boolean insertUser(String name , String phone , String psd , String gender) {
        try {
            // 加载数据库驱动程序
            Class.forName("com.mysql.cj.jdbc.Driver");

            // 建立数据库连接
            Connection connection = DriverManager.getConnection(jdbcUrl, username, password);

            // 创建 PreparedStatement 对象
            String sql = "insert into user(name, phone, psd, gender) VALUES (?,?,?,?)";
            PreparedStatement statement = connection.prepareStatement(sql);

            // 设置参数值
            statement.setString(1, name);
            statement.setString(2, phone);
            statement.setString(3, psd);
            statement.setString(4, gender);
        	  // 执行插入语句
            int rows = statement.executeUpdate();
            // 关闭 PreparedStatement 和数据库连接
            statement.close();
            connection.close();
            if (rows > 0) {
                System.out.println("插入成功");
                return true ;
            } else {
                System.out.println("插入失败");
                return false ;
            }
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        }

        return true ;
    }

}

~~~



# Statement\PreparedStatement

创建：

~~~
conn.createStatement();
conn.prepareStatement(sql);
~~~

**Statement:**

- Statement是一个简单的接口，它用于执行静态SQL语句。
- 它通过使用Connection对象的createStatement()方法创建。
- Statement对象的执行方法包括executeQuery()和executeUpdate()，分别用于执行查询语句和更新语句。

~~~java
// 创建连接
Connection conn = DriverManager.getConnection(url, username, password);
// 创建Statement对象
Statement stmt = conn.createStatement();
// 执行查询操作
ResultSet rs = stmt.executeQuery("SELECT * FROM users");
// 遍历查询结果集
while (rs.next()) {
    int id = rs.getInt("id");
    String name = rs.getString("name");
    int age = rs.getInt("age");
    System.out.println("id: " + id + ", name: " + name + ", age: " + age);
}
// 执行插入操作
int rows = stmt.executeUpdate("INSERT INTO users(name, age) VALUES('Tom', 20)");
System.out.println(rows + " rows inserted.");
// 执行更新操作
int rows = stmt.executeUpdate("UPDATE users SET age = 21 WHERE name = 'Tom'");
System.out.println(rows + " rows updated.");
// 执行删除操作
int rows = stmt.executeUpdate("DELETE FROM users WHERE name = 'Tom'");
System.out.println(rows + " rows deleted.");
// 关闭连接
stmt.close();
conn.close();
~~~



**PreparedStatement:**

- PreparedStatement是一个预编译的接口，它继承自Statement接口。
- PreparedStatement对象可以用于执行动态SQL语句，即SQL语句中包含参数的情况。
- PreparedStatement对象在执行时会被预编译，可以提高执行效率。
- PreparedStatement对象使用Connection对象的prepareStatement()方法创建，创建时需要传入SQL语句作为参数。在预编译后，可以通过设置参数的方式执行SQL语句。
- PreparedStatement对象的执行方法包括executeQuery()和executeUpdate()，与Statement对象相同。

PreparedStatement对象比Statement对象更加灵活，更加安全，也更加高效。在开发过程中，应该尽量使用PreparedStatement对象来执行SQL语句



~~~java
// 创建连接
Connection conn = DriverManager.getConnection(url, username, password);
// 创建PreparedStatement对象
PreparedStatement pstmt = conn.prepareStatement("INSERT INTO users(name, age) VALUES(?, ?)");
// 设置参数
pstmt.setString(1, "Tom");
pstmt.setInt(2, 20);
// 执行插入操作
int rows = pstmt.executeUpdate();
System.out.println(rows + " rows inserted.");
// 修改参数
pstmt.setInt(2, 21);
// 执行更新操作
int rows = pstmt.executeUpdate("UPDATE users SET age = ? WHERE name = ?");
System.out.println(rows + " rows updated.");
// 设置参数
pstmt.setString(1, "Tom");
// 执行删除操作
int rows = pstmt.executeUpdate("DELETE FROM users WHERE name = ?");
System.out.println(rows + " rows deleted.");
// 关闭连接
pstmt.close();
conn.close();
~~~



# DriverManager

DriverManager是Java中用于管理JDBC驱动程序的类。它提供了一组静态方法，用于获取数据库连接和注册JDBC驱动程序。

**获取数据库连接：**

当需要与数据库建立连接时，可以使用DriverManager提供的 **getConnection()** 方法获取一个Connection对象。

- **getConnection()** 方法：需要传入数据库连接的URL、用户名和密码等信息，然后根据URL中指定的数据库类型，自动选择相应的驱动程序进行加载和连接。



**注册JDBC驱动程序：**

DriverManager还提供了一个registerDriver()方法，用于注册JDBC驱动程序。在使用JDBC驱动程序时，需要先将其注册到DriverManager中，然后才能使用驱动程序提供的功能。

注册驱动程序的方式有两种：

- 一种是使用Class.forName()方法加载驱动程序类，并调用DriverManager的registerDriver()方法进行注册；

~~~java
// 加载MySQL驱动程序类
Class.forName("com.mysql.cj.jdbc.Driver");
// 创建MySQL数据库连接
String url = "jdbc:mysql://localhost:3306/test";
String username = "root";
String password = "123456";
Connection conn = DriverManager.getConnection(url, username, password);
// 注册MySQL驱动程序
Driver driver = new com.mysql.cj.jdbc.Driver();
DriverManager.registerDriver(driver);
~~~



- 另一种是直接调用DriverManager的registerDriver()方法，传入一个驱动程序实例。

~~~java
// 创建MySQL数据库连接
String url = "jdbc:mysql://localhost:3306/test";
String username = "root";
String password = "123456";
Connection conn = DriverManager.getConnection(url, username, password);
// 注册MySQL驱动程序
Driver driver = new com.mysql.cj.jdbc.Driver();
DriverManager.registerDriver(driver);
~~~



# Connection

Connection是Java中表示数据库连接的接口。它提供了一系列方法，用于管理和操作数据库连接。

在使用JDBC连接数据库时，需要先获取一个Connection对象。通常情况下，可以使用DriverManager类提供的静态方法getConnection()来获取Connection对象,在获取Connection对象时，需要传入数据库连接的URL、用户名和密码等信息，然后根据URL中指定的数据库类型，自动选择相应的驱动程序进行加载和连接。

Connection对象是**线程不安全的**，因此在多线程环境下需要进行同步处理。同时，使用完Connection对象后，需要调用close()方法将其关闭，以释放数据库连接和相关资源。



**Connection对象可以用于执行SQL语句、提交事务、设置自动提交模式、设置事务隔离级别等操作。**

- 获取执行者对象
  - 获取普通执行者对象：Statement createStatement();
  - 获取预编译执行者对象：PreparedStatement prepareStatement(String sql);
- 管理事务
  - 开启事务：setAutoCommit(boolean autoCommit); 参数为false，则开启事务。
  - 提交事务：commit();
  - 回滚事务：rollback();
- 释放资源
  - 立即将数据库连接对象释放：void close();

~~~java
// 创建MySQL数据库连接
String url = "jdbc:mysql://localhost:3306/test";
String username = "root";
String password = "123456";
Connection conn = DriverManager.getConnection(url, username, password);
try {
    // 关闭自动提交
    conn.setAutoCommit(false);
    // 执行一系列SQL语句
    Statement stmt = conn.createStatement();
    stmt.executeUpdate("INSERT INTO users(name, age) VALUES('Tom', 20)");
    stmt.executeUpdate("UPDATE users SET age = 21 WHERE name = 'Tom'");
    stmt.executeUpdate("DELETE FROM users WHERE name = 'Tom'");
    // 提交事务
    conn.commit();
} catch (SQLException e) {
    // 回滚事务
    conn.rollback();
    e.printStackTrace();
} finally {
    // 关闭连接
    conn.close();
}
~~~

- 论事务提交还是回滚，都需要关闭Connection对象以释放数据库连接和相关资源。



**Connection对象还可以获取相关的元数据信息，例如数据库的名称、版本号等。**

~~~java
// 获取数据库元数据
DatabaseMetaData metaData = conn.getMetaData();
// 获取数据库名称
String dbName = metaData.getDatabaseProductName();
// 获取数据库版本号
String dbVersion = metaData.getDatabaseProductVersion();
~~~



# ResultSet

ResultSet 是 Java 中表示查询结果的接口。当使用 Statemen t或 PreparedStatement 对象执行SQL查询语句时，返回的结果就是一个 ResultSet 对象。

ResultSet 对象包含了查询结果的所有数据，可以通过 ResultSet 对象的方法逐行读取查询结果。



- ResultSet对象提供了一系列的getXXX()方法，用于**获取查询结果中的不同类型的数据**:
  - getInt()、getString()、getDate()

- ResultSet对象还提供了一些方法，用于**遍历查询结果**:
  - next()、previous()、first()、last()

- ResultSet对象还可以通过getMetaData()方法获取查询结果的元数据信息
  - 列名、数据类型、列数



~~~java
// 创建MySQL数据库连接
String url = "jdbc:mysql://localhost:3306/test";
String username = "root";
String password = "123456";
Connection conn = DriverManager.getConnection(url, username, password);
// 创建Statement对象
Statement stmt = conn.createStatement();
// 执行查询语句
String sql = "SELECT id, name, age FROM users";
ResultSet rs = stmt.executeQuery(sql);
// 遍历查询结果
while (rs.next()) {
    // 获取查询结果中的数据
    int id = rs.getInt("id");
    String name = rs.getString("name");
    int age = rs.getInt("age");
    // 打印查询结果中的数据
    System.out.println("id: " + id + ", name: " + name + ", age: " + age);
}
// 获取查询结果元数据
ResultSetMetaData metaData = rs.getMetaData();
// 获取查询结果列数
int columnCount = metaData.getColumnCount();
System.out.println("Column count: " + columnCount);
// 获取查询结果列名
for (int i = 1; i <= columnCount; i++) {
    String columnName = metaData.getColumnName(i);
    System.out.println("Column name: " + columnName);
}
// 关闭连接
rs.close();
stmt.close();
conn.close();
~~~





ResultSet对象一般情况下是只读的，不能直接修改查询结果中的数据

如果需要修改查询结果中的数据，可以使用可更新的ResultSet对象，或者使用PreparedStatement对象的updateXXX()方法来执行UPDATE或DELETE等修改操作。



**可更新的ResultSet对象：**

可更新的ResultSet对象是一种特殊的ResultSet对象，它允许对查询结果中的数据进行修改、插入和删除等操作。

可更新的ResultSet对象中的数据修改不会立即影响数据库中的数据，只有在调用ResultSet对象的**updateRow()**方法之后，才会将修改的结果保存到数据库中。

**创建可更新的ResultSet对象：**

- Statement对象的createStatement()方法，并指定ResultSet对象的可更新性

~~~java
// 创建可更新的ResultSet对象
Statement stmt = conn.createStatement(ResultSet.TYPE_SCROLL_SENSITIVE, ResultSet.CONCUR_UPDATABLE);
ResultSet rs = stmt.executeQuery("SELECT * FROM users");
~~~



创建可更新的ResultSet对象之后，就可以:

- 通过ResultSet对象的**updateXXX()**方法修改查询结果中的数据

- 通过调用ResultSet对象的**updateRow()**方法将修改的结果保存到数据库中

~~~java
// 修改可更新的ResultSet对象中的数据
rs.absolute(2);
rs.updateString("name", "Tom");
rs.updateInt("age", 25);
rs.updateRow();
~~~

- ResultSet对象的absolute(n)方法：将游标移动到第n行数据

- 可更新的ResultSet对象对于数据库的性能和安全性等方面也有一定的影响，需要谨慎使用。











































































