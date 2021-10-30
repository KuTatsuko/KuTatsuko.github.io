# 基础加强

### Junit 单元测试

​	断言：通过断言操作判定程序输出值与期望值是否相等

​	使用方法：

```java
Assert.assertEquals(3, result);		//期望结果, 运算结果
```

​	@Before：修饰的方法会在测试方法之前被自动执行

​	@After：修饰的方法会在测试方法执行之后自动被执行



### 注解

​	作用：对元素进行说明

​		① 编写文档：通过代码里标识的注解生成文档

​		② 代码分析：对代码进行分析（反射）

​		③ 编译检查：让编译器实现基本的编辑检查（如 @Override）

​	

​	生成文档：

```java
/**
*	注解 javadoc 演示
*	
*	@author	Ku_Tatsuko
*	@version 1.0
*	@since 1.5
*/
public class AnnoDemo1 {
    /**
    *	计算两数之和
    *	@param a 整数
    *	@param b 整数
    *	@return 两数之和
    */
    public int add(int a, int b) {
        return a + b;
    }
    //生成方法：javadoc AnnoDemo1.java
}
```



##### JDK 中预定义的一些注解

​	@Override：检测该注解标注的方法是否继承自父类（接口）

​	@Deprecated：标注的内容表示已过时

​	@SuppressWarnings：压制警告，一般传递参数 all（ @SuppressWarnings("all") ）



##### 自定义注解

​	格式：

```java
元注解
public @interface 注解名称 {
	属性列表;
}
```

​	本质：就是接口，该接口默认继承 Annotation 接口

​	属性：接口中可以定义的成员方法

​		① 属性的返回值类型有以下取值：

​				* 基本数据类型

​				* string

​				* 枚举

​				* 注解

​				* 以上类型的数组

​		② 定义了属性，在使用时需要给属性赋值

​				1.如果定义属性时，使用 default 关键字赋予初始值，则使用时候可不进行赋值

​				2.如果只有一个属性需要赋值，且属性名称为 value，则 value 可以省略

​				3.数组赋值时，使用 { } 包裹，如果数组中只有一个值，可以省略 { }

​	反编译：javap xxx.java

​	示例：

```java
//定义
public @interface MyAnno {
    int value();
    Person per();
    MyAnno2 anno2();
    String[] strs();
}

//使用
@MyAnno(value=12, per=Person.P1, anno2=@MyAnno2, strs="bbb")
public class Worker { }
```

​	

​	元注解：用于描述注解的注解

​		@Target：描述注解能用作用的位置

​			取值：{ElementType.XXX}

​				TYPE：可以作用于类上

​				METHOD：可以作用于方法上

​				FIELD：可以作用于成员变量上

​		@Retention：描述注解被保留的阶段

​			一般使用 @Retention(RetentionPolicy.RUNTIME)：当被描述的注解，会保留到 class 字节码文件中，并被 JVM 读取到

​		@Documented：描述注解是否被抽取到 api 文档中

​		@Inherited：描述注解是否被子类继承



##### 在程序使用（解析）注解

​	1.获取注解定义的位置的对象（Class, Method, Field）

​	2.获取指定的注解（ getAnnotation(Class) ）

​	3.调用注解中的抽象方法获取配置的属性值

​	示例：

```java
//注解编写
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface Pro {
    String className();
    String methodName();
}

//测试类代码
//1 解析注解
//1.1 获取该类的字节码文件对象
Class<ReflectAnno> reflectAnnoClass = ReflectAnno.class;
//2 获取上边的注解对象，本质是在内存中生成了一个该注解接口的子类实现对象
Pro an = reflectAnnoClass.getAnnotation(Pro.class);
//3 调用注解对象中定义的抽象方法，获取返回值
String className = an.className();
String methodName = an.methodName();
Class<?> c = Class.forName(className);
Constructor<?> con = c.getConstructor();
Object obj = con.newInstance();
Method m = c.getMethod(methodName);
m.invoke(obj);
```



##### 简单测试框架

```java
Calculator c = new Calculator();
Class cls = c.getClass();
Method[] methods = cls.getMethods();
int number = 0;
BufferedWriter bw = new BufferedWriter(new FileWriter("bug.txt"));

for (Method method : methods) {
    if (method.isAnnotationPresent(Check.class)) {
        try {
            method.invoke(c);
        } catch (Exception e) {
            number++;
            bw.write(method.getName() + " 方法出现异常");
            bw.newLine();
            bw.write("异常名称：" + e.getCause().getClass().getSimpleName());
            bw.newLine();
            bw.write("异常原因：" + e.getCause().getMessage());
            bw.newLine();
            bw.write("-----------------------");
            bw.newLine();
        }
    }
}

bw.write("本次测试一共出现 " + number + " 次异常");

bw.flush();
bw.close();
```

​	注：以后大多数时候会使用注解，而不是自定义注解



# MySQL

### 小记

​	卸载后删除：原 MySQL 安装目录下 my.ini 文件内的 datadir 路径

​	服务启动：

​		① cmd  --->  services.msc

​		② net start mysql   /   net stop mysql

​	登录：

​		① mysql -uroot -p密码

​		② mysql -hip -uroot -p密码

​	timestamp类型：包含年月日时分秒，如果不赋予值或赋予null，则默认为当前系统时间

​	IFNULL()：如果有 null 参与运算，则计算结果都为 null，可以用 IFNULL(列名, 期望结果) 替换

​	分页公式：开始的索引 = (当前页码 - 1) * 每页显示条数

​	唯一约束：UNIQUE

​	自动增长：AUTO_INCREMENT

​	联合主键：PRIMARY KEY (列1, 列2)

​	SQL 分类：

​		① DDL：操作数据库和表

​		② DML：增删改表中数据

​		③ DQL：查询表中的数据

​		④ DCL：用户管理与授权



### 语句

##### 	注释

​	① 单行注释：-- 注释 或 # 注释

​	② 多行注释：/*   */



##### 	数据库操作

​	创建：

​		① CREATE DATABASE 数据库名;

​		② CREATE DATABASE IF NOT EXISTS 数据库名;

​		③ CREATE DATABASE 数据库名 CHARACTER SET 编码;	(GBK、UTF8)

​	查询：

​		① 列出数据库：SHOW DATBASES;

​		② 查询数据库创建语句：SHOW CREATE DATABASE 数据库名;

​	修改：

​		① ALTER DATABASE 数据库名 CHARACTER SET 编码;

​	删除：

​		① DROP DATABASE 数据库名;

​		② DROP DATABASE IF EXISTS 数据库名;

​	使用：

​		① 查询当前正在使用的数据库：SELECT DATABASE();

​		② 使用数据库：USE 数据库名;



##### 	表操作

​	创建：

​		① CREATE TABLE 表名 ( ... );

​		② CREATE TABLE 表名 LIKE 被复制的表;

​		③ 外键：CONSTRAINT 外键名 FOREIGN KEY (列名) REFERENCE 主表名(主表列名);

​		④ 外键级联更新：上述代码末尾添加 ON UPDATE CASCADE;

​		⑤ 外键级联删除：与级联更新可同时存在，即在上诉代码末尾再添加 ON DELETE CASCADE;

​	查询：

​		① 查询数据中所有表：SHOW TABLES;

​		② 查询表结构：DESC 表名;

​	修改：

​		① 修改表名：ALTER TABLE 表名 RENAME TO 新表名;

​		② 修改表的字符集：ALTER TABLE 表名 CHARACTER SET 编码;

​		③ 添加列：ALTER TABLE 表名 ADD 列名 数据类型 [约束];

​		④ 修改列名称与类型：ALTER TABLE 表名 CHANGE 列名 新列名 新数据类型 [约束];

​		⑤ 修改列类型：ALTER TABLE 表名 MODIFT 列名 新数据类型 [约束];

​		⑥ 删除列：ALTERT TABLE 表名 DROP 列名;

​		⑦ 删除主键：ALTER TABLE 表名 DROP PRIMARY KEY;

​		⑧ 删除外键：ALTER TABLE 表名 DROP FOREIGN KEY 外键名;

​	删除：

​		① DROP TABLE 表名;

​		② DROP TABLE IF EXISTS 表名;



##### 	数据操作

​	添加：

​		① INSERT INTO 表名(列1,列2,...,列n) VALUES(值1,值2,...,值n);

​		② INSERT INTO 表名 VALUES(值1,值2,...,值n);

​	修改：

​		① UPDATE 表名 SET 列1 = 值1, 列2 = 值2, ... [WHERE 条件]

​	删除：

​		① DELETE FROM 表名 [WHERE 条件];

​		② TRUNCATE TABLE 表名;	--->	先删除表，再创建一个一模一样的表

​	查询：

​		① SELECT 列名

​			 FROM 表名

​			 WHERE 条件

​			 GROUP BY 分组字段

​			 HAVING 分组后条件

​			 ORDER BY 排序

​			 LIMIT 分页限定

​	具体例子：

​		① SELECT * FROM student ORDER BY math ASC, English DESC;

​		② SELECT sex, AVG(math), COUNT(id) 人数 FROM student

​			 WHERE  math > 70 GROUP BY sex HAVING 人数 > 2;

​		

##### 数据库关系设计

​	一对多：在多的一方建立外键，只想一的一方的主键

​	多对多：创建第三张表，至少包含两个字段，分别作为第三张表的外键，指向两张表的主键

​		

##### 范式

​	第一范式（1NF）：每一列都是不可分割的原子数据项

​	第二范式（2NF）：在1NF基础上，非码属性必须完全依赖于码（在1NF基础上消除非主属性对主码的部分函数依赖）

​	第三范式（3NF）：在2NF基础上，任何非主属性不依赖于其他非主属性（在2NF基础上消除传递依赖）

​	概念：

​		① 函数依赖：A --> B，如果通过A属性(属性组)的值，可以确定唯一B属性的值。则称B依赖A

​				例：学号 --> 姓名

​		② 完全依赖：A --> B，如果A是一个属性组，则B属性值的确定需要依赖于A属性组中所有的属性值

​				例：(学号, 课程名称) --> 分数

​		③ 部分函数依赖：A --> B，如果A是一个属性组，则B属性值的确定只需要依赖于A属性值中某一项

​				例：(学号, 课程名称) --> 姓名

​		④ 传递函数依赖：A --> B / B --> C，如果通过A属性(属性组)的值，可以确定唯一B属性的值，在通过B属性(属性组)的值可以确定唯一C的值

​				例：学号 --> 系名，系名 --> 系主任

​		⑤ 码：在一张表中，一个属性或属性组，被其他所有属性完全依赖，则成称这个属性(属性组)为码

​				例：码为 (学号, 课程名称) ，主属性为 码属性组中所有属性，非主属性为 除开码属性组的属性



##### 数据库备份与还原

​	备份：mysqldump -u用户名 -p密码 数据库名 > 保存路径

​	还原：登录 --> 创建数据库 --> 使用数据库 --> 执行：source 文件路径

​		

##### 多表查询

​	隐式内连接：SELECT * FROM emp, dept WHERE emp.dept_id = dept.id;

​	显式内连接：SELECT * FROM emp [INNER] JOIN dept ON emp.dept_id = dept.id;

​	左外连接：

​		语法：SELECT 字段 FROM 表1 LEFT [OUTER] JOIN 表2 ON 条件;

​		原理：如果员工没有部门id，使用内连接会筛选不出，则可以使用外连接，外连接查询的是左表所有数据以及其交集的部分

​	右外连接：

​		语法：SELECT 字段 FROM 表1 RIGHT [OUTER] JOIN 表2 ON 条件;

​		原理：查询的是右表所有数据以及其交集的部分

​	子查询：

​		① 单行单列：子查询可以作为条件，使用运算符判断，运算符为 >   >=   <   <=   =

​				例：SELECT * FROM emp WHERE emp.salary < (SELECT AVG(salary) FROM emp);

​		② 多行单列：子查询可以作为条件，使用运算符 IN 判断

​				例：SELECT * FROM emp WGERE dept_id IN 

​						(SELECT id FROM dept WHERE name = '财务部' OR name = '市场部');

​		③ 多行多列：子查询可以作为虚拟表参与查询（可使用内连接替代）

​				例：SELECT * FROM dept t1, (SELECT * FROM emp WHERE join_date > '2011-11-11') t2

​						WHERE t1.id = t2.dept_id;



### 事务

​	概念：如果一个包含多个步骤的业务操作被事务管理，这些操作要么同时成功要么同时失败

​	操作：

​		① 开启：START TRANSACTION;

​		② 回滚：ROLLBACK;

​		③ 提交：COMMIT;

​	事务提交：

​		查看默认提交方式：SELECT @@aotocommit;	---> 1代表自动提交，0代表手动提交

​		修改默认提交方式：SET @@aotocommit = 0;

​		注：MySQL 默认自动提交，Oracle 默认手动提交

​	事务四大特性：

​		① 原子性：事务是不可分割的最小操作单位，要么同时成功，要么同时失败

​		② 持久性：当事务提交或回滚后，数据库会持久化的保存数据

​		③ 隔离性：多个事务之间相互独立

​		④ 一致性：实务操作前后数据总量不变

​	事务隔离级别（了解）：

​		概念：多个事务之间是隔离的。但如果多个事务操作同一批数据，则会引发一些问题

​		存在问题：

​			① 脏读：一个事务读取到另一个事务没有提交的数据

​			② 不可重复读：同一事务中两次读取到的数据不一样

​			③ 幻读：一个事务操作数据表中所有记录，另一个事务添加了一条数据，则第一个事务查询不到自己的修改

​		隔离级别：

​			① read uncommitted：读未提交						   产生问题：脏读、不可重复读、幻读

​			② read committed：读已提交（oracle默认）	 产生问题：不可重复度、幻读

​			③ repeatable read：可重复度（MySQL默认）	产生问题：幻读

​			④ serializable：串行化											可解决所有问题

​		注意：隔离级别从小到大安全性越来越高，但效率越来越低

​		查询隔离级别：SELECT @@tx_isolation;

​		设置隔离级别：SET GLOBAL TRANSACTION ISOLATION LEVEL 级别字符串;

​		

### 管理用户

##### 用户管理

​	查询：SELECT * FROM USER;

​	添加：CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';

​	删除：DROP USER '用户名'@'主机名';

​	修改：

​		① UPDATE USER SET PASSWORD = PASSWORD('新密码') WHERE USER = '用户名';

​		② SET PASSWORD FOR '用户名'@'主机名' = PASSWORD('新密码');

​	重置 MySQL 的 root 用户密码：

​		① cmd 执行 net stop mysql

​		② 使用无验证方式启动 MySQL 服务：mysqld --skip-grant-tables

​		③ 开启新 cmd 窗口，直接输入 mysql，回车登录

​		④ 修改用户密码

​		⑤ 关闭 cmd 窗口，任务管理器手动结束 mysqld.exe

​		⑥ cmd 执行 net start mysql

​	注：所有操作需要先 USE mysql，主机名为 '%' 表示可在任意主机登录



##### 权限管理

​	查询权限：SHOW GRANTS FOR '用户名'@'主机名';

​	授予权限：GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名';

​	撤销权限：REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名';

​	授予所有权限：`RANT ALL ON *.* TO '用户名'@'主机名';`



# JDBC

### JDBC基础

##### 小记

​	概念：Java Data Base Connectivity Java 数据库连接，Java 语言操作数据库

​	JDBC本质：其实是官方(sun公司)定义的一套操作所有关系型数据库的规则，即接口。各个数据库厂商去实现这套接口，提供数据库驱动jar包。我们可以使用这套接口(JDBC)编程，真正执行的代码是驱动jar包中的实现类

​	jar 包导入：复制 jar 包到项目新建的 libs 文件夹下，右键 --> Add As Library

​	快速入门：

