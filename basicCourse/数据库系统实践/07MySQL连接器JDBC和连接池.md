# 数据库系统实践
## 07MySQL连接器JDBC和连接池
#### 1. JDBC概述
- JDBC(Java DataBase Connectivity, Java数据库连接)是一种用于执行SQL语句的Java API（Application Programming Interface，应用程序接口），可以为多种关系型数据库提供统一访问。
![JDBC概述](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/%E6%95%B0%E6%8D%AE%E5%BA%93%E7%B3%BB%E7%BB%9F%E5%AE%9E%E8%B7%B5-JDBC%E6%A6%82%E8%BF%B0.jpg?raw=true)
- JDBC 的基本功能如下：
    + 建立与数据库的连接；
    + 向数据库发送SQL语句； 
    + 处理从数据库返回的结果。
- 常见的JDBC驱动程序可分为4种类型
    + JDBC-ODBC Bridge driver
    + native-API, partly Java driver
    + JDBC-Net pure Java driver 
    + native protocol, pure Java driver
#### 2. JDBC连接过程
![JDBC连接过程](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/%E6%95%B0%E6%8D%AE%E5%BA%93%E7%B3%BB%E7%BB%9F%E5%AE%9E%E8%B7%B5-JDBC%E8%BF%9E%E6%8E%A5%E8%BF%87%E7%A8%8B.jpg?raw=true)

- JDBC是一种底层API，不能直接访问数据库
- 要通过JDBC来存取某一特定的数据库，必须依赖于相应数据库厂商提供的JDBC驱动程序
- JDBC驱动程序是连接JDBC API与具体数据库之间的桥梁
- 下载的JDBC驱动包，对于普通的Java Application应用程序，只需要将JDBC驱动包引入项目即可
- 对于Java Web应用，通常将JDBC驱动包放置在项目的WEB-INF/lib目录下
###### JDBC连接MySQL数据库步骤
1. 加载JDBC驱动程序
    + 加载驱动程序的方法是使用java.lang.Class类的静态方法forName(String className)`Class.forName("com.mysql.jdbc.Driver");` 
    + 若加载成功，系统将加载的驱动程序注册到DriverManager类中。如果加载失败，将抛出ClassNotFoundException异常，即未找到指定的驱动类
    + 加载MySQL驱动程序的完整代码如下  
```java
try {
   Class.forName(“com.mysql.jdbc.Driver”);  
} catch (ClassNotFoundException ex) {
   System.out.println("加载数据库驱动时抛出异常!");
   ex.printStackTrace();
}
```
2. 创建数据库连接
    + Connection接口代表与数据库的连接，只有建立了连接，用户程序才能操作数据库。
    + 一个应用程序可与单个数据库有一个或多个连接，也可以与多个数据库有连接
    + 与数据库建立连接的方法是调用DriverManager类的getConnection()方法
    + getConnection()方法的返回值类型为java.sql.Connection，如果连接数据库失败，将抛出SQLException异常，其方法调用如下:`Connection conn= DriverManager.getConnection(String url, String userName, String password);`
    + 依次指定要连接数据库的路径、用户名及密码，即可创建数据库连接对象
    + 若要连接BookStore数据库，则连接数据库路径的写法如下：jdbc:mysql://localhost:3306/bookstore
    + 也可以采用带数据库数据编码格式的方式：
jdbc:mysql://localhost:3306/bookstore?useUnicode=true&characterEncoding=gb2312
    *连接BookStore数据库的代码  
```java
try {
String url ="jdbc:mysql://localhost:3306/bookstore";
String user="root";    //访问数据库的用户名
String password="123456";    //访问数据库的密码
Connection conn= DriverManager.getConnection(url,user,password); 
System.out.println("连接数据库成功！");
} catch (SQLException ex) {
System.out.println("连接数据库失败！");
ex.printStackTrace();
}
```
3. 创建Statement对象
    + 连接数据库后，要执行SQL语句，必须创建一个Statement对象
        * 创建Statement对象
            - 利用Connection接口的createStatement()方法可以创建Statement对象，用来执行静态的SQL语句
            - 代码如下：`Statement stmt=conn.createStatement(); //conn为数据库连接对象`
        * 创建PreparedStatement对象
            - 利用Connection接口的prepareStatement(String sql)方法可以创建PreparedStatement对象，用来执行动态的SQL语句
            - 假设已经创建了数据库连接对象conn，创建PreparedStatement对象pstmt的代码如下：`String sql = "select * from users where u_id>? and u_sex=?";PreparedStatement pstmt = conn.prepareStatement(sql);`
            - 在执行该SQL语句前，需要对每个输入参数进行设置，设置参数的语法格式如下：`pstmt.setXxx(position,value);`
            - 若设置参数u_id的值为3，u_sex的值为“女”，代码如下：
                + pstmt.setInt(1,3);
                + pstmt.setString(2,"女");
