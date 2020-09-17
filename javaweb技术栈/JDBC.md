# JDBC

## JDBC查询与更新

`Connection`通过写在try后面来**避免没有关闭**

```java
import java.sql.*;
import java.util.Arrays
    ties;

public class Main {
    public static void main(String[] args) throws SQLException {
        String JDBC_URL = "jdbc:mysql://localhost:3306/learnjdbc?useSSL=FALSE&serverTimezone=UTC";
        String JDBC_USER = "root";
        String JDBC_PASSWORD = "oxxPSWD123";

        ConnectionTest connectionTest = new ConnectionTest();
        //connectionTest.setMsg(JDBC_URL, JDBC_USER, JDBC_PASSWORD);//添加
        //connectionTest.delMsg(JDBC_URL, JDBC_USER, JDBC_PASSWORD);
        connectionTest.updateMsg(JDBC_URL, JDBC_USER, JDBC_PASSWORD);//更新
        System.out.println();
        connectionTest.selectMsg1(JDBC_URL, JDBC_USER, JDBC_PASSWORD);//查询
        System.out.println();
        connectionTest.selectMsg2(JDBC_URL, JDBC_USER, JDBC_PASSWORD);//查询
    }
}
```

```java
import java.io.Serializable;
import java.sql.*;
import java.util.ArrayList;
import java.util.List;

public class ConnectionTest {
    public void selectMsg1(String JDBC_URL, String JDBC_USER, String JDBC_PASSWORD) throws SQLException {
        String sql = "SELECT id, grade, name, gender ,score FROM student WHERE gender='M'";
        try (Connection conn = DriverManager.getConnection(JDBC_URL, JDBC_USER, JDBC_PASSWORD)) {
            try (Statement stmt = conn.createStatement()) {
                //必须首先调用setObject()设置每个占位符?的值，最后获取的仍然是ResultSet对象。
                try (ResultSet rs = stmt.executeQuery(sql)) {
                    while (rs.next()) {
                        long id = rs.getLong(1); // 注意：索引从1开始
                        long grade = rs.getLong(2);
                        String name = rs.getString(3);
                        String gender = rs.getString(4);
                        long score = rs.getLong(5);
                        System.out.println(id + " " + grade + " " + name + " " + gender + " " + score);
                    }
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }

    public void selectMsg2(String JDBC_URL, String JDBC_USER, String JDBC_PASSWORD) throws SQLException {
        String sql = "SELECT id,grade,name,gender,score From student WHERE gender=? AND grade=?";
        try (Connection conn = DriverManager.getConnection(JDBC_URL, JDBC_USER, JDBC_PASSWORD)) {
            try (PreparedStatement ps = conn.prepareStatement(sql)) {
                ps.setObject(1, 0);
                ps.setObject(2, 1);
                try (ResultSet rs = ps.executeQuery()) {
                    while (rs.next()) {
                        long id = rs.getLong(1); // 注意：索引从1开始
                        long grade = rs.getLong(2);
                        String name = rs.getString(3);
                        String gender = rs.getString(4);
                        long score = rs.getLong(5);
                        System.out.println(id + " " + grade + " " + name + " " + gender + " " + score);
                    }
                }
            }
        }
    }

    public void setMsg(String JDBC_URL, String JDBC_USER, String JDBC_PASSWORD) throws SQLException {
        String sql = "INSERT INTO student ( grade, name, gender,score) VALUES (?,?,?,?)";
        try (Connection conn = DriverManager.getConnection(JDBC_URL, JDBC_USER, JDBC_PASSWORD)) {
            try (PreparedStatement ps = conn.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS)) {
                ps.setObject(1, 1);
                ps.setObject(2, "XLB");
                ps.setObject(3, "0");
                ps.setObject(4, 78);
                int n = ps.executeUpdate();
                try (ResultSet resultSet = ps.getGeneratedKeys()) {
                    if (resultSet.next()) {
                        long id = resultSet.getLong(1);
                    }
                }
            }
        }
    }

    public void delMsg(String JDBC_URL, String JDBC_USER, String JDBC_PASSWORD) throws SQLException {
        String sql = "DELETE FROM student WHERE id=?";
        try (Connection conn = DriverManager.getConnection(JDBC_URL, JDBC_USER, JDBC_PASSWORD)) {
            try (PreparedStatement ps = conn.prepareStatement(sql)) {
                ps.setObject(1, 8);
                ps.executeUpdate();
            }
        }
    }

    public void updateMsg(String JDBC_URL, String JDBC_USER, String JDBC_PASSWORD) throws SQLException{
        String sql="UPDATE student SET name=?,score=? WHERE id=?";
        try(Connection connection =DriverManager.getConnection( JDBC_URL, JDBC_USER,JDBC_PASSWORD)) {
            try (PreparedStatement ps = connection.prepareStatement(sql)){
                ps.setObject(1,"Jobs");
                ps.setObject(2,94);
                ps.setObject(3,9);
                ps.executeUpdate();
            }
        }
    }
}
```