```java
//1.注册驱动
Class.forName("com.mysql.cj.jdbc.Driver");
//2.获取数据库连接对象
Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/hm_db3", "root", "123456");
//3.定义 sql 语句
String sql = "update account set balance = 500 where id = 1";
//4.获取执行 sql 的对象 Statement
Statement stmt = conn.createStatement();
//5.执行 sql
int count = stmt.executeUpdate(sql);
//6.处理结果
System.out.println(count);
//7.释放资源
stmt.close();
conn.close();
```

​		

##### 各对象详解

1.DriverManager：驱动管理对象

​	① 注册驱动：告诉程序该使用哪个数据库驱动 jar

​			源码：static void registerDriver(Driver driver)   -->   Driver 类中有静态代码块加载注册

​			写代码时使用：Class.forName("com.mysql.cj.jdbc.Driver");

​			注意：MySQL 5 之后的驱动 jar 包可以省略注册驱动

​	② 获取数据库连接：

​			语法：jdbc:mysql://域名:端口号/数据库名

​			注意：如果是本地连接，且端口号为 3306，则 url 可简写为 jdbc:mysql:///数据库名

2.Connection：数据库连接对象

​	① 获取执行 sql 的对象（Statement 和 PreparedStatement）

​	② 管理事务：

​			开启事务：setAutoCommit(boolean autoCommit)   -->   设置为 false 即为开启事务

​			提交事务：commit()

​			回滚事务：rollback()

3.Statement：执行静态 SQL 语句的对象

​	① execute(String sql)：可执行任意 SQL 语句

​	② executeUpdate(String sql)：执行DML(insert、update、delete)，DDL(create、alter、drop)

​	③ executeQuery(String sql)：执行 DQL 语句 (select)

4.ResultSet：结果集对象，封装查询结果

​	① boolean next()：游标向下移动一行，判断当前行是否是最后一行

​	② getXxx(参数)：获取数据，参数可为 getDouble("balance") 或 getString(1)   -->  下标从 1 开始 

5.PreparedStatement：执行预编译 SQL 语句的对象

​	① SQL 语句参数使用 ? 作为占位符，如 select * from user where name = ?

​	② 创建对象：Connection.prepareStatement(String sql);

​	③ 给 ? 赋值：setXxx(参数1, 参数2)   -->   参数1：? 的位置编号，从1开始，参数2：? 的值

​	注意：后期都会使用此对象完成增删改查所有操作



### JDBCUtils

```java
package cn.itcast.util;

import java.io.FileReader;
import java.io.IOException;
import java.net.URL;
import java.sql.*;
import java.util.Properties;

public class JDBCUtils {
    private static String driver;
    private static String url;
    private static String user;
    private static String password;

    static {
        try {
            Properties pro = new Properties();
            ClassLoader classLoader = JDBCUtils.class.getClassLoader();
            URL res = classLoader.getResource("jdbc.properties");
            String path = res.getPath();
            pro.load(new FileReader(path));
            driver = pro.getProperty("driver");
            url = pro.getProperty("url");
            user = pro.getProperty("user");
            password = pro.getProperty("password");
            Class.forName(driver);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(url, user, password);
    }

    public static void close(Statement stmt, Connection conn) {
        if (stmt != null) {
            try {
                stmt.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if (conn != null) {
            try {
                conn.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }

    public static void close(Statement stmt, Connection conn, ResultSet rs) {
        if (stmt != null) {
            try {
                stmt.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if (conn != null) {
            try {
                conn.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if (rs != null) {
            try {
                rs.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }

}
```



### 数据库连接池

标准接口：javax.sql.DataSource

方法：

​	① 获取连接：getConnection()

​	② 归还连接：close()   -->   如果连接对象是从连接池中获取的，调用 close() 方法则变为归还连接

常用连接池技术：

​	① C3P0：较老但常用

​	② Druid：阿里巴巴提供，性能优越



##### C3P0 连接池技术

​	① 导入包：c3p0-0.9.5.2.jar、mchange-commons-java-0.2.12.jar

​	② 定义配置文件：将 c3p0-config.xml 或 c3p0.properties 放入 src 目录下即可

​	③ 创建核心数据库连接池对象：ComboPooledDataSource()

​			注：空参使用默认配置，可传参使用指定配置，如：ComboPooledDataSource("otherc3p0")

​	④ 获取连接：getConnection()

​	例：

```java
DataSource ds = new ComboPooledDataSource();
Connection conn = ds.getConnection();
```



##### Druid连接池技术

​	① 导入包：druid-1.0.9.jar

​	② 定义配置文件：properties 形式，任意名称与位置

​	③ 加载配置文件：可使用 Xxx.class.getClassLoader().getResourceAsStream("...");

​	④ 获取数据库连接池对象：通过工厂类 DruidDataSourceFactory.createDataSource(pro) 获取

​			注：传入 Properties 对象

​	⑤ 获取连接：getConnection()

​	例：

```java
Properties pro = new Properties();
InputStream is = DruidDemo.class.getClassLoader().getResourceAsStream("druid.properties");
pro.load(is);
DataSource ds = DruidDataSourceFactory.createDataSource(pro);
Connection conn = ds.getConnection();
System.out.println(conn);
```



##### Druid 版 JDBCUtils

```java
package cn.itcast.utils;

import com.alibaba.druid.pool.DruidDataSourceFactory;

import javax.sql.DataSource;
import java.io.IOException;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Properties;

public class JDBCUtils {
    private static DataSource ds;

    static {
        try {
            Properties pro = new Properties();
            pro.load(JDBCUtils.class.getClassLoader().getResourceAsStream("durid.properties"));
            ds = DruidDataSourceFactory.createDataSource(pro);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    //获取连接
    public static Connection getConnection() throws SQLException {
        return ds.getConnection();
    }

    //获取连接池
    public static DataSource getDataSource() {
        return ds;
    }

    public static void close (Statement stmt, Connection conn) {
        close(null, stmt, conn);
    }

    public static void close (ResultSet rs, Statement stmt, Connection conn) {
        if (null != rs) {
            try {
                rs.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if (null != stmt) {
            try {
                stmt.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if (null != conn) {
            try {
                conn.close();   //归还连接
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }

}
```



### Spring JDBC

功能：简化 JDBC 中绑定实体类、设置参数、处理结果等操作

1.创建 JdbcTemplate 对象，依赖于 DataSource: JdbcTemplate t = new JdbcTemplate(ds);

2.CRUD 操作：

​	① update()：执行 DML 语句（增删改）

​	② queryForMap()：查询结果并封装为 Map 集合（只能查询封装单条记录，列名为 key，值为 value）

​	③ queryForList()：查询结果并封装为 List 集合（每条记录封装为 Map，再装载到 List）

​	④ query()：查询结果并封装为 JavaBean

​	⑤ queryForObject()：查询结果并封装为对象

示例：

```java
//执行 DML 语句
JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());
String sql = "update account set balance = 5000 where id = ?";
int count = template.update(sql, 4);

//查询结果并封装为 Map 集合
String sql = "select * from emp where id = 1001";
Map<String, Object> map = template.queryForMap(sql);

//查询结果并封装为 List 集合
String sql = "select * from emp";
List<Map<String, Object>> list = template.queryForList(sql);

//查询结果并封装为 JavaBean
String sql = "select * from emp";
List<Emp> list = template.query(sql, new BeanPropertyRowMapper<Emp>(Emp.class));
for (Emp emp : list) {
	System.out.println(emp);
}

//查询结果并封装为对象
String sql = "select count(*) from emp";
Long aLong = template.queryForObject(sql, Long.class);
```



# HTML 选择器

##### 基础选择器

​	① id选择器：#xxx { }

​	② 元素选择器：标签名称 { }

​	③ 类选择器：.xxx { }



##### 拓展选择器

​	① 选择所有元素：* { }

​	② 并集选择器：选择器1, 选择器2 { }

​	③ 子选择器：选择器1 选择器2 { }

​	④ 父选择器：选择器1 > 选择器2 { }

​	⑤ 属性选择器：元素名称[属性名="属性值"] { }   -->   input[type="text"] { }

​	⑥ 伪类选择器：元素:状态 {}   -->   a:hover { }



# JavaScript

### 基本语法

1.与 html 结合方式

​	① 内部 JS：`<script> alert("hello world !") </script>`

​	② 外部 JS：`<script src="js/a.js" />`

2.注释

​	① 单行注释：//

​	② 多行注释：/* */

3.数据类型

​	Ⅰ：原始数据类型

​		① number：整数 / 小数 / NaN（Not a Number 不是数字的数字类型）

​		② undefined：如果变量没有给初始化值，则会被默认赋值为 undefined

​		③ boolean

​		④ null

​		⑤ string

​	Ⅱ：引用数据类型：对象

4.变量：var 变量名 = 初始值;   -->   可用 typeof(变量名) 查看数据类型

5.运算符补充：===   -->   全等于，先比较类型，如果类型不同则直接返回 false

6.流程控制语句：与 Java 相同



### 基本对象

##### Function

​	1.创建：

​		① var 方法名  = new Function(形参列表, 方法体);

​		② function 方法名(形参列表) { 方法体 }

​		③ var 方法名 = function(形参列表) { 方法体 }

​	2.方法：

​	3.属性：

​		① length：形参个数

​	4.特点：

​		① 方法定义时形参类型可省略，返回值类型也省略

​		② 方法是一个对象，如果定义名称相同的方法则会被覆盖

​		③ 在 JS 中，方法的调用只与方法名有关，与参数列表无关

​		④ 在方法声明中有一个隐藏内置对象（数组）arguments，封装所有实际参数

​	5.调用：方法名(实参列表)

​	示例：

```javascript
function add() {
    var sum = 0;
    for (var i=0; i<arguments.length; i++) {
        sum += arguments[i];
    }
    return sum;
}
alert(add(1, 2, 3, 4));
```



##### Array

​	1.创建：

​		① var arr = new Array(元素列表);

​		② var arr = new Array(默认长度);

​		③ var arr = [元素列表];

​	2.常用方法：

​		① join(参数)：将数组中的元素按照指定的分隔符拼接为字符串（留空默认为逗号）

​		② push()：向数组末尾添加一个或多个元素，并返回新的长度

​	3.属性：length   -->   数组的长度

​	4.特点：

​		① 数组元素的类型可变

​		② 数组长度可变



##### Date

​	1.创建：var date = new Date();

​	2.常用方法：

​		① toLocaleString()：返回当前对应时间的本地字符串格式

​		② getTime()：返回当前日期对象描述的时间到1970年1月1日零点的毫秒值差



##### Math

​	1.创建：无需创建直接使用

​	2.常用方法：

​		① random()：返回 0 ~ 1 之间的随机数，含 0 不含 1

​		② ceil(x)：上取整

​		③ floor(x)：下取整

​		④ round(x)：四舍五入为最接近的整数

​	3.常用属性：Math.PI

​	示例：

```javascript
var number = Math.round((Math.random() * 100)) + 1;
document.write(number);
```



##### RegExp 正则表达式

​	1.正则表达式：

​		① 单个字符：[ ]

​			如：[a] [ab] [a-zA-Z0-9_]

​			特殊符号代表特殊含义的单个字符：

​				\d：单个数字字符  [0-9]

​				\w：单个单词字符 [a-zA-Z0-9_]

​		② 量词符号：

​			?：出现 0 次或者 1 次

​			*：出现 0 次或多次

​			+：出现 1 次或多次

​			{m, n}：m <= 数量 <= n，缺省表示可为 {, n} 最大 n 次，{m, } 最少 m 次

​		③ 开始结束符号：

​			^：开始

​			$：结束

​	2.正则对象：

​		① 创建：

​			Ⅰ: var reg = new RegExp("正则表达式");

​			Ⅱ: var reg = /正则表达式/;

​		② 方法：test(参数)   -->   验证指定的字符串是否符合正则定义的规范

​	例：var reg = /^\w{6, 12}$/; 表示为以字符开头和结尾的长度为 6 ~ 12 位的字符串



##### Global

​	1.特点：全局对象，Global 封装的方法不需要对象可以直接使用，方法名();

​	2.常用方法：

​		① encodeURI()：url 编码

​			 decodeURI()：url 解码

​		② encodeURIComponent()：url 编码，编码后字符串更长

​		 	decodeURIComponent()：url 解码

​		③ parseInt()：逐一判断每个字符是否为数字，直到不是数字为止，将前边数字部分转为 number

​		④ isNaN()：判断值是否为 NaN

​		⑤ eval()：将 JavaScript 字符串作为脚本代码执行

​	例：

```javascript
var str = "StrLaLaLa星尘";
var encode = encodeURI(str);
var s = decodeURI(encode);

var str = "a234abc";
var number = parseInt(str);

var  a = NaN;
document.write(isNaN(a));

var jscode = "alert(123)";
eval(jscode);
```



### DOM

##### 小记

​	概念：Document Object Model 文档对象模型

​	功能：控制 html 文档的内容

​	获取页面标签（元素）的对象：Element

​		document.getElementById("id值")：通过元素的 id 获取元素对象

​	修改属性值：如 img.src = "xxx";

​	修改标签体内容：innerHTML

​	绑定事件：onclick，可直接在标签内添加，或 js 中 mybtn.oclick = fun2，推荐第二种

​	W3C DOM 标准：

​		Ⅰ：核心 DOM - 针对如何结构化文档的标注模型

​			① Document：文档对象

​			② Element：元素对象

​			③ Attribute：属性对象

​			④ Text：文本对象

​			⑤ Comment：注释对象

​			⑥ Node：节点对象，其他 5 个的父对象

​		Ⅱ：XML DOM - 针对 XML 文档的标准模型

​		Ⅲ：HTML DOM - 针对 HTML 文档的标准模型

​	a 标签去跳转功能（作按钮）：href="javascript:void(0);"



##### Document

常用方法：

​	Ⅰ.获取 Element 对象：

​		① getElementById()

​		② getElementByTagName()：根据元素名称获取元素对象，返回数组

​		③ getElementByClassName()：返回数组

​		④ getElementByName()：返回数组

​	Ⅱ.创建其他 DOM 对象：

​		① createAttribute()

​		② createComment()

​		③ createElement()

​		④ createTextNode()



##### Element

常用方法：

​	① removeAttribute()：删除属性

​	② setAttribute()：设置属性



##### Node

常用方法和属性：

​	① appendChild()：向节点的子节点列表末尾添加新的子节点

​	② removeChild()：删除（并返回）当前节点的指定子节点

​	③ replaceChild()：用新阶段替换一个子节点

​	④ parent Node 属性：返回节点的父节点

示例1：

```javascript
var div1 = document.getElementById("div1");
var div2 = document.createElement("div");
div2.setAttribute("id", "div2");
div1.appendChild(div2);
div1.removceChile(div2);
```

示例2：

```javascript
<script>
  document.getElementById("btn_add").onclick = function () {
    var id = document.getElementById("id").value;
    var name = document.getElementById("name").value;

    var td_id = document.createElement("td");
    td_id.appendChild(document.createTextNode(id));
    var td_name = document.createElement("td");
    td_name.appendChild(document.createTextNode(name));

    var td_a = document.createElement("td");
    var ele_a = document.createElement("a");
    ele_a.setAttribute("href", "javascript:void(0);");
    ele_a.setAttribute("onclick", "delTr(this);");
    ele_a.appendChild(document.createTextNode("删除"));
    td_a.appendChild(ele_a);

    var tr = document.createElement("tr");
    tr.appendChild(td_id);
    tr.appendChild(td_name);
    tr.appendChild(td_a);

    document.getElementsByTagName("table")[0].appendChild(tr);
  }

  function delTr(obj) {
    var table = obj.parentNode.parentNode.parentNode;
    var tr = obj.parentNode.parentNode;
    table.removeChild(tr);
  }
</script>
```