4. 执行SQL语句
    + 创建Statement对象后，就可以利用该对象的相应方法来执行SQL语句，实现对数据库的具体操作
    + Statement对象的常用方法有executeQuery()、executeUpdate()等
    + ResultSet executeQuery(String sql)方法：该方法用于执行产生单个结果集的SQL语句，如SELECT语句，该方法返回一个结果集ResultSet对象
    + int executeUpdate(String sql)方法：该方法用于执行 INSERT、UPDATE 或 DELETE 语句以及SQL DDL（数据定义语言）语句。该方法的返回值是一个整数，表示受影响的行数。对于 CREATE TABLE 或 DROP TABLE 等不操作数据行的语句，返回值为0
    + 假设已经创建了Statement对象stmt，查询users表中的所有记录，并将查询结果保存到ResultSet对象rs中，则代码如下：`String sql = "select * from users";ResultSet rs= stmt.executeQuery(sql);`
    + 若要删除users表中u_id为7的记录，则代码如下：`String sql = "delete from users where u_id=7";int rows= stmt.executeUpdate(sql);`
    + PreparedStatement对象也可以调用executeQuery()和executeUpdate()两个方法，但都不需要带参数。若要删除users表中u_id为7的记录，则代码如下：`String sql = "delete from users where u_id=?";PreparedStatement pstmt = conn.prepareStatement(sql);pstmt.setInt(1,7);int rows= pstmt.executeUpdate ();`
5. 处理执行SQL语句的返回结果
    + 使用Statement对象的executeQuery()方法执行一条SELECT语句后，会返回一个ResultSet对象
    + ResultSet对象保存查询的结果集，调用ResultSet对象的相应方法就可以对结果集中的数据行进行处理
    + ResultSet对象的常用方法如下
        * boolean next()：ResultSet对象具有指向当前数据行的指针，指针最初指向第一行之前，使用next()方法可以将指针移动到下一行。如果没有下一行时，则返回false
        * getXxx(列名或列索引)：该方法可获取所在行指定列的值。其中，Xxx指的是列的数据类型。若使用列名作为参数，则getString(“name”)，表示获取当前行列名为“name”的列值。列索引值从1开始编号，如第2列对应的索引值为2
    + 若要获取users表中第一条记录的基本信息，代码如下：   
```java
String sql = "select * from users";
ResultSet rs=stmt. executeQuery(sql);
rs.next();
int u_id=rs.getInt(1);          
//或int u_id=rs.getInt("u_id");
String u_name=rs.getString(2);  
String u_sex=rs.getString("u_sex");
```

6. 关闭连接
    + 连接数据库过程中创建的Connection对象、Statement对象和ResultSet对象，都占用一定的JDBC资源
    + 当完成对数据库的访问之后，应及时关闭这些对象，以释放所占用的资源。这些对象都提供了close()方法，关闭对象的次序与创建对象的次序正相反，因此关闭对象的代码如下：rs.close();stmt.close();conn.close();
#### 3. JDBC数据库操作
###### 增加数据
- JDBC提供了两种实现增加数据的操作方法
    + 使用Statement对象提供的带参数的executeUpdate()方法
    + 通过PreparedStatement对象提供的无参数的executeUpdate()方法
###### 修改数据
- JDBC也提供了两种实现修改数据库中已有数据的方法，同实现增加操作的方法基本相同，只不过是使用UPDATE命令来实现更新操作
- 使用Statement对象实现修改users表中用户名为zhangping的用户，将其密码修改为654321，其关键代码如下  
```java
Statement stmt = conn.createStatement();
String sql = "update users set u_pwd='654321' where u_name='zhangping'";
int temp = stmt.executeUpdate(sql);
```
- 使用PreparedStatement对象实现修改users表中用户名为zhangping1的用户，将其密码修改为654321，其关键代码如下   
```java
String sql = "update users set u_pwd=? where u_name=?";
PreparedStatement pstmt  = conn.prepareStatement(sql);
pstmt.setString(1, "654321");
pstmt.setString(2, "zhangping1");
int temp =pstmt.executeUpdate();
```
###### 删除数据
使用Statement对象实现删除users表中用户名为zhangping的用户，其关键代码如下：
```java
Statement stmt = conn.createStatement();
String sql = "delete from users where u_name='zhangping'";
int temp = stmt.executeUpdate(sql);
```
使用PreparedStatement对象实现删除users表中用户名为zhangping1的用户，其关键代码如下：
```java
String sql = " delete from users where u_name=?";
PreparedStatement pstmt  = conn.prepareStatement(sql);
pstmt.setString(1, "zhangping1");
int temp =pstmt.executeUpdate();
```
###### 查询数据
- JDBC同样提供了两种实现数据查询的方法
    + 使用Statement对象提供的带参数的executeQuery()方法
    + 通过PreparedStatement对象提供的无参数的executeQuery()方法