`prepareStatement`不仅可以解决拼接字符串问题，还能操作Blob的数据（（binary large object)----二进制大对象，是一个可以存储二进制文件的容器）比如用数据库存图片。

先把文件，图片转换成流的形式

```java
FileInputStream is =new FileInputStream(new File("pathname"));
ps.setBlob(1,is);
```

### jdbc事务

数据库事务（Transaction）具有ACID特性：

- Atomicity：原子性
- Consistency：一致性
- Isolation：隔离性
- Durability：持久性

JDBC提供了事务的支持，使用Connection可以开启、提交或回滚事务。

默认情况下，我们获取到`Connection`连接后，总是处于“自动提交”模式，也就是每执行一条SQL都是作为事务自动执行的

```java
Connection conn = openConnection();
try {
    // 关闭自动提交:
    conn.setAutoCommit(false);
    // 执行多条SQL语句:
    insert(); update(); delete();
    // 提交事务:
    conn.commit();
} catch (SQLException e) {
    // 回滚事务:
    conn.rollback();
} finally {
    conn.setAutoCommit(true);
    conn.close();
}
```

### jdbc BATCH

使用JDBC的batch操作会大大提高执行效率，对内容相同，参数不同的SQL，要优先考虑batch操作

+ 添加到batch：Statement.addBatch();
+ ；执行批处理SQL语句：Statement.executeBatch();
+ 清除批处理命令：Statement.clearBatch();

```java
    public void setMsg2(String JDBC_URL, String JDBC_USER, String JDBC_PASSWORD) throws SQLException {
        String sql = "INSERT INTO student ( grade, name, gender,score) VALUES (?,?,?,?)";
        
        try (Connection conn = DriverManager.getConnection(
            JDBC_URL, JDBC_USER, JDBC_PASSWORD)) {
            try (PreparedStatement ps = conn.prepareStatement(
                sql, Statement.RETURN_GENERATED_KEYS)) {
                for (String name : names) {
                    ps.setString(1, name);
                    ps.setBoolean(2, gender);
                    ps.setInt(3, grade);
                    ps.setInt(4, score);
                    ps.addBatch(); // 添加到batch
                }
                int[] ns = ps.executeBatch();// 执行batch
                try (ResultSet resultSet = ps.getGeneratedKeys()) {
                    if (resultSet.next()) {
                        long id = resultSet.getLong(1);
                    }
                }
            }
        }
    }
```
### 数据库连接池

如果每一次操作都来一次打开连接，操作，关闭连接，那么创建和销毁JDBC连接的开销就太大了。为了避免频繁地创建和销毁JDBC连接，我们可以通过连接池（Connection Pool）复用已经创建好的连接。

#### HikariCP案例

```java
import com.zaxxer.hikari.HikariConfig;
import com.zaxxer.hikari.HikariDataSource;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class DataSource {
    public static void main(String[] args) {
        HikariConfig config = new HikariConfig();
        config.setJdbcUrl("jdbc:mysql://localhost:3306/learnjdbc?useSSL=FALSE&serverTimezone=UTC");
        config.setUsername("root");
        config.setPassword("oxxPSWD123");
        config.addDataSourceProperty("connectionTimeout", "1000");// 连接超时：1秒
        config.addDataSourceProperty("idleTimeout", "60000"); // 空闲超时：60秒
        config.addDataSourceProperty("maximumPoolSize", "10"); // 最大连接数：10
        javax.sql.DataSource dataSource = new HikariDataSource(config);
        String sql = "SELECT id,grade,name,gender,score From student WHERE id<?";

        try (Connection conn = dataSource.getConnection()) {
            try (PreparedStatement ps = conn.prepareStatement(sql)) {
                ps.setObject(1, 6);
                try (ResultSet rs = ps.executeQuery()) {
                    while (rs.next()) {
                        long id = rs.getLong(1); // 注意：索引从1开始
                        long grade = rs.getLong(2);
                        String name = rs.getString(3);
                        String gender = rs.getString(4);
                        long score = rs.getLong(5);
                        System.out.println(id + " " + grade + " " + name + " " + gender + " " + score);
                    }
                }
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}

```

### 搭建Dao持久层

#### 编写工具类jdbcUtils