##### HTML DOM

​	1.标签体的设置和获取：innerHTML

​		示例：

```javascript
table.innerHTML += "<tr>\n" +
        "    <td>" + id + "</td>\n" +
        "    <td>" + name + "</td>\n" +
        "    <td>" + gender + "</td>\n" +
        "    <td><a href=\"javascript:void(0);\" onclick=\"delTr(this);\">删除</a></td>\n" +
        "  </tr>";
```

​	2.控制元素样式

​		① 使用元素的 style 属性设置：div1.style.border = "1px solid red";

​		② 提前定义好类选择器的样式，通过 className 属性设置：div2.className = "d1";

​		示例：

```javascript
div1.style.width = "200px";
div1.style.fontSize = "20px";	//font-size --> fontSize
div2.className = "d1"
```



### BOM

​	概念：Browser Object Model 浏览器对象模型，将浏览器各个组成部分封装成对象

​	组成：Window 窗口、Navigator 浏览器、Screen 屏幕、History 历史、Location 地址栏



##### Window

​	1.常用方法

​		Ⅰ.与弹出框有关：

​			① alert()：警告框	

​			② confirm()：确认和取消按钮对话框，点确定返回 true，取消则返回 false

​			③ prompt()：显示可提示用户输入的对话框，返回值为用户输入值

​		Ⅱ.与浏览器窗口有关：

​			① open()：打开一个新浏览器窗口，返回新的 window 对象

​			② close()：关闭浏览器窗口，谁调用关闭谁，如 open() 返回 nw 则 nw.close(); 

​		Ⅲ.与定时器有关：

​			① setTimeout()：指定毫秒数后调用函数，参数为 (js代码或方法对象, 毫秒值)，返回唯一标识

​			② clearTimeout()：取消定时器，参数为唯一标识

​			③ setInterval()：周期定时器，循环

​			④ clearInterval()：取消周期定时器

​	2.属性

​		① 获取其他 BOM 对象：navigator、screen、history、location

​		② 获取 DOM 对象：document

​		例：var h1 = window.history;   var h2 = history;

​	3.特点

​		① Window 对象不需要创建即可使用，如 window.方法名();

​		② window 引用可以省略，如 方法名();

​	示例：

```javascript
<script>
  var number = 1;
  function fun() {
    number++;
    if (number > 3) {
      number = 1;
    }
    document.getElementById("myImg");
    myImg.src = "img/banner_" + number + ".jpg";
  }
  setInterval(fun, 3000);
</script>
```



##### Location

常用方法与属性：

​	① location.reload()：刷新页面

​	② location.href：获取当前链接，或访问指定链接

示例：

```javascript
<script>
  var time = document.getElementById("time");
  var second = 5;
  function showTime() {
    second--;
    if (second <= 0) {
      location.href = "https://baidu.com";
    }
    time.innerHTML = second;
  }
  setInterval(showTime, 1000);
</script>
```



##### History

常用方法与属性：

​	① back()：加载 history 列表中前一个 URL

​	② forward()：加载 history 列表后一个 URL

​	③ go(参数)：加载 history 列表中某个具体的 URL，参数中

​		正数：前进几个历史记录

​		负数：后退几个历史记录

​	④ history.length：当前窗口历史列表中的 URL 数量



### 事件监听机制

1.点击事件：

​	① onclick：单击

​	② ondblclick：双击

2.焦点事件：

​	① onblur：失去焦点

​	② onfocus：获得焦点

3.加载事件：

​	① onload：完成加载

4.鼠标事件：

​	① onmousedown：鼠标按下

​		注：定义方法时可定义一个形参，接收 event 对象，event.button 可获取鼠标按键值

​	② onmouseup：鼠标松开

​	③ onmousemove：鼠标移动

​	④ onmouseover：鼠标移到某元素上

​	⑤ onmouseout：鼠标从某元素移开

5.键盘事件：

​	① onkeydown：按键按下

​	② onkeyup：按键松开

​	③ onkeypress：按键按下并松开

6.选择和改变：

​	① onchange：域的内容被改变

​	② onselect：文本被选中

7.表单事件：

​	① onsubmit：提交事件，方法 return false 可阻止表单提交

​	② onreset：重置按钮被点击

​	注：form 表单上事件有两种写法：onclick="return check();" 或 onsubmit="check();"



##### 案例1

```javascript
<script>
    window.onload = function () {
        var cbs = document.getElementsByName("cb");
        document.getElementById("selectAll").onclick = function () {
            for (var i=0; i<cbs.length; i++) {
                cbs[i].checked = true;
            }
        }
        document.getElementById("unSelectAll").onclick = function () {
            for (var i=0; i<cbs.length; i++) {
                cbs[i].checked = false;
            }
        }
        document.getElementById("selectRev").onclick = function () {
            for (var i=0; i<cbs.length; i++) {
                cbs[i].checked = !cbs[i].checked;
            }
        }
        document.getElementById("firstCb").onclick = function () {
            for (var i=0; i<cbs.length; i++) {
                cbs[i].checked = this.checked;
            }
        }
        var trs = document.getElementsByTagName("tr");
        for (var i=0; i<trs.length; i++) {
            trs[i].onmouseover = function () {
                this.className = "over";
            }
            trs[i].onmouseout = function () {
                this.className = "out";
            }
        }
    }
</script>
```



##### 案例2

```javascript
<script>
    window.onload = function () {
        document.getElementById("form").onsubmit = function () {
            return checkUsername() && checkPassword();
        }
        document.getElementById("username").onblur = checkUsername;
        document.getElementById("password").onblur = checkPassword;
    }

    function checkUsername() {
        var username = document.getElementById("username").value;
        var reg_username = /^\w{6,12}$/;
        var flag = reg_username.test(username);
        var s_username = document.getElementById("s_username");
        if (flag) {
            s_username.innerHTML = "<img src='img/gou.png' width='35px'/>"
        } else {
            s_username.innerHTML = "用户名格式有误";
        }
        return flag;
    }

    function checkPassword() {
        var password = document.getElementById("password").value;
        var reg_password = /^\w{6,12}$/;
        var flag = reg_password.test(password);
        var s_password = document.getElementById("s_password");
        if (flag) {
            s_password.innerHTML = "<img src='img/gou.png' width='35px'/>"
        } else {
            s_password.innerHTML = "密码格式有误";
        }
        return flag;
    }
</script>
```



# Bootstrap

##### 具体查看在线文档

##### 简单引入

```html
<!doctype html>
<html lang="zh-CN">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <!-- 上述3个meta标签*必须*放在最前面，任何其他内容都*必须*跟随其后！ -->
    <title>Bootstrap 101 Template</title>

    <!-- Bootstrap -->
    <link rel="stylesheet" href="css/bootstrap.min.css">
  </head>
  <body>
    <h1>你好，世界！</h1>

    <!-- jQuery (Bootstrap 的所有 JavaScript 插件都依赖 jQuery，所以必须放在前边) -->
    <script src="js/jquery.min.js"></script>
    <!-- 加载 Bootstrap 的所有 JavaScript 插件。你也可以根据需要只加载单个插件。 -->
    <script src="js/bootstrap.min.js"></script>
  </body>
</html>
```



# XML

### 基本格式

1.声明格式：<?xml 属性列表 ?>

2.声明属性列表：

​	① version：版本号，必填

​	② encoding：编码方式，默认为 ISO-8859-1

​	③ standalone：是否独立，取值为 yes 或 no

3.指令：结合 css 为 <?xml-stylesheet type="text/css" href=a.css ?> （了解）

4.标签：名称不能以 xml 开头，无论大小写，不能包含空格或数字、标点符号开头

5.属性：id 属性值唯一

6.文本：CDATA 区中数据会原样展示，如  <![CDATA[ 数据 ]]>



### 约束

##### DTD：简单约束

​	1.内部 DTD：<!DOCTYPE students [ 约束内容 ]>

​	2.外部 DTD：

​			① 本地 <!DOCTYPE 根标签名 SYSTEM "DTD文件位置">

​			② 网络 <!DOCTYPE 根标签名 PUBLIC "DTD文件名" "DTD网络位置URL">

​	3.格式（了解）

```xml-dtd
<!ELEMENT students (student*) > <!-- 标签名为 students，里面能放无限个 student 子标签 -->
<!-- 注：(student*) 中 * 换为 + 则表示必须出现一次或多次 -->
<!ELEMENT student (name,age,sex)> <!-- student 内能放定义的三个标签，只能出现一次 -->
<!ELEMENT name (#PCDATA)> <!-- name 能能放字符串（#PCDATA） -->
<!ELEMENT age (#PCDATA)>
<!ELEMENT sex (#PCDATA)>
<!ATTLIST student number ID #REQUIRED> <!-- student 标签有属性 id，类型为 number -->
<!-- 注:#REQUIRED 表示必须出现且唯一 -->
```



##### Schema：复杂约束

​	1.填写 xml 文档的根元素

​	2.引入 xsi 前缀：xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"

​	3.引入 xsd 文件命名空间：xsi:schemaLocation="http://www.itcast.cn/xml  student.xsd"

​	4.为每一个 xsd 约束声明一个前缀作为标识：xmlns="http://www.itcast.cn/xml"

​		注：xmlns:a="" 则表示前缀为 a: ，xmlns="" 则为默认前缀

​	5.例：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
 <students xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 		   xmlns="http://www.itcast.cn/xml" 
 		   xsi:schemaLocation="http://www.itcast.cn/xml  student.xsd"
		   >
 	<student number="heima_0001">
 		<name>tom</name>
 		<age>18</age>
 		<sex>male</sex>
 	</student>
 </students>
```



### Jsoup 解析

##### 解析方式与快速入门

​	1.DOM：将标记语言文档一次性加载进内存，在内存中形成 DOM 树

​			优点：操作方便，能够进行 CRUD 所有操作

​			缺点：占内存

​	2.SAX：逐行读取，基于事件驱动

​			优点：不占内存

​			缺点：只能读取，不能增删改

​	注：Jsoup 为 DOM 解析

​	示例：

```java
String path = JsoupDemo1.class.getClassLoader().getResource("student.xml").getPath();
Document document = Jsoup.parse(new File(path), "utf-8");
Elements elements = document.getElementsByTag("name");
System.out.println(elements.get(0).text());
```



##### 对象使用

​	1.Jsoup：工具类，可以解析 html 或 xml，返回 Document

​		① parse(File in, String charset)：解析文件

​		② parse(String html)：解析字符串

​		③ parse(URL url, int timeoutMillis)：通过网络路径获取指定html 或 xml 的文档对象

​	2.Document：文档对象，代表内中的 DOM 树

​		① getElementById(String id)：根据 id 属性值获取唯一 Element 对象

​		② getElementByTag(String tagName)：根据标签名获取集合

​		③ getElementByAttribute(String key)：根据属性名获取集合

​		④ getElementByAttributeValue(String key, String value)：根据对应属性名和属性值获取集合

​		示例：

```java
Elements students = document.getElementsByTag("student");
Elements elementById = document.getElementsByAttribute("id");
Elements elementsByAttributeValue = document.getElementsByAttributeValue("number", "itcast_0001");
Element elementById1 = document.getElementById("1");
```

​	3.Elements：元素 Element 的集合，可以当作 ArrayList 使用

​	4.Element：元素对象

​		① 获取子元素对象：同 Document 的方法，仅获取本元素内的子元素

​		② 获取属性值：attr(String key)，根据属性名称获取属性值

​		③ 获取文本内容：

​				text()：获取所有子标签的纯文本内容

​				html()：获取标签体的所有内容（包含子标签的字符串内容）

​		示例：

```java
Element ele_student = document.getElementsByTag("student").get(0);
Elements ele_name = ele_student.getElementsByTag("name");
String number = ele_student.attr("number");
String text = ele_name.text();
String html = ele_name.html();
```

​	5.Node：节点对象



### 快捷查询方式

##### selector 选择器

​	使用方法：select(String cssQuery)

​	语法：参考 Selector 类中定义的语法，类似 css 选择器

​	示例：

```java
Elements elements = document.select("name");
Elements elements1 = document.select("#itcast");
Elements elements2 = document.select("student[number=\"itcast_0001\"] > age");
```



##### Xpath

​	使用 Jsoup 的 Xpath 需要额外导入 jar 包

​	查询 w3cSchool 使用语法

​	示例：

```java
JXDocument jxDocument = new JXDocument(document);
List<JXNode> jxNodes = jxDocument.selN("//student");
//查询所有 student 标签下的 name 标签
List<JXNode> jxNodes2 = jxDocument.selN("//student/name");
//查询所有 student 标签下带 id 属性的 name 标签
List<JXNode> jxNodes3 = jxDocument.selN("//student/name[@id]");
//查询所有 student 标签下带 id 属性为 itcast 的 name 标签
List<JXNode> jxNodes4 = jxDocument.selN("//student/name[@id=itcast]");
```



# Tomcat

### 小记

端口号被占用：netstat -ano 找到被占用的端口号，任务管理器杀死对应进程

正常关闭：bin\shutdown.bat 或 ctrl + c

IDEA 动态 WEB 项目：Java EE、Java Enterprise、Maven、Gradle、Spring、Spring Initializr

IDEA 配置：run --> Edit Configurations --> + --> Tomcat Server --> Local

IDEA Tomcat 热部署：run --> Edit Configurations --> On "Update" action

注意：

​	① WEB-INF 中资源不能被浏览器直接访问

​	② IDEA 会为每一个 Tomcat 部署的项目单独建立一份配置文件

​			查看控制台启动 log 中 的 Using CATALINA_BASE:

​	③ Tomcat 运行后可以看到 target 文件夹，即为 Tomcat 部署的 Web 项目，区别于工作空间项目



### 部署方式

1.直接放入 webapps 目录，可打包为 war 包再放入

2.配置 conf\server.xml 文件，在 <HOST> 里添加如 <Context doBase="D:/h1" path="haha" />

3.在 conf\Catalina\localhost 里创建名为网址目录的 xml 文件，如：

​	文件名：abc.xml

​	文件内容：<Context doBase="D:/h1" />

​	含义：网址为 localhost:8080/abc/index.html，映射的路径为 D:/h1





# Servlet

### 快速入门

##### 运行原理

​	概念：运行在服务器端的小程序

​	1.服务器接收到请求后，解析 URL 路径，获取访问的 Servlet 资源路径

​	2.Tomcat 查找 web.xml 中 <url-pattern>

​	3.如果有，查找对应 <servlet-class> 全类名（往后配置文件中含全类名均为反射）

​	4.Tomcat 通过反射将字节码加载进内存，并创建其对象，调用方法



##### 简单示例

​	1.创建 Java EE 项目

​	2.定义一个类，实现 Servlet 接口中的抽象方法

​	3.在 web.xml 中配置 Servlet，或使用注解配置（Servlet 类上添加 @WebServlet("/demo3") ）

```xml
<servlet>
    <servlet-name>demo1</servlet-name>
    <servlet-class>cn.itcast.web.servlet.ServletDemo1</servlet-class>
</servlet>

<servlet-mapping>
    <servlet-name>demo1</servlet-name>
    <url-pattern>/demo1</url-pattern>