- 使用SELECT命令实现对数据的查询操作，查询的结果集使用ResultSet对象保存
###### 批处理
- JDBC使用Statement对象和PreparedStatement对象的相应方法实现批处理，其实现步骤如下：
    1. 使用addBatch(sql)方法，将需要执行的SQL命令添加到批处理中。
    2. 使用executeBatch()方法，执行批处理命令。
    3. 使用clearBatch()方法，清空批处理队列。    
- 使用JDBC实现批处理有三种方法：
    + 批量执行静态的SQL
        * 使用Statement对象的addBatch()方法可以批量执行静态SQL
        * 优点是可以向数据库发送多条不同的SQL语句
        * 缺点是SQL语句没有预编译，执行效率较低，并且当向数据库发送多条语句相同，但仅参数不同的SQL语句时，需重复使用多条相同的SQL语句
    + 批量执行动态的SQL
        * 批量执行动态SQL，需要使用PreparedStatement对象的addBatch()方法来实现批处理
        * 优点是发送的是预编译后的SQL语句，执行效率高
        * 缺点是只能应用在SQL语句相同，但参数不同的批处理中
        * 因此此种形式的批处理经常用于在同一个表中批量更新表中的数据
    + 批量执行混合模式的SQL
        * 使用PreparedStatement对象的addBatch()方法还可以实现混合模式的批处理，既可以执行批量执行动态SQL，同时也可以批量执行静态SQL
#### 4. 数据源
- 数据库连接池的基本思想就是为数据库连接建立一个“缓冲池”，预先在“缓冲池”中放入一定数量的连接。当需要建立数据库连接时，只需从“缓冲池”中取出一个，使用完毕之后再放回去
- 数据源（Data Source）是目前Web开发中获取数据库连接的首选方法。这种方法是首先创建一个数据源对象，由数据源对象事先建立若干连接对象，通过连接池管理这些连接对象
- JNDI是一种将名称和对象绑定的技术，对象工厂负责创建对象，这些对象都和唯一的名称绑定，应用程序可以通过名称来获得某个对象的访问
- 在Tomcat服务器下配置MySQL数据库连接池的方法：
    （1）将MySQL数据库的JDBC驱动程序包复制到Tomcat安装路径下的lib文件夹中
    （2）配置数据源。配置Tomcat根目录下conf文件夹中的文件context.xml
    （3）在应用程序中使用数据源。配置数据源后，就可以使用javax.naming.Context接口的lookup()方法来查找JNDI数据源
#### 5. 分页查询例子
- 案例要求:使用JDBC执行SQL的SELECT命令实现图书基本信息的查询及分页显示
- 知识点补充:在数据查询、数据更新事务中，如果需要在结果集中前后移动或显示结果集指定的一条记录的时候，就要用到游动查询，这时应使用带参数的createStatement()方法创建语句对象，其语法格式如下：Statement stmt=conn.createStatement(int type，int concurrency);
    + type的取值决定滚动方式，其取值为
        * ResultSet.TYPE_FORWORD_ONLY：结果集的游标只能向下滚动
        * ResultSet.TYPE_SCROLL_INSENSITIVE：结果集的游标可以上下移动，当数据库变化时，当前结果集不变
        * ResultSet.TYPE_SCROLL_SENSITIVE：返回可滚动的结果集，当数据库变化时，当前结果集同步改变
    + concurrency的取值决定是否可以用结果集更新数据库，其取值为
        * ResultSet.CONCUR_READ_ONLY：不能用结果集更新数据库中的表
        * ResultSet.CONCUR_UPDATETABLE：能用结果集更新数据库中的表
- 创建Web项目
    1. 打开MyEclipse，新建Web Project项目，项目名称为Website。将JDBC驱动包放置在项目的WEB-INF/lib目录下，在项目中导入MySQL JDBC驱动包。
    2. 新建Example7_8.jsp，实现记录的分页显示功能。
    3. 打开浏览器，在地址栏中访问http://localhost:8080/Website/Example7_8.jsp