</servlet-mapping>
```



##### 方法详解

​	1.init：初始化方法，在 Servlet 被创建时执行，只执行一次，可在 <servlet> 中配置创建时机

​		① 第一次被访问时创建，<load-on-startup> 值为负数（默认，可不写此标签）

​		② 在服务器启动时创建，<load-on-startup> 值为 0 或正数

​	2.getServletConfig：获取 ServletConfig 对象（Servlet 配置对象）

​	3.service：提供服务类方法，每次 Servlet 被访问时均执行

​	4.getServletInfo：获取 Servlet 信息（版本、作者等）

​	5.destroy：销毁方法，在服务器正常关闭时执行

​	

### 体系结构

Servlet 接口

​		↓

GenericServlet 抽象类，将 Servlet 接口中除 service() 方法做了默认空实现

​		↓

HttpServlet 抽象类，对 http 协议的简单封装，使用时复写  doGet / doPost 方法（常用）



### Servlet 访问路径

1.一个 Servlet 可以定义多个访问路径：@WebServlet({"/d3", "/dd3", "/ddd3"})

2.urlpartten 路径定义规则：

​	① /xxx

​	② /xxx/xxx

​	③ *.do



# HTTP

### 概念

##### 特点

​	1.基于 TCP/IP 高级协议

​	2.默认端口号：80

​	3.基于请求/响应模型：一次请求对应一次相应

​	4.无状态的：每次请求之间相互独立，不能交互数据

##### 历史版本

​	1.0：每次请求都会建立新的连接

​	1.1：复用连接



### 请求消息数据格式

##### 请求行

​	请求方式   请求url   请求协议/版本

​	POST   /day14_shr/httptest.html   HTTP/1.1

##### 请求头

​	客户端浏览器告诉服务器的信息，格式为 请求头名称:请求值

​	常见请求头：

​		Host：请求主机

​		User-Agent：浏览器标识

​		Accept：响应信息格式

​		Accept-Language：响应语言环境

​		Accept-Encoding：响应编码

​		Referer：告诉服务器当前请求从哪来（可用于防盗链和统计工作）

​		Connection：连接状态，1.1 的标识为 keep-alive 代表可复用

​		Upgrade-Insecure-Requests：升级信息

##### 请求空行

​	用于分割 POST 请求的请求头和请求体

##### 请求体（正文）

​	封装 POST 请求消息的请求参数



##### 字符串格式

```http
POST /day14_shr/httptest.html HTTP/1.1
Host: localhost:8080
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.164 Safari/537.36 Edg/91.0.864.71
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Accept-Encoding: gzip, deflate, br
Referer: http://localhost:8080/day14_shr/httptest.html
Connection: keep-alive
Upgrade-Insecure-Requests: 1

username=zhangsan
```



### 响应消息数据格式

##### 响应行

​	1.组成：协议/版本	响应状态码	状态码描述

​				HTTP/1.1 200 OK

​	2.状态码描述

​		① 1xx：服务器没有接收完全客户端信息，等待一段时间后发送 1xx 状态码

​		② 2xx：响应成功

​		③ 3xx：重定向。302(重定向)，304(服务器告诉客户端访问本地缓存，节省传输流量)

​		④ 4xx：客户端错误。405(请求方式没有对应的doXxx方法)

​		⑤ 5xx：服务器端错误。500(服务器内部出现异常)

##### 响应头

​	格式为 请求头名称:请求值

​	常见响应体：

​		Content-Type：服务器告诉客户端本次响应体数据格式以及编码格式

​		Content-Disposition：服务器告诉客户端以什么格式打开响应体数据

​				in-line：默认值，在当前页面内打开

​				attachment; filename=xxx：以附件形式打开响应体，常用于文件下载

##### 响应空行

​	分割响应头和响应体

##### 响应体

​	封装响应的 html 页面代码



##### 字符串格式

```http
HTTP/1.1 200 OK
Server: Apache-Coyote/1.1
Content-Type: text/html;charset=UTF-8
Content-Length: 169
Date: Mon, 26 Jul 2021 03:15:19 GMT
X-DNS-Prefetch-Control: off

<html></html>
```



# Request

### 基本概念

URI：统一资源标识符

URL：统一资源定位符



##### 运行原理

​	1.Tomcat 服务器会根据请求 url 中的资源路径，创建对应的 ServletDemo1 的对象

​	2.Tomcat 服务器会创建 request 和 response 对象，request 对象中封装请求消息数据

​	3.Tomcat 将 request 和 response 两个对象传递给 service 方法，并调用

​	4.程序员可通过 request 对象获取请求消息信息，通过response 对象设置响应数据信息

​	5.服务器做出响应，在响应之前会从 response 对象中拿程序员设置的响应数据



##### 继承结构

​	ServletRequest			--> 接口

​	HttpServletRequest	--> 接口，继承自 ServletRequest

​	RequestFacade			--> 类，实现 HttpServletRequest，服务器自动创建



### 功能

##### 获取请求消息数据

​	1.获取请求行

​		GET /day14/demo1?name=abc HTTP/1.1

​		① getMethod()				 请求方式：GET

​		② getContextPath()		 虚拟目录：/day14（常用）

​		③ getServletPath()		   Servlet 路径：/demo1

​		④ getQueryString()		  get 方式请求参数：name=abc

​		⑤ getRequestURI()		  请求 URI：/day14/demo1（常用）

​		⑥ getRequestURL()		 请求 URL：http://localhost/day14/demo1

​		⑦ getProtocol()				协议及版本：HTTP/1.1

​		⑧ getRemoteAddr()		客户机 IP地址



​	2.获取请求头

​		① getHeader(String name)：通过请求头名称获取请求头值（常用）

​		② Enumeration<String> getHeaderNames()：获取所有请求头名称

​		示例：

```java
//示例1
Enumeration<String> headerNames = request.getHeaderNames();
while (headerNames.hasMoreElements()) {
    String name = headerNames.nextElement();
    String value = request.getHeader(name);
    System.out.println(name + ": " + value);
}
//示例2
String agent = request.getHeader("user-agent");
if (agent.contains("Chrome")) {
    response.setContentType("text/html;charset=utf-8");
    response.getWriter().write("谷歌浏览器访问");
}
//示例3
String referer = request.getHeader("referer");
System.out.println(referer);
if (referer != null) {
    if (referer.contains("/day14")) {
        System.out.println("播放");
    } else {
        System.out.println("盗链");
    }
}
```

​	

3.获取请求体

​	① BufferReader getReader()：获取字符输入流，只能操作字符数据（常用）

​	② ServletInputStream getInputStream()：获取字节输入流，可以操作所有类型数据

​	示例：

```java
BufferedReader br = request.getReader();
String line = null;
while ((line = br.readLine()) != null) {
    System.out.println(line);
}
```



##### 其他功能

​	1.获取请求参数通用方式，get 和 post 通用（如 username）

​		① String getParameter(String name)：根据参数名获取参数值 username=zs

​		② String[] getParameterValues(String name)：根据参数名获取参数数组 hobby=xx&hobby=yy

​		③ Enumeration<String> getParameterNames()：获取所有请求参数名

​		④ Map<String, String[]> getParameterMap()：获取所有参数的 Map 集合

​		⑤ request.setCharacterEncoding("UTF-8")：获取参数前设置，解决 post 参数中文乱码



​	2.请求转发：服务器内部资源跳转方式

​		① 步骤：

​			Ⅰ：通过 request 对象获取请求转发对象：getRequestDispatcher(String path)

​			Ⅱ：使用 RequestDispatcher 对象进行转发：forward(ServletRequest r1,ServletResponse r2)

​			使用：

​		② 特点：

​			Ⅰ：浏览器地址栏路径不发生变化

​			Ⅱ： 只能转发到当前服务器内部资源中

​			Ⅲ：转发是一次请求（多个 Servlet 之间使用同一个请求）



​	3.共享数据

​		域对象：一个有作用范围的对象，可以在范围内共享数据

​		request 域：代表一次请求的范围，一般用于请求转发的多个资源中共享数据

​		方法：

​			① setAttribute(String name, Object obj)：存储数据

​			② getAttribute(String name)：通过键获取值

​			③ removeAttribute(String name)：通过键移除键值对



​	4.获取 ServletContext

​		获取：getServletContext()



### BeanUtils

​	导包：commons-beanutils-1.8.0.jar

​	作用：简化将参数封装到实体类的操作

​	方法：

​		① populate(Object obj, Map map)：将对应 map 集合的键值对信息封装到对应 JavaBean 对象中

​		② setProperty(Object obj, "属性", "成员变量")	（不常用）

​		③ getProperty()		（不常用）

​	注：setProperty(user, "hehe", "male") 表示方法为 setHeHe(Xxx xxx)，成员变量值为 male

​	示例：

```java
Map<String, String[]> map = req.getParameterMap();
User loginUser = new User();
BeanUtils.populate(loginUser, map);
```



### 登录案例

##### 开发步骤

​	1.创建项目，导入 html  页面，配置文件，jar 包

​	2.创建数据库环境

​	3.创建用户实体类 cn.itcast.domain.User

​	4.创建 JDBC 工具类 cn.itcast.util.JDBCUtils

​	5.创建 DAO 层 cn.itcast.dao.UserDao

​	6.编写 Servlet 类 cn.itcast.web.servlet.LoginServlet

​	7.编写 FailServlet 和 SuccessServlet



##### 详细代码

JDBCUtils

```java
package cn.itcast.util;

import com.alibaba.druid.pool.DruidDataSourceFactory;

import javax.sql.DataSource;
import java.io.IOException;
import java.io.InputStream;
import java.sql.Connection;
import java.sql.SQLException;
import java.util.Properties;

public class JDBCUtils {

    private static DataSource ds;

    static {
        try {
            Properties pro = new Properties();
            InputStream is = JDBCUtils.class.getClassLoader().getResourceAsStream("druid.properties");
            pro.load(is);
            ds = DruidDataSourceFactory.createDataSource(pro);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static DataSource getDataSource() {
        return ds;
    }

    public static Connection getConnection() throws SQLException {
        return ds.getConnection();
    }

}
```



UserDao

```java
package cn.itcast.dao;

import cn.itcast.domain.User;
import cn.itcast.util.JDBCUtils;
import org.springframework.dao.DataAccessException;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;

public class UserDao {

    private JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());

    public User login(User loginUser) {
        try {
            String sql = "select * from user where username = ? and password = ?";
            User user = template.queryForObject(
                    sql,
                    new BeanPropertyRowMapper<User>(User.class),
                    loginUser.getUsername(),
                    loginUser.getPassword()
            );
            return user;
        } catch (DataAccessException e) {
            e.printStackTrace();
            return null;
        }

    }

}
```



LoginServlet

```java
package cn.itcast.web.Servlet;

import cn.itcast.dao.UserDao;
import cn.itcast.domain.User;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/loginServlet")
public class LoginServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setCharacterEncoding("utf-8");
        
        /*String username = req.getParameter("username");
        String password = req.getParameter("password");

        User loginUser = new User();
        loginUser.setUsername(username);
        loginUser.setPassword(password);*/

        Map<String, String[]> map = req.getParameterMap();
        User loginUser = new User();
        try {
            BeanUtils.populate(loginUser, map);
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        }

        UserDao userDao = new UserDao();
        User user = userDao.login(loginUser);

        if (null == user) {
            req.getRequestDispatcher("/failServlet").forward(req, resp);
        } else {
            req.setAttribute("user", user);
            req.getRequestDispatcher("/successServlet").forward(req, resp);
        }
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doGet(req, resp);
    }
}
```



FailServlet

```java
package cn.itcast.web.Servlet;

import javax.servlet.*;
import javax.servlet.http.*;
import javax.servlet.annotation.*;
import java.io.IOException;

@WebServlet(name = "FailServlet", value = "/failServlet")
public class FailServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html;charset=utf-8");
        response.getWriter().write("登录失败！用户名或密码错误");
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doGet(request, response);
    }
}
```



SuccessServlet

```java
package cn.itcast.web.Servlet;

import cn.itcast.domain.User;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/successServlet")
public class SuccessServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        User user = (User) request.getAttribute("user");
        if (null != user) {
            response.setContentType("text/html;charset=utf-8");
            response.getWriter().write("登录成功！" + user.getUsername() + "，欢迎！");
        }
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doGet(request, response);
    }
}
```



# Response

### 功能

1.设置相应行（设置状态码）：setStatus(int sc)

2.设置响应头：setHeader(String name, String value)

3.设置响应体：

​		① 获取输出流：

​				字符输出流：PrintWriter getWriter()

​				字节输出流：ServletOutputStream getOutputStream()

​		② 使用输出流，将数据输出到客户端浏览器



### 案例

##### 完成重定向

​	1.代码：response.sendRedirect("/day15/responseDemo2")

​	2.原理：response.setStatus(302); response.setHeader("location", "day15/responseDemo2");

​	3.重定向特点：（redirect）

​		① 地址栏发送变化

​		② 可以访问其他站点的资源

​		③ 重定向是两次请求，不能使用 request 对象共享数据

​	4.转发特点：（forward）

​		① 转发地址栏路径不变

​		② 转发只能访问当前服务器下资源

​		③ 转发是一次请求，可以使用 request 对象共享数据

​	5.路径写法：

​		① 相对路径：如 ./index.tml 不以 / 开头，./ 表示当前目录，../ 表示后退一级目录

​		② 绝对路径：如 http://localhost/day15/rd2 或 /day15/rd2 等以 / 开头的路径

​			给客户端使用：需要加虚拟目录（项目访问目录，如/day15/），如 <a>，<form>，重定向

​				一般使用动态虚拟目录获取：request.getContextPath();

​			给服务器使用：不需要加虚拟目录



##### 输出字符数据

​	步骤：获取字符输出流，输出数据

​	乱码问题：

​		1.PrintWriter pw = response.getWriter(); 获取的流默认编码为 ISO-8859-1

​		2.设置该流默认编码：response.setCharacterEncoding("utf-8")

​		3.告诉浏览器响应体使用的编码：response.setHeader("content-type", "text/html; charset=utf-8")

​		4.简单形式（原理为上面两句）：response.setContentType("text/html; charset=utf-8")



##### 输出字节数据

​	字节输出流常用于输出任意二进制数据，也可输出字符

​	步骤：

```java
ServletOutputStream sos = response.getOutputStream();
sos.write("你好".getBytes(StandardCharsets.UTF_8));
```



##### 验证码

```java
int width = 100;
int height = 50;

//创建对象，在内存中存储图片（验证码图片对象）
BufferedImage image = new BufferedImage(width, height, BufferedImage.TYPE_INT_RGB);

//美化图片
Graphics g = image.getGraphics();   //画笔对象
g.setColor(Color.PINK);
g.fillRect(0, 0, width, height);

//画边框
g.setColor(Color.BLUE);
g.drawRect(0, 0, width-1, height-1);

String str = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
Random rand = new Random();

//生成随机字符
for (int i=0; i<=4; i++) {
    int index = rand.nextInt(str.length());
    char ch = str.charAt(index);
    g.drawString(ch+"", width/5*i, height/2);
}

//画干扰线
g.setColor(Color.GREEN);
for (int i=0; i<10; i++) {
    int x1 = rand.nextInt(width);
    int x2 = rand.nextInt(width);
    int y1 = rand.nextInt(height);
    int y2 = rand.nextInt(height);
    g.drawLine(x1, x2, y1, y2);
}

//将图片输出到页面
ImageIO.write(image, "jpg", response.getOutputStream());
```



##### 验证码点击刷新

```html
<img src="/day15/CheckCodeServlet" id="checkCode"/>
<script>
  window.onload = function () {
    var img = document.getElementById("checkCode");
    img.onclick = function () {
      //加时间戳，让服务器响应，不读取本地缓存
      var date = new Date().getTime();
      img.src = "/day15/CheckCodeServlet?" + date;
    }
  }
</script>
```



# ServletContext 对象

### 概念与获取

概念：代表整个 web 应用，可以和程序的容器（服务器）通信

获取：

​	1.通过 request 对象获取：request.getServletContext();

​	2.通过 HttpServlet 获取this.getServletContext();



### 功能

1.获取 MIME 类型

​	MIME 类型：在互联网通信中定义的一种文件数据类型

​	格式：大类型/小类型	text/html	image/jpeg

​	获取：String getMimeType(String file)



2.域对象：共享数据

​	ServletContext 对象范围：所有用户所有请求的数据

​	① setAttribute(String name, Object value)

​	② getAttribute(String name)

​	③ removeAttribute(String name)



3.获取文件(服务器)路径

```java
context.getRealPath("/b.txt");					//web 目录下资源
context.getRealPath("/WEB-INF/c.txt");			//WEB-INF 目录下资源
context.getRealPath("/WEB-INF/classes/a.txt");	//src 目录下资源
```



### 案例-文件下载

DownloadServlet

```java
String filename = request.getParameter("filename");
//找到文件服务器路径
ServletContext servletContext = this.getServletContext();
String realPath = servletContext.getRealPath("/downloadsrc/" + filename);
//用字节流关联
FileInputStream fis = new FileInputStream(realPath);

//设置 response 响应头
String mimeType = servletContext.getMimeType(filename);
response.setHeader("content-type", mimeType);

//解决中文文件名问题
String agent = request.getHeader("user-agent");
filename = DownLoadUtils.getFileName(agent, filename);

response.setHeader("content-disposition", "attachment;filename=" + filename);

//将输入流数据写出到输出流中
ServletOutputStream sos = response.getOutputStream();
byte[] buff = new byte[1024 * 8];
int len = 0;
while ((len = fis.read(buff)) != -1) {
    sos.write(buff, 0, len);
}
fis.close();
```



download.html

```html
<a href="/day15/downLoadServlet?filename=1.png">图片</a>
<a href="/day15/downLoadServlet?filename=2.avi">视频</a>
<a href="/day15/downLoadServlet?filename=鲸鱼.png">中文文件名</a>
```



downloadUtils

```java
public static String getFileName(String agent, String filename) throws UnsupportedEncodingException {
    if (agent.contains("MSIE")) {
        // IE浏览器
        filename = URLEncoder.encode(filename, "utf-8");
        filename = filename.replace("+", " ");
    } else if (agent.contains("Firefox")) {
        // 火狐浏览器
        BASE64Encoder base64Encoder = new BASE64Encoder();
        filename = "=?utf-8?B?" + base64Encoder.encode(filename.getBytes("utf-8")) + "?=";
    } else {
        // 其它浏览器
        filename = URLEncoder.encode(filename, "utf-8");
    }
    return filename;
}
```



# Cookie

### 会话技术

1.会话：一次会话中包含多次请求和响应

2.功能：在一次会话的范围内多次请求之间共享数据

3.方式：

​	① 客户端会话技术：Cookie

​	② 服务端会话技术：Session



### 概念与使用

概念：将数据保存到客户端

实现原理：基于响应头 set-cookie 和请求头 cookie 实现

##### 使用步骤

​	1.new Cookie(String name, String value)	--- 创建 Cookie 对象，绑定数据

​	2.response.addCookie(Cookie cookie)		--- 发送 Cookie 对象

​	3.Cookie[] request.getCookies()					--- 获取 Cookie，拿到数据



### Cookie 的细节

1.一次可以创建多个 cookie 对象，使用 response 调用多次 addCookie 方式发送

2.Cookie 存活时间：

​	① 默认情况下，浏览器关闭后 Cookie 数据会被销毁

​	② 持久化存储：setMaxAge(int seconds)

​			正数：将 Cookie 写入硬盘，并指定存活秒数

​			负数：默认值

​			零：删除 Cookie 信息

3.Cookie 中文问题

​	① Tomcat 8 之前 Cookie 不能直接存储中文数据，一般采用 URL 编码转码

​	② Tomcat 8 之后支持，但特殊字符不支持，同样使用 URL 编码转码

4.Cookie 共享问题

​	① 默认情况下不能共享，可使用 setPath("/") 设置为同 Tomcat 服务器中共享，或指定共享的虚拟目录

​	② 不同服务器中一般通过设置一级域名共享，如 setDomain(".baidu.com");



### 特点和作用

##### 特点

​	1.Cookie 存储在客户端浏览器

​	2.浏览器对单个 Cookie 大小有限制（4kb），以及同一域名下 Cookie 数量也有限制（20个）

##### 作用

​	1.Cookie 一般用于存储少量且不太敏感的数据

​	2.在不登陆情况下完成服务器对客户端的身份识别（如网站个性化设置）



### 案例

```java
response.setContentType("text/html;charset=utf-8");
Cookie[] cookies = request.getCookies();
boolean flag = false;

if (null != cookies || cookies.length > 0) {
    for (Cookie cookie : cookies) {
        if ("lastTime".equals(cookie.getName())) {
            flag = true;    //有该 Cookie 不是第一次访问
            Date date = new Date();
            SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
            String str_date = sdf.format(date);
            str_date = URLEncoder.encode(str_date, "utf-8");
            cookie.setValue(str_date);
            cookie.setMaxAge(60 * 60 * 24 * 30);
            response.addCookie(cookie);

            //响应数据
            String value = cookie.getValue();
            value = URLDecoder.decode(value, "utf-8");
            response.getWriter().write("<h1>欢迎回来，上次访问时间为：" + value + "</h1>");
            break;
        }
    }
}

if (null == cookies || cookies.length == 0 || flag == false) {
    Date date = new Date();
    SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
    String str_date = sdf.format(date);
    str_date = URLEncoder.encode(str_date, "utf-8");

    Cookie cookie = new Cookie("lastTime", str_date);
    cookie.setMaxAge(60 * 60 * 24 * 30);
    response.addCookie(cookie);

    response.getWriter().write("<h1>您好，欢迎您首次访问</h1>");
}
```



# Session

### 概念与使用

概念：服务端会话技术，在一次会话的多次请求间共享数据，将数据保存在服务端的对象中

原理：Session 的实现依赖于 Cookie（JSESSIONID）



##### 快速入门

​	1.获取 HttpSession 对象：HttpSession session = request.getSession();

​	2.使用 HttpSession 对象：

​		Object getAttribute(String name)

​		void setAttribute(String name, Object value)

​		void removeAttribute(String name)



### 细节

1.客户端关闭，服务器不关闭，默认情况下两次获取的 session 不为同一个

​	注：如果需要相同，则可创建 Cookie，键为 JSESSIONID，设置最长存活时间

​		Cookie c = new Cookie("JSESSIONID", session.getId());

​		c.setMaxAge(60 * 60);

​		response.addCookie(c);

2.客户端不关闭，服务器关闭，两次获取的 session 也不是同一个

​	注：Tomcat 服务器能自动完成以下操作：（IDEA 中不行）

​		session 钝化：服务器关闭前，将 session 对象序列化到硬盘

​		session 活化：服务器启动后，将 session 文件转化为内存中的 session 对象

3.session 的销毁时机

​	① 服务器关闭

​	② session 对象调用 invalidate()

​	③ session 默认失效时间 30 分钟（Tomcat 目录下 conf/web.xml 中 session-config 可修改）



### 特点

1.session 用于存储一次会话的多次请求的数据，存在于服务端

2.session 可以存储任意类型、任意大小的数据

3.session 与 cookie 的区别：

​	① session 存储数据在服务端，cookie 在客户端

​	② session 没有数据大小限制，cookie 有

​	③ session 数据相对安全，cookie 相对不安全



### 案例

LoginServlet

```java
package cn.itcast.servlet;

import javax.servlet.*;
import javax.servlet.http.*;
import javax.servlet.annotation.*;
import java.io.IOException;

@WebServlet(name = "LoginServlet", value = "/LoginServlet")
public class LoginServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request, response);
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setCharacterEncoding("UTF-8");
        String username = request.getParameter("username");
        String password = request.getParameter("password");
        String checkCode = request.getParameter("checkCode");
        //获取生成的验证码
        HttpSession session = request.getSession();
        String checkCode_session = (String) session.getAttribute("checkCode_session");
        //删除 session 中存储的验证码
        session.removeAttribute("checkCode_session");
        if (checkCode_session != null && checkCode_session.equalsIgnoreCase(checkCode)) {
            if ("zhangsan".equals(username) && "123".equals(password)) {
                session.setAttribute("user", username);
                response.sendRedirect(request.getContextPath() + "/success.jsp");
            } else {
                request.setAttribute("login_error", "用户名或密码错误");
                request.getRequestDispatcher("/login.jsp").forward(request, response);
            }
        } else {
            request.setAttribute("cc_error", "验证码错误");
            request.getRequestDispatcher("/login.jsp").forward(request, response);
        }
    }
}
```



CheckCodeServlet

```java
package cn.itcast.servlet;

import javax.imageio.ImageIO;
import javax.servlet.*;
import javax.servlet.http.*;
import javax.servlet.annotation.*;
import java.awt.*;
import java.awt.image.BufferedImage;
import java.io.IOException;
import java.util.Random;

@WebServlet(name = "CheckCodeServlet", value = "/CheckCodeServlet")
public class CheckCodeServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doPost(request, response);
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        int width = 100;
        int height = 50;

        //创建对象，在内存中存储图片（验证码图片对象）
        BufferedImage image = new BufferedImage(width, height, BufferedImage.TYPE_INT_RGB);

        //美化图片
        Graphics g = image.getGraphics();   //画笔对象
        g.setColor(Color.PINK);
        g.fillRect(0, 0, width, height);

        //画边框
        g.setColor(Color.BLUE);
        g.drawRect(0, 0, width-1, height-1);

        String str = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
        Random rand = new Random();
        StringBuilder sb = new StringBuilder();

        //生成随机字符
        for (int i=0; i<4; i++) {
            int index = rand.nextInt(str.length());
            char ch = str.charAt(index);
            sb.append(ch);
            g.drawString(ch+"", width/5*i, height/2);
        }
        request.getSession().setAttribute("checkCode_session", sb.toString());

        //画干扰线
        g.setColor(Color.GREEN);
        for (int i=0; i<10; i++) {
            int x1 = rand.nextInt(width);
            int x2 = rand.nextInt(width);
            int y1 = rand.nextInt(height);
            int y2 = rand.nextInt(height);
            g.drawLine(x1, x2, y1, y2);
        }

        //将图片输出到页面
        ImageIO.write(image, "jpg", response.getOutputStream());
    }
}
```



login.jsp

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>login</title>


    <script>
        window.onload = function () {
            document.getElementById("img").onclick = function () {
                this.src = "/day16/CheckCodeServlet?time=" + new Date().getTime();
            }
        }
    </script>
    <style>
        div{
            color: red;
        }

    </style>
</head>
<body>

    <form action="/day16/LoginServlet" method="post">
        <table>
            <tr>
                <td>用户名</td>
                <td><input type="text" name="username"></td>
            </tr>
            <tr>
                <td>密码</td>
                <td><input type="password" name="password"></td>
            </tr>
            <tr>
                <td>验证码</td>
                <td><input type="text" name="checkCode"></td>
            </tr>
            <tr>
                <td colspan="2"><img id="img" src="/day16/CheckCodeServlet"></td>
            </tr>
            <tr>
                <td colspan="2"><input type="submit" value="登录"></td>
            </tr>
        </table>


    </form>

    <div><%=request.getAttribute("cc_error") == null ? "" : request.getAttribute("cc_error")%></div>
    <div><%=request.getAttribute("login_error") == null ? "" : request.getAttribute("login_error")%></div>

</body>
</html>
```



# JSP

### 脚本

1.<%  代码 %>：service 方法中可以定义什么，该脚本中就可以定义什么

2.<%! 代码 %>：JSP 转换后的java类的成员位置

3.<%= 代码 %>：定义的 java 代码会输出到页面上。输出语句中可以定义什么，该脚本中就可以定义什么



### 指令

作用：用于配置 JSP 页面，导入资源文件

格式：<%@ 指令名称 属性1=值1 属性2=值2 ... %>



##### 分类

​	1.page：用于配置 JSP 页面

​		① contentType：等同于 response.setContentType()

​			--- 设置响应体的 MIME 类型及字符集

​			--- 设置当前 JSP 页面编码（仅高级 IDE 生效，低级工具则需设置 pageEncoding 设置当前页面字符集）

​		② import：导包

​		③ errorPage：当前页面发生异常后，跳转到指定错误页面

​		④ isErrorPage：标识当前页面是否为错误页面（true 则可使用内置 exception 对象）

​	2.include：页面包含，格式为 <%@ include file="banner.jsp" %>

​	3.taglib：导入资源，格式为 <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>



### 注释

1.html 注释：<!--   -->

2.JSP 注释：<%--   --%>



### 内置对象

|   变量名    |      真实类型       |                作用或作用范围                |
| :---------: | :-----------------: | :------------------------------------------: |
| pageContext |     PageContext     | 当前页面共享数据，还可以获取其他八个内置对象 |
|   request   | HttpServletRequest  |        一次请求访问的多个资源（转发）        |
|   session   |     HttpSession     |             一次会话的多个请求间             |
| application |   ServletContext    |              所有用户间共享数据              |
|  response   | HttpServletResponse |                   响应对象                   |
|    page     |       Object        |      当前页面（Servlet）的对象，既 this      |
|     out     |      JspWriter      |          输出对象，数据输出到页面上          |
|   config    |    ServletConfig    |              Servlet 的配置对象              |
|  exception  |      Throwable      |                   异常对象                   |

注：out 字符输出流对象可以将数据输出到页面上，和 response.getWriter() 类似
	response.getWriter() 和 out.write() 的区别：
		① 在 Tomcat 服务器真正给客户端做出响应前，先找 response 缓冲区数据，再找 out 缓冲区数据
		② response.getWriter() 数据输出永远在 out.write() 之前



# MVC 开发模式

### 三层模式

M：Model 模型，JavaBean

* 完成具体的业务操作，如：查询数据库，封装对象

V：View 视图，JSP
* 展示数据

C：Controller 控制器，Servlet
* 获取用户的输入
* 调用模型
* 将数据交给视图进行展示



# EL 表达式

### 小记

概念：Expression Language 表达式语言

作用：替换和简化 JSP 页面中的 Java 代码的编写

语法：${ 表达式 }

注意：JSP 页面中默认指出 EL 表达式，如果要忽略 EL 表达式

​	① 设置 page 指令中： isELIgnored="true"

​	② \ ${ 表达式 }：忽略当前 EL 表达式



### 使用

##### 运算

​	1.算数运算符：+ - * /(div) %(mod)

​	2.比较运算符：> < >= <= == !=

​	3.逻辑运算符：&&(and) ||(or) !(not)

​	4.空运算符：empty，用于判断字符串、集合、数组对象是否为null或者长度是否为 0

​		① ${empty list：判断字符串、集合、数组对象是否为null或者长度为 0

​		② ${not empty str}：表示判断字符串、集合、数组对象是否不为null 并且 长度 > 0



##### 获取值

​	1.${域名称.键名}：从指定域中获取指定键的值，其中域名称为

​		① pageScope		--> pageContext

​		② requestScope 	--> request

​		③ sessionScope 	--> session

​		④ applicationScope --> application（ServletContext）

​	2.${键名}：表示依次从最小的域中查找是否有该键对应的值，直到找到为止

​	3.获取对象、List集合、Map集合的值

​		① 对象：${域名称.键名.属性名}		--> 本质上会去调用对象的getter方法

​		② List集合：${域名称.键名[索引]}

​		③ Map集合：${域名称.键名.key名称} 或 ${域名称.键名["key名称"]}

​		例：${map.user.name}

​	注：EL 表达式只能从域对象中获取值



##### 隐式对象

​	EL 表达式中有 11 个隐式对象

​	pageContext： 获取 JSP 其他八个内置对象

​	常用：${pageContext.request.contextPath}：动态获取虚拟目录



# JSTL

### 小记

概念：Java Server Pages Tag Library，JSP 标准标签库，Apache 组织开源提供

作用：简化和替换 JSP 页面上的 Java 代码

使用步骤：

​	① 导入 JSTL 相关 jar 包

​	② 引入标签库：<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>



### 常见标签

##### if

​	相当于 Java 的 if

​	属性：test 必要，接收 boolean 表达式，为 true 显示标签内 html 内容，一般搭配 EL 表达式

​	注意：c:if 标签没有 else 情况

​	示例：

```jsp
<c:if test="${not empty myList}">
    遍历集合操作...
</c:if>
<c:if test="${number % 2 != 0}">
    <h1>${number}为奇数</h1>
</c:if>
```



##### choose

​	相当于 Java 的 switch

​	1.choose 标签声明，相当于 switch

​	2.when 标签判断，相当于 case

​	3.otherwise 标签做其他声明，相当于 default

​	示例：

```jsp
<c:choose>
	<c:when test="${number == 1}">星期一</c:when>
    <c:when test="${number == 2}">星期二</c:when>
    ...
    <c:otherwise>输入数字有误</c:otherwise>
</c:choose>
```



##### forEach

​	相当于 Java 的 for

​	1.完成重复操作，如 for(int i=0; i<10; i++) { }，属性为：

​		① begin：开始值，包含

​		② end：结束值，包含

​		③ var：临时变量

​		④ step：步长

​		⑤ varStatus：循环状态对象

​				index：容器中元素的索引，从 0 开始

​				count：循环次数，从 1 开始



​	2.遍历容器，如 for(User user : list) { }，属性为：

​		① items：容器对象

​		② var：容器中元素的临时变量

​		③ varStatus：循环状态对象



​	示例：

```jsp
<c:forEach begin="0" end="9" var="i" step="2" varStatus="s">
    ${i} ${s.index} ${s.count}
</c:forEach>

<%
	List list = new ArrayList();
	list.add("aaa");
	list.add("bbb");
	reqeust.setAttribute("list", list);
%>

<c:forEach items="${list}" var="str" varStatus="s">
	${s.index} ${s.count} ${str}
</c:forEach>
```



### 案例

在 request 域中有一个存有 User 对象的 List 集合，需用 JSTL + EL 将 List 集合数据展示到 JSP 页面的表格 table 中

jstl_test.jsp

```jsp
<%
    List list = new ArrayList();
    list.add(new User("张三", 23, new Date()));
    list.add(new User("李四", 24, new Date()));
    list.add(new User("王五", 25, new Date()));
    request.setAttribute("list", list);
%>

<table border="1" width="400px">

    <tr>
        <th>编号</th>
        <th>姓名</th>
        <th>年龄</th>
        <th>生日</th>
    </tr>

    <%-- 数据行 --%>
    <c:forEach items="${list}" var="user" varStatus="s">

        <c:if test="${s.count % 2 == 0}">
            <tr bgcolor="red">
        </c:if>
        <c:if test="${s.count % 2 != 0}">
            <tr bgcolor="green">
        </c:if>

            <td>${s.count}</td>
            <td>${user.name}</td>
            <td>${user.age}</td>
            <td>${user.birStr}</td>
        </tr>

    </c:forEach>

</table>
```



User.java

```java
package cn.itcast.domain;

import java.text.SimpleDateFormat;
import java.util.Date;

public class User {
    private String name;
    private int age;
    private Date birthday;

    public String getBirStr() {
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        return  sdf.format(birthday);
    }

    public User(String name, int age, Date birthday) {
        this.name = name;
        this.age = age;
        this.birthday = birthday;
    }

    public User() {
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", birthday=" + birthday +
                '}';
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public Date getBirthday() {
        return birthday;
    }

    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }
}
```



# 三层架构

### 界面层

框架：SpringMVC

作用：接收用户参数，封装数据，调用业务逻辑层完成处理，转发 JSP 页面完成显示



### 业务逻辑层

框架：Spring

作用：组合 DAO 层中的简单方法，形成复杂的功能（业务逻辑操作）



### 数据访问层

框架：MyBatis

作用：定义了对数据库最基本的 CRUD 操作



### 案例

技术选型：Servlet + JSP + MySQL + JDBCTemplate + Druid + BeanUtils + Tomcat

项目：day18_case



# 代理模式

### 概念

作用：增强对象的功能，涉及常见的设计模式

设计模式：一些通用的解决固定问题的方式，一般有装饰模式和代理模式

真实对象：被代理的对象

代理模式：代理对象代理真实对象，达到增强真实对象功能的目的



### 实现方式

1.静态代理：有一个类文件描述代理模式

2.动态代理：在内存中形成代理类

3.动态代理实现步骤：

​	① 代理对象和真实对象实现相同接口

​	② 代理对象 = Proxy.newProxyInstance();

​	③ 使用代理对象调用方法

​	④ 增强方法

4.增强方式：

​	① 增强参数列表

​	② 增强返回值类型

​	③ 增强方法体执行逻辑



### 示例

SaleComputer 接口

```java
public interface SaleComputer {
    public String sale (double money);
    public void show();
}
```



Lenovo 厂商类

```java
public class Lenovo implements SaleComputer {
    @Override
    public String sale(double money) {
        System.out.println("花了 " + money + " 元购买了一台联想电脑 ...");
        return "联想电脑";
    }

    @Override
    public void show() {
        System.out.println("展示电脑 ...");
    }
}
```



ProxyTest 代理商类

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

public class ProxyTest {
    public static void main(String[] args) {
        Lenovo lenovo = new Lenovo();

        /*
        三个参数：（固定写法）
            1.类加载器：真实对象，getClass().getClassLoader()
            2.接口数组：真实对象，lenovo.getClass().getInterfaces()
            3.处理器：new InvocationHandler()
         */
        SaleComputer proxy_lenovo = (SaleComputer) Proxy.newProxyInstance(lenovo.getClass().getClassLoader(), lenovo.getClass().getInterfaces(), new InvocationHandler() {
            /*
            代理逻辑编写的方法：代理对象调用的所有方法都会触发该方法执行
            参数：
                1.proxy：代理对象
                2.method：代理对象调用的方法（该方法已被封装为对象）
                3.args：代理对象调用方法时传递的实际参数
             */
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                //是否为 sale 方法
                if (method.getName().equals("sale")) {
                    //增强参数
                    double money = (double) args[0];
                    money = money * 0.85;   //代理商花费 6800 购买电脑赚差价
                    System.out.println("专车接送 ...");

                    //使用真实对象调用该方法，返回参数
                    Object obj = method.invoke(lenovo, money);

                    System.out.println("免费送货 ...");
                    return obj + "_鼠标垫";
                } else {
                    //其他方法，正常调用不做增强
                    Object obj = method.invoke(lenovo, args);
                    return obj;
                }
            }
        });

        String computer = proxy_lenovo.sale(8000);
        System.out.println(computer);
    }
}
```





# Filter

### 入门

概念：过滤器，一般用于登录验证、统一编码工作、敏感字符过滤 ...

快速入门：

​	1.定义类实现 Filter 接口（javax.servlet.*）

​	2.复写方法

​	3.配置拦截路径

​		① web.xml

​		② 注解：@WebFilter("/*")

示例：（注解）

```java
package cn.itcast.web.filter;

import javax.servlet.*;
import javax.servlet.annotation.WebFilter;
import java.io.IOException;

@WebFilter("/*")
public class FilterDemo1 implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException { }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("FilterDemo1 被执行");
        //放行
        filterChain.doFilter(servletRequest, servletResponse);
    }

    @Override
    public void destroy() { }
}
```



示例：（web.xml）

```xml
<filter>
    <filter-name>demo1</filter-name>
    <filter-class>cn.itcast.web.filter.FilterDemo1</filter-class>
</filter>
<filter-mapping>
    <filter-name>demo1</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```



### 过滤器细节

##### 执行流程

​	① 执行过滤器（一般对 request 增强）

​	② 执行放行后的资源

​	③ 回来执行过滤器放行代码下面的代码（一般对 response 增强）



##### 生命周期方法

​	① init：初始化方法，服务器启动后会创建 Filter 并调用，用于加载资源

​	② doFilter：每次请求被拦截资源时候执行

​	③ destory：服务器关闭时 Filter 被销毁并调用，用于释放资源



##### 过滤器配置

​	1.拦截路径配置

​		① 具体资源路径：/index.jsp	-- 只有访问 index.jsp 时才会执行

​		② 拦截目录：/user/*	-- 访问 /user 下所有资源时候会执行

​		③ 后缀名拦截：*.jsp	--访问所有后缀为 jsp 的资源时执行

​		④ 拦截所有资源：/*

​	2.拦截方式配置

​		① 注解配置：设置 dispatcherTypes 属性

​			REQUEST：默认值，浏览器直接请求资源

​			FORWARD：转发访问资源

​			INCLUDE：包含访问资源

​			ERROR：错误跳转资源

​			ASYNC：异步访问资源

​		② web.xml：

​			设置 <filter-mapping> 下 <dispatcher> 标签



##### 过滤器链

​	执行顺序：过滤器1 --> 过滤器2 --> 资源执行 --> 过滤器2 --> 过滤器1

​	先后顺序问题：

​		① 注解配置：按照类名的字符比较规则，值小的先执行

​		② web.xml：<filter-mapping> 谁定义在上边谁先执行



### 案例

##### 登陆验证

```java
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws ServletException, IOException {
    //强制转换
    //HttpServletRequest httpServletRequest = (HttpServletRequest) request;
    //获取资源请求路径
    String uri = ((HttpServletRequest) request).getRequestURI();
    //判断是否包含登录相关资源路径，注意排除 css/js/图片/验证码 等资源
    if (uri.contains("/login.jsp") || uri.contains("/loginServlet") ||
        uri.contains("/css/") || uri.contains("/js/") || uri.contains("/fonts/") || uri.contains("/checkCodeServlet")
    ) {
        chain.doFilter(request, response);
    } else {
        //不包含，需要验证是否已登录
        Object user = ((HttpServletRequest) request).getSession().getAttribute("user");
        if (null != user) {
            chain.doFilter(request, response);  //已登录，放行
        } else {
            request.setAttribute("login_msg", "尚未登陆");
            request.getRequestDispatcher("/login.jsp").forward(request, response);
        }
    }
}
```



##### 过滤敏感词

对 request 对象进行增强，增强获取参数相关方法

```java
import javax.servlet.*;
import javax.servlet.annotation.*;
import java.io.*;
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.sql.Array;
import java.util.ArrayList;
import java.util.List;

@WebFilter("/*")
public class SensitiveWordsFilter implements Filter {
    private List<String> list = new ArrayList<String>();    //敏感词汇集合

    public void init(FilterConfig config) throws ServletException {
        try {
            ServletContext servletContext = config.getServletContext();
            String realPath = servletContext.getRealPath("/WEB-INF/classes/敏感词汇.txt");
            BufferedReader br = new BufferedReader(new InputStreamReader(new FileInputStream(realPath),  "UTF-8"));
            String line = null;
            while (null != (line = br.readLine())) {
                list.add(line);
            }
            br.close();
            System.out.println(list);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public void destroy() {
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws ServletException, IOException {
        ServletRequest proxy_request = (ServletRequest) Proxy.newProxyInstance(request.getClass().getClassLoader(), request.getClass().getInterfaces(), new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                if (method.getName().equals("getParameter")) {
                    String value = (String) method.invoke(request, args);
                    if (null != value) {
                        for (String str : list) {
                            if (value.contains(str)) {
                                value = value.replaceAll(str, "***");
                            }
                        }
                    }
                    return value;
                }
                return method.invoke(request, args);
            }
        });
        chain.doFilter(proxy_request, response);	//注意：返回代理对象
    }
}
```



# Listener

### 概念

监听器：web 三大组件之一

事件监听机制：

​	1.事件：一件事情

​	2.事件源：事件发生的地方

​	3.监听器：一个对象

​	4.注册监听：将事件、事件源、监听器绑在一起，当事件源上发生某事件时执行监听器代码



### ServletContextListener

作用：监听 ServletContext 对象的创建和销毁，常用于加载初始化配置文件

方法：

​	contextInitialized(ServletContextEvent sce)：ServletContext 创建后被调用

​	contextDestroyed(ServletContextEvent sce)：ServletContext  销毁前被调用

步骤：

​	1.定义一个类，实现 ServletContextListener 方法

​	2.复写方法

​	3.配置

​		① web.xml：<listener> 标签

​		② 注解：@WebListener



### 范例

web.xml

```xml
<listener>
    <listener-class>cn.itcast.listener.ContextLoaderListener</listener-class>
</listener>

<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>/WEB-INF/classes/applicationContext.xml</param-value>
</context-param>
```



ContextLoaderListener

```java
import javax.servlet.ServletContext;
import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;
import javax.servlet.annotation.WebListener;
import java.io.FileInputStream;

//@WebListener
public class ContextLoaderListener implements ServletContextListener {

    @Override
    public void contextInitialized(ServletContextEvent sce) {
        ServletContext servletContext = sce.getServletContext();
        String contextConfigLocation = servletContext.getInitParameter("contextConfigLocation");
        String realPath = servletContext.getRealPath(contextConfigLocation);
        try {
            FileInputStream fis = new FileInputStream(realPath);
            System.out.println(fis);
        } catch (Exception e) {
            e.printStackTrace();
        }
        System.out.println("ServletContext 对象被创建");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("ServletContext 对象被销毁");
    }

}
```



# JQuery 基础

### 概念

##### JQuery 版本

​	1.x：兼容ie678,使用最为广泛的，官方只做BUG维护，
​		功能不再新增。因此一般项目来说，使用1.x版本就可以了，
​		最终版本：1.12.4 (2016年5月20日)
​	2.x：不兼容ie678，很少有人使用，官方只做BUG维护，
​		功能不再新增。如果不考虑兼容低版本的浏览器可以使用2.x，
​		最终版本：2.2.4 (2016年5月20日)
​	3.x：不兼容ie678，只支持最新的浏览器。除非特殊要求，
​		一般不会使用3.x版本的，很多老的jQuery插件不支持这个版本。
​		目前该版本是官方主要更新维护的版本。最新版本：3.2.1（2017年3月20日）

简单使用：var div1 = $("#div1");   alert(div1.html());



##### 与 JS 区别与转换

​	1.JQuery 对象操作更方便

​	2.JQuery 对象和 js 对象方法不通用

​	3.相互转换：

​		① JQury --> js: JQuery对象[索引]、JQuery对象.get(索引)

​		② js --> JQuery: $(js对象)

​	示例：

```javascript
//1.JS 方式获取所有 div
var divs = document.getElementsByTagName("div");
for (var i=0; i<divs.length; i++) {
	divs[i].innerHTML = "bbb";
}

//2.JQuery 方式获取所有 div
var $divs = $("div");
$divs.html("ccc");

//3.JQuery 转 JS
$divs[0].innerHTML = "ddd";
$divs.get(1).innerHTML = "eee";

//4.JS 转 JQuery
$(divs[0]).html("fff");
```



### 选择器

##### 基本语法

​	1.事件绑定：$("#btn1").click( function() { } );

​	2.入口函数：

​		① 定义：$( function() {} );

​		② 与 window.onload 区别：

​				window.onload 只能定义一次，后边的会将前边的覆盖掉

​				$(function) 可以定义多次

​	3.样式控制：

​		① $("#div1").css("background-color", "red");

​		② $("#div1").css("backgroundColor", "pink");



##### 分类

​	1.基本选择器

​		① 标签选择器：$("html标签")

​		② id 选择器：$("#id属性值")

​		③ 类选择器：$(".class属性值")

​		④ 并集选择器：$("选择器1, 选择器2 ...")



​	2.层级选择器

​		① 后代选择器：$("A B")

​		② 子选择器：$("A > B")



​	3.属性选择器

​		① 属性名称选择器：$("A[属性名]")	包含指定属性

​		② 属性选择器：$("A[属性名='值']")	包含指定属性且指定值

​		③ 复合属性选择器：$("A[属性名='值'] [] ...")	包含多个属性条件

​		示例：

```javascript
$("div[title]")				//含有属性值 title
$("div[title='test']")		//属性 title 值等于 test
$("div[title!='test']")		//属性 title 值不等于 test
$("div[title^='te']")		//属性 title 值以 te 开头
$("div[title$='est']")		//属性 title 值以 est 结尾
$("div[title*='es']")		//属性 title 值含有 es
$("div[id][title*='es']")	//选取有属性 id 的 div 元素，且属性 title 值含有 es
```



​	4.过滤选择器

​		① 首元素选择器：语法为 :first					选择第一个元素

​		② 尾元素选择器：语法为 :last					 选择最后一个元素

​		③ 非元素选择器：语法为 :not(selector)	 不包含指定内容的元素

​		④ 偶数选择器：语法为 :even					   偶数，从 0 开始

​		⑤ 奇数选择器：语法为 :odd						 奇数，从 0 开始

​		⑥ 等于索引选择器：语法为 :eq(index)		指定元素索引

​		⑦ 大于索引选择器：语法为 :gt(index)		 大于指定索引元素

​		⑧ 小于索引选择器：语法为 :lt(index)		  小于指定索引元素

​		⑨ 标题选择器：语法为 :header					获得标题（h1 ~ h6）元素，固定写法

​		示例：

```javascript
$("div:first")			//第一个 div
$("div:last")			//最后一个 div
$("div:not(.one)")		//class 不为 one 的所有 div
$("div:even")			//索引值为偶数的 div
$("div:odd")			//索引值为奇数的 div
$("div:gt(3)")			//索引值大于 3 的 div
$("div:eq(3)")			//索引值等于 3 的 div
$("div:lt(3)")			//索引值小于 3 的 div
$(":header")			//所有标题元素
```



​	5.表单过滤选择器

​		① 可用元素选择器：语法 :enabled

​		② 不可用元素选择器：语法 :disabled

​		③ 勾选选择器：语法 :checked

​		④ 选中选择器：语法 :selected

​		示例：

```javascript
//改变 input 类型为 text 且表单可用的值为 aaa
$("input[type='text']:enabled").val("aaa")

//改变 input 类型为 text 且表单不可用的值为 aaa
$("input[type='text']:disabled").val("aaa")

//输出 input 类型为 ckeckbox 且被选中的复选框的个数
alert($("input[type='checkbox']:checked").length)

//获取 id 为 job 的选择菜单中选项被选中的个数
alert($("#job > option:selected").length)
```



### DOM 操作

##### 内容操作

​	1.html()：获取 / 设置元素的标签体内容

​	2.text()：获取 / 设置元素的纯文本内容

​	3.val()：获取 / 设置元素的 value 属性值



##### 属性操作

​	1.通用属性操作

​		① attr()：获取 / 设置元素的属性（操作元素自定义属性时建议使用）

​		② removeAttr()：删除属性

​		③ prop()：获取 / 设置元素的属性（操作元素固有属性时建议使用）

​		④ removeProp()：删除属性

​		示例：

```javascript
var name = $("#bj").attr("name");			//获取北京节点的 name 属性值
$("#bj").attr("name", "dabeijing");			//设置北京节点的 name 属性值为 dabeijing
$("#bj").attr("discription", "didu");		//新增 discription 属性且值为 didu
$("#bj").removeAttr("name");				//删除 name 属性
var checked = ("hobby").prop("checked");	//获得 hobby 的选中状态
```



​	2.对 class 属性操作

​		① addClass()：添加 class 属性值

​		② removeClass()：删除 class 属性值

​		③ toggleClass()：切换 class 属性值（无则增加，有则删除）

​		④ css()：切换样式

​		示例：

```javascript
$("#one").prop("class", "second");				//采用属性增加样式
$("#one").addClass("second");					//添加 class 属性值
$("#one").removeClass("second");				//删除 class 属性值
$("#one").toggleClass("second");				//切换 class 属性值
var bgc = $("#one").css("backgroundColor");		//通过 css() 获取背景颜色
$("#one").css("backgroundColor", "pink");		//通过 css() 设置背景颜色
```



##### CRUD 操作

​	1.对象1.append(对象2)：对象2 添加到 对象1 内部，并且在末尾

​	2.对象1.prepend(对象2)：对象2 添加到 对象1 内部，并且在开头

​	3.对象1.appendTo(对象2)：对象1 添加到 对象2 内部，并且在末尾

​	4.对象1.prependTo(对象2)：对象1 添加到 对象2 内部，并且在开头

​	5.对象1.after(对象2)：对象2 添加到 对象1 后边，对象1 和 对象2 是兄弟关系

​	6.对象1.before(对象2)：对象2 添加到 对象1 前边，对象1 和 对象2 是兄弟关系

​	7.对象1.insertAfter(对象2)：对象1 添加到 对象2 后边，对象1 和 对象2 是兄弟关系

​	8.对象1.insertBefore(对象2)：对象1 添加到 对象2 前边，对象1 和 对象2 是兄弟关系

​	9.对象.remove()：将对象删除

​	10.对象.empty()：将对象后代元素全部清空，保留当前对象及其属性节点

​	11.对象.clone()：克隆当前对象

​	示例：$("#obj1").append($("#obj2"));



### 案例

##### 隔行换色

```javascript
$(function () {
   $("tr:gt(1):odd").css("backgroundColor", "pink");
   $("tr:gt(1):even").css("backgroundColor", "yellow");
});
```



##### 全选与全部选

```javascript
function selectAll(obj) {
   $(".itemSelect").prop("checked", obj.checked);
}
```



##### 表情追加

```javascript
$(function () {
    $("ul img").click(function () {
        $(".word").append($(this).clone());
    });
});
```



##### 左右移动

```javascript
$(function () {
   $("#toRight").click(function () {
      $("#rightName").append($("#leftName > option:selected"));
   });
   $("#toLeft").click(function () {
      $("#rightName > option:selected").appendTo($("#leftName"));
   });
});
```



# JQuery 高级

### 动画

##### 默认显示和隐藏

​	1.show([speed], [easing], [fn])

​	2.hide([speed], [easing], [fn])

​	3.toggle([speed], [easing], [fn])

​	参数：（可选）

​		① speed：速度（slow, normal, fast）或表示动画毫秒数（如：1000）

​		② easing：指定切换效果，默认 "swing"（快慢快），可用 "linear"（线性）

​		③ fn：动画完成后执行的函数，每个元素执行一次

​	示例：$("#showDiv").toggle("slow", "liner");



##### 滑动显示和隐藏

​	1.slideDown([speed], [easing], [fn])

​	2.slideUp([speed], [easing], [fn])

​	3.slideToggle([speed], [easing], [fn])



##### 淡入淡出显示和隐藏

​	1.fadeIn([speed], [easing], [fn])

​	2.fadeOut([speed], [easing], [fn])

​	3.fadeToggle([speed], [easing], [fn])



### 遍历

1.jq对象.each(callback)

​	示例：

```javascript
//方式1
citys.each(function () {
    alert(this.innerHTML);
});

//方式2,回调函数中定义参数，index（索引），element（元素对象）
citys.each(function (index, element) {
    alert($(this).html());
    alert(index + ":" + element.innerHTML);
    alert(index + ":" + $(element).html());
});

//方式3，continue 与 break
citys.each(function (index, element) {
    if ("上海" == $(element).html()) {
        return true;	//返回 false 则结束循环（break），返回 true 则继续（continue）
    }
    alert(index + ":" + $(element).html());
});
```



2.$.each(object, [callback])

​	示例：

```javascript
$.each(citys, function () {
    alert($(this).html());
});
```



3.for .. of（JQuery 3.0 后提供的版本）

​	示例：

```javascript
for (li of citys) {
    alert($(li).html());
}
```



### 事件绑定

1.JQuery 标准绑定方式

​	使用：JQ对象.事件方法(回调函数);

​	注：如果调用事件方法，不传递回调函数，则会触发浏览器默认行为

2.on 绑定事件 / off 解除绑定

​	JQ对象.on("事件名称", 回调函数)

​	JQ对象.off("事件名称")

3.事件切换 toggle

​	JQ对象.toggle(fn1, fn2 ...)

​	注：1.9 版本 toggle() 方法被移除，JQuery Migrate (迁移) 插件可以恢复此功能



示例：

```javascript
//链式编程
$("#name").mouseover(function() {
    alert("鼠标来了...");
}).mouseout(function () {
    alert("鼠标走了...");
});

//让输入框获得焦点
$("#name").focus();

//不写参数则将组件上所有事件全部解除
$("#btn").on("click", function() {} );
$("#btn").off();

//toggle 切换方法
$("#btn").toggle(function () {
    $("#myDiv").css("backgroundColor", "green");
}, function () {
    $("#myDiv").css("backgroundColor", "pink");
});
```



### 案例

##### 广告显示隐藏

```javascript
$(function () {
    setTimeout(adShow, 3000);
    setTimeout(adHide, 8000);
});
function adShow() {
    $("#ad").show("slow");
}
function adHide() {
    $("#ad").hide("slow");
}
```



##### 抽奖

```javascript
var rollInterval;    //开始定时器id
var index;      //随机角标
var imgs = ["../img/man00.jpg",
            "../img/man01.jpg",
            "../img/man02.jpg",
            "../img/man03.jpg",
            "../img/man04.jpg",
            "../img/man05.jpg",
            "../img/man06.jpg"
];

$(function () {
    //进入页面时按钮是否可点击
    $("#startID").prop("disabled", false);
    $("#stopID").prop("disabled", true);

    //开始按钮绑定单击事件
    $("#startID").click(function () {
        //定于循环器
        rollInterval = setInterval(function () {
            $("#startID").prop("disabled", true);
            $("#stopID").prop("disabled", false);

            index = Math.floor(Math.random() * 7);
            $("#img1ID").prop("src", imgs[index]);
        }, 20);
    });

    //结束按钮绑定单击事件
    $("#stopID").click(function () {
        $("#startID").prop("disabled", false);
        $("#stopID").prop("disabled", true);
        clearInterval(rollInterval);
        $("#img2ID").prop("src", imgs[index]).hide();
        $("#img2ID").show(1000);
    });
});
```



### 插件

作用：增强 JQuery 的功能

① $.fn.extend(object)：增强通过 JQuery 获取的对象的功能 --> $("#id")

② $.extend(object)：增强 JQuery 对象自身的功能 --> $. / JQuery.



##### 增强对象功能

```javascript
$.fn.extend({
    check:function () {
        this.prop("checked", true);
    },
    uncheck:function () {
       this.prop("checked", false);
    }
});

$(function () {
    $("#btn-check").click(function () {
        $("input[type='checkbox']").check();
    });
    $("#btn-uncheck").click(function () {
        $("input[type='checkbox']").uncheck();
    });
});
```



##### 增强 JQuery 功能

```javascript
$.extend({
    max:function (a, b) {
        return a >= b ? a : b;
    },
    min:function (a, b) {
        return a <= b ? a : b;
    }
});

alert($.max(4, 3));
alert($.min(1, 2));
```



# AJAX

概念：Asynchronous Javascript And XML，Ajax 在浏览器与 Web 服务器之间使用异步数据传输（HTTP 请求），这样就可使网页从服务器请求少量的信息，而不是整个页面。

序列化表单数据：$("#form").serialize()，常用于 get/post 的 data 传参



### 原生 JS 方式（了解）

```javascript
function fun() {
  //1.创建核心对象
  var xmlhttp;
  if (window.XMLHttpRequest) {
    // code for IE7+, Firefox, Chrome, Opera, Safari
    xmlhttp=new XMLHttpRequest();
  } else {
    // code for IE6, IE5
    xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
  }

  //2.建立连接
  /*
      参数：
          1.请求方式：GET、POST
              GET：请求参数在 URL 后拼接，send 方法参数为空
              POST：请求参数在 send 方法中定义
          2.请求 URL
          3.同步或异步：true 异步，false 同步
   */
  xmlhttp.open("GET", "ajaxServlet?username=tom", true);

  //3.发送请求
  xmlhttp.send();

  //4.接收并处理来自服务器的响应结果
  //当 xmlhttp 对象的就绪状态改变时，触发事件 onreadystatechange
  xmlhttp.onreadystatechange=function() {
    //判断 readyState 就绪状态是否为 4，status 响应状态码是否为 200
    if (xmlhttp.readyState==4 && xmlhttp.status==200) {
      //获取服务器响应结果
      var responseText = xmlhttp.responseText;
      alert(responseText);
    }
  }
}
```



### JQuery 方式

##### $.ajax()

语法：$.ajax({键值对});		--> 通用处理

```javascript
function fun() {
  $.ajax({
    url:"ajaxServlet",  //请求路径
    type:"POST",        //请求方式
    //请求参数，不建议：data:"username=jack&age=23"
    data:{"username" : "jack", "age" : 23},
    success:function (data) {   //响应成功后的回调函数
      alert(data);
    },
    error:function () { //请求出错则执行的回调函数
      alert("出错啦 ！！！");
    },
    dataType:"text"     //响应数据格式
  });
}
```



##### $.get()

语法：$.get(url, [data], [callback], [type])	--> 处理 get 请求

```javascript
function fun() {
  $.get("ajaxServlet", {"username" : "jack"}, function (data) {
    alert(data);
  }, "text");
}
```



##### $.post()

语法：$.get(url, [data], [callback], [type])	--> 处理 post 请求，代码与 get 类似仅方法名不同



# JSON

概念：JavaScript Object Notation，JavaScript 对象表示法，常用于存储和叫交换文本信息，进行数据的传输，比 XML 更小、更快、更易于解析



### 语法

##### 基本规则

​	1.数据在 名称/值 对中，json 数据是由键值对构成的，键用引号（单双均可）引起来，也可不使用引号

​		值的取值范围：

​			① 数字（整数或浮点数）

​			② 字符串（双引号中）

​			③ 逻辑值（true 或 false）

​			④ 数组（方括号中）{ "persons" : [ { } , { } ] }

​			⑤ 对象（花括号中）{ "address" : { "province" : "陕西" } }

​			⑥ null

​	2.数据由逗号分隔

​	3.花括号保存对象，使用 { } 定义 json 格式

​	4.方括号保存数组 [ ]

​	示例：

```javascript
//1.定义基本格式
var person = {"name":"张三", age:23, 'gender':true};

//2.嵌套格式 {} --> []
var persons = {
  "person" : [
    {"name":"张三", "age":23, "gender":true},
    {"name":"李四", "age":24, "gender":true},
    {"name":"王五", "age":25, "gender":false}
  ]
};

//3.嵌套格式 [] --> {}
var ps = [
  {"name":"张三", "age":23, "gender":true},
  {"name":"李四", "age":24, "gender":true},
  {"name":"王五", "age":25, "gender":false}
];
```



##### 获取数据

​	1.json对象.键名

​	2.json对象["键名"]

​	3.数组对象[索引]

​	示例：

```javascript
var name = person.name;
var name1 = person["name"];
var name2 = persons.persons[2].name;
var name3 = ps[1].name;

for (var key in person) {
	alert(key + " : " + person[key]);
}

for (var i=0; i<ps.length; i++) {
	var p = ps[i];
	for (var key in p) {
		alert(key + " : " + p[key]);
	}
}
```



### 对象转换

常见解析器：Jsonlib、Gson、Fastjson、Jackson

步骤：

​	1.导入 Jackson 相关 jar 包

​	2.创建 Jackson 核心对象 ObjectMapper

​	3.调用 ObjectMapper 相关方法进行转换



##### JSON 转 Java 对象

方法：readValue(json字符串, class)

示例：

```java
String value = "{\"name\":\"张三\", \"age\":23, \"gender\":true}";
Person person = mapper.readValue(value, Person.class);
System.out.println(person);
```



##### Java 对象转 JSON

转换方法：

​	1.writeValue(参数1, obj)：

​		参数1：

​			File：保存到指定文件

​			Writer：将 json 数据填充到字符输出流

​			OutputStream：将 json 数据填充到字节输出流

​	2.writeValueAsString(obj)：将对象转为 json 字符串

​	示例：

```java
Person p = new Person();
p.setName("张三");
p.setAge(23);
p.setGender("男");

ObjectMapper mapper = new ObjectMapper();

String json = mapper.writeValueAsString(p);
System.out.println(json);

mapper.writeValue(new File("d://a.txt"), p);

mapper.writeValue(new FileWriter("d://b.txt"), p);
```



注解：（加载实体类的属性上）

​	1.@JsonIgnore：排除属性

​	2.@JsonFormat：属性值的格式化

​	示例：

```java
public class Person {
    private String name;
    private int age;
    private String gender;
    
    //@JsonIgnore
    @JsonFormat(pattern = "yyyy-MM-dd")
    private Date birthday;
    
    ...
}
```



复杂 java 对象转换：

​	1.List：转换为数组 [ { }, { } ... ]

​	2.Map：转换为对象格式 { "xxx":"xxx", ... }



### 案例

案例：校验用户名

注意：服务器响应数据时，在客户端想要当作 json 数据使用，需要：

​	1.$.get(type)：最后一个参数 type 指定为 "json"

​	2.在服务器设置 MIME 格式 response.setContentType("application/json;charset=utf-8");



register.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

    <script src="js/jquery-3.3.1.min.js"></script>

    <script>
      $(function () {
        $("#username").blur(function () {
          var username = $(this).val();
          /*
            发送 Ajax 请求，期望数据：
              {"userExist":true, "msg":"此用户名太受欢迎，请更换一个"}
              {"userExist":false, "msg":"用户名可用"}
           */
          $.get("findUserServlet", {username:username}, function (data) {
            var span = $("#s_username");
            if (data.userExist) {
              span.css("color", "red");
              span.html(data.msg);
            } else {
              span.css("color", "green");
              span.html(data.msg);
            }
          }, "json");
        });
      });
    </script>

</head>
<body>

  <form method="post">
    <input type="text" name="username" id="username" /> <br/>
    <span id="s_username"></span> <br/>
    <input type="password" name="password" /> <br/>
    <input type="submit" value="注册" />
  </form>

</body>
</html>
```



FindUserServet

```java
package cn.itcast.web.servlet;

import com.fasterxml.jackson.databind.ObjectMapper;

import javax.servlet.*;
import javax.servlet.http.*;
import javax.servlet.annotation.*;
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;

@WebServlet(name = "findUserServlet", value = "/findUserServlet")
public class FindUserServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request, response);
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //设置响应数据格式为 json
        response.setContentType("application/json;charset=utf-8");

        String username = request.getParameter("username");
        Map<String, Object> map = new HashMap<String, Object>();

        if ("tom".equals(username)) {
            map.put("userExist", true);
            map.put("msg", "此用户名太受欢迎，请更换一个");
        } else {
            map.put("userExist", false);
            map.put("msg", "用户名可用");
        }

        ObjectMapper mapper = new ObjectMapper();
        mapper.writeValue(response.getWriter(), map);
    }
}
```



# Redis

概念：NOSQL 非关系型数据库，常用于缓存数据提高访问速度

文件结构：

​	redis.windows.conf：配置文件

​	redis-server.exe：服务端

​	redis-cli.exe：客户端



### 命令操作

##### 数据结构

​	redis 存储的是 key value 格式数据，key 都是字符串，value 有五种不同数据结构

​	value 数据结构：

​		1.字符串类型 string

​		2.哈希类型 hash :  map 格式

​		3.列表类型 list :  linkedlist 格式，支持重复元素

​		4.集合类型 set :  不允许重复元素

​		5.有序集合类型 sortedset :  不允许重复，且元素有序



##### 字符串类型

​	存储：set key value	 --> set username zhangsan

​	获取：get key				--> get username

​	删除：del key				--> del username



##### 哈希类型

​	存储：

​		hset key field value	--> hset myhash username lisi

​	获取：

​		① hget key field		--> hget myhash username

​		② hgetall key			 --> hgetall myhash

​	删除：

​		hdel key field	  --> hdel myhash username



##### 列表类型

​	概念：可以添加一个元素到列表头或尾部

​	添加：

​		① lpush key value	--> lpush myList a（添加到左边）

​		② rpush key value	--> rpush myList b（添加到右边）

​	获取：

​		lrange key start end	--> lrange myList 0 -1（范围获取，end 的 -1 为全部）

​	删除：

​		① lpop key：删除最左边元素并返回

​		② rpop key：删除最右边元素并返回



##### 集合类型

​	存储：sadd key value	--> sadd mySet a / sadd mySet a b c

​	获取：smembers key	--> smembers mySet（获取所有元素）

​	删除：srem key value	--> srem mySet a



##### 有序集合类型

​	概念：不允许重复且有序，每个元素都会关联一个 double 类型分数，根据分数排序

​	存储：zadd key score value	--> zadd mySort 60 zhangsan

​	获取：zrange key start end [withscores]	--> zrange mySort 0 -1 withscores

​	删除：zrem key value	--> zrem mySort lisi



##### 通用命令

​	keys *		--> 查询所有键

​	type key	--> 获取值对应的 value 类型

​	del key	  --> 删除指定的 key



### 持久化

​	redis 是一个内存数据库，当 redis 服务器重启，获取电脑重启，数据会丢失，我们可以将 redis 内存中的数据持久化保存到硬盘的文件中。



##### RDB

​	默认方式，不需要进行配置，默认就使用这种机制，在一定的间隔时间中，检测 key 的变化情况，然后持久化数据。

​	1.编辑redis.windwos.conf文件

​			#after 900 sec (15 min) if at least 1 key changed

​			save 900 1

​			#after 300 sec (5 min) if at least 10 keys changed

​			save 300 10

​			#after 60 sec if at least 10000 keys changed

​			save 60 10000

​	2.cmd 重新启动 redis 服务器，并指定配置文件名称	--> redis-server.exe redis.windows.conf	



##### AOF

​	日志记录方式，可以记录每一条命令的操作。可以每一次命令操作后，持久化数据，对性能影响较大。

​	1.编辑 redis.windows.conf 文件中 appendonly yes （开启 aof）

​		#appendfsync always  ： 每一次操作都进行持久化

​		appendfsync everysec ： 每隔一秒进行一次持久化

​		#appendfsync no		  ： 不进行持久化

​	2.cmd 重新启动 redis 服务器，并指定配置文件名称	--> redis-server.exe redis.windows.conf



### Java 客户端 Jedis

##### 使用步骤

```java
Jedis jedis = new Jedis();     		//如果空参，则默认 ("localhost", 6379)
jedis.set("username", "zhangsan");	//数据操作
jedis.close();						//关闭连接
```



##### 字符串类型

```java
jedis.set("username", "zhangsan");			//set
String zhangsan = jedis.get("zhangsan");	//get

//将 activecode：a2KsBC 键值对存入 redis ，并且 20 秒后自动删除该键值对
jedis.setex("activecode", 20, "a2KsBC");
```



##### 哈希类型

```java
//存储 hash
jedis.hset("user", "name", "lisi");
jedis.hset("user", "age", "23");

//获取hash
String name = jedis.hget("user", "name");
System.out.println(name);

//获取 hash 中所有 map 数据
Map<String, String> user = jedis.hgetAll("user");
Set<String> keySet = user.keySet();     //获取 keySet
for (String key : keySet) {
    String value = user.get(key);
    System.out.println(value);
}
```



##### 列表类型

```java
//list 存储
jedis.lpush("myList", "a", "b", "c");
jedis.rpush("myList", "a", "b", "c");

//list 范围获取
List<String> myList = jedis.lrange("myList", 0, -1);
System.out.println(myList);

//list 弹出
String element1 = jedis.lpop("myList");
String element2 = jedis.rpop("myList");
System.out.println(element1);
System.out.println(element2);
```



##### 集合类型

```java
//set 存储
jedis.sadd("mySet", "java", "php", "c++");

//set 获取
Set<String> mySet = jedis.smembers("mySet");
System.out.println(mySet);
```



##### 有序集合类型

```java
//sortedSet 存储
jedis.zadd("mySortedSet", 3, "亚瑟");
jedis.zadd("mySortedSet", 30, "梅林");
jedis.zadd("mySortedSet", 22, "兰斯洛特");

//sortedSet 获取
Set<String> mySortedSet = jedis.zrange("mySortedSet", 0, -1);
System.out.println(mySortedSet);
```



### Jedis 连接池

##### 连接池使用

```java
//创建配置对象
JedisPoolConfig config = new JedisPoolConfig();
config.setMaxTotal(50);     //最大连接数
config.setMaxIdle(10);      //最大空闲数

//创建 Jedis 连接池对象
JedisPool jedisPool = new JedisPool(config, "localhost", 6379);

//获取连接
Jedis jedis = jedisPool.getResource();

//使用
jedis.set("haha", "hehe");

//归还连接
jedis.close();
```



##### 连接池工具类

```java
import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;

import java.io.IOException;
import java.io.InputStream;
import java.util.Properties;

public class JedisPoolUtils {

    private static JedisPool jedisPool;

    static {
        //读取配置文件
        InputStream is = JedisPoolUtils.class.getClassLoader().getResourceAsStream("jedis.properties");
        Properties pro = new Properties();
        try {
            pro.load(is);
        } catch (IOException e) {
            e.printStackTrace();
        }
        //获取配置，设置到 JedisPoolConfig 中
        JedisPoolConfig config = new JedisPoolConfig();
        config.setMaxTotal(Integer.parseInt(pro.getProperty("maxTotal")));
        config.setMaxIdle(Integer.parseInt(pro.getProperty("maxIdle")));
        //初始化 JedisPool
        jedisPool = new JedisPool(config, pro.getProperty("host"), Integer.parseInt(pro.getProperty("port")));
    }

    public static Jedis getJedis() {
        return jedisPool.getResource();
    }
}
```



### 案例

ProvinceDaoImpl

```java
public class ProvinceDaoImpl implements ProvinceDao {

    private JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());

    @Override
    public List<Province> findAll() {
        String sql = "select * from province";
        List<Province> list = template.query(sql, new BeanPropertyRowMapper<Province>(Province.class));
        return list;
    }

}
```



ProvinceServiceImpl

```java
public class ProvinceServiceImpl implements ProvinceService {
    private ProvinceDao dao = new ProvinceDaoImpl();

    @Override
    public List<Province> findAll() {
        return dao.findAll();
    }

    //使用 redis 缓存
    @Override
    public String findAllJson() {
        Jedis jedis = JedisPoolUtils.getJedis();
        String province_json = jedis.get("province");

        if (null == province_json || province_json.length() == 0) {
            System.out.println("redis 中没有数据，查询数据库 ...");
            List<Province> ps = dao.findAll();

            //将list 序列化为 json
            ObjectMapper mapper = new ObjectMapper();
            try {
                province_json = mapper.writeValueAsString(ps);
            } catch (JsonProcessingException e) {
                e.printStackTrace();
            }

            //json 数据存入 redis
            jedis.set("province", province_json);
            jedis.close();
        }

        return province_json;
    }

}
```



ProvinceServlet

```java
@Override
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    /*ProvinceService service = new ProvinceServiceImpl();
    List<Province> list = service.findAll();
    //序列化 list 为 json
    ObjectMapper mapper = new ObjectMapper();
    String json = mapper.writeValueAsString(list);*/

    ProvinceService service = new ProvinceServiceImpl();
    String json = service.findAllJson();

    //响应结果
    response.setContentType("application/json;charset=utf-8");
    response.getWriter().write(json);
}
```



index.jsp

```jsp
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <title>JSP - Hello World</title>

    <script src="js/jquery-3.3.1.min.js"></script>

    <script>
        $(function () {
            $.get("provinceServlet", {}, function (data) {
                var province = $("#province");
                $(data).each(function () {
                    var option = "<option name='" + this.id + "'>" + this.name + "</option>"
                    province.append(option);
                });
            });
        });
    </script>

</head>

<body>

    <select id="province">
        <option>-- 请选择省份 --</option>
    </select>

</body>
</html>
```



# Maven

### 小记

仓库分类：本地仓库、远程仓库（私服）、中央仓库

修改仓库位置：Maven目录/conf/settings.xml 中 <localRepository> 项

IDEA 集成 Maven：

​	① settings 中配置 Maven 目录、配置文件、仓库

​	② Runner 中 VM Options 添加 -DarchetypeCatalog=internal（jar 包优先搜索本地仓库）

指定 Web 资源包：File -> Project Structure -> Modules -> 项目下的 Web 中可调整

设置 jar 包仅在编译时起作用： pom.xml 中 <dependency> 中设置 <scope>provided</scope>

自定义代码模板：settings 中搜索 live

添加 jar 包：maven 中央仓库网站搜索 -> 粘贴 dependency 进 pom.xml -> 点击出现的 m 字母刷新



### 配置

1.新建环境变量 MAVEN_HOME，指定到 Maven 根目录

2.Path 里添加 %MAVEN_HOME%\bin\

注意：Maven 环境变量依赖 JAVA_HOME



### 概念

##### Maven 目录结构

​	src/main/java				核心代码

​	src/main/resources	  配置文件

​	src/test/java				   测试代码

​	src/test/resources		测试配置文件

​	src/main/webapp		 网站页面资源



##### 常用命令

​	mvn clean		 删除编译文件（target 文件夹）

​	mvn compile	编译项目

​	mvn test			编译包含测试代码

​	mvn package	打包项目，编译所有代码并放入 target 文件夹，pom.xml 中 <packaging> 指定后缀

​	mvn install		完成以上所有操作，并安装进本地仓库

​	mvn deploy	   完成以上所有操作，并发布项目

​	注意：正好对应 Maven 生命周期中的 编译、测试、打包、安装、发布



##### Maven 概念模型

1.pom.xml（项目对象模型）中包含：

​	项目自身信息（modelVersion、groupId、artifactId、version、packaging、name 等）

​	项目运行所依赖的 jar 包信息（dependencies）

​	项目运行环境信息，如 jdk，tomcat 信息（build）



2.依赖管理模型（dependency）中包含：

​	公司组织名称（groupId）

​	项目名（artifactId）

​	版本号（version）



3.构建模型中包含默认生命周期，每一个构建项目的命令对应 Maven 底层的一个插件



### Maven 项目

##### 创建 Java 工程

​	1.Maven 项目勾选 Create from archetype，下方选择 quickstart

​	2.修改 GroupId、ArtifactId 等信息

​	注意：一般 Java 项目不使用模板



##### 创建 Web 工程

​	1.Maven 项目勾选 Create from archetype，下方选择 webapp

​	2.修改 GroupId、ArtifactId 等信息



### 依赖范围

| 依赖范围 | 对编译 classpath 有效 | 对测试 classpath 有效 | 对运行 classpath 有效 | 例子                        |
| -------- | :-------------------: | :-------------------: | :-------------------: | :-------------------------- |
| compile  |           Y           |           Y           |           Y           | spring-code                 |
| test     |           -           |           Y           |           -           | Junit                       |
| provided |           Y           |           Y           |           -           | servlet-api                 |
| runtime  |           -           |           Y           |           Y           | JDBC 驱动                   |
| system   |           Y           |           Y           |           -           | 本地的，Maven仓库之外的类库 |



