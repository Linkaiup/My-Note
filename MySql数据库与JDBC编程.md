###                                  MySql数据库与JDBC编程

###                                        一、SQL语法

1. SQL语句基础

   1. 查询语句：select，功能最丰富的语句
   2. DML语句(数据操作)：insert、update、delete
   3. DDL语句(数据定义)：creat、alter(修改表结构)、drop、truncate
   4. DCL语句(数据控制)：grant、revoke
   5. 事务控制语句：commit、rollback、savepoint

2. DDL语句语法

3. DML语句语法

   ​

   ###                             二、JDBC典型用法

   ### 			三、执行SQL语句的方式

   - 使用Java8新增的executeLargeUpdate方法执行DDL和DML语句

   - 使用execute方法执行SQL语句

   - PreparedStatement执行SQL语句

   - CallableStatement调用存储过程

     ### 		   	  四、管理结果集

   - 可滚动、可更新的结果集

     ```java
     PreparedStatement ps=conn.prepareStatement(sql,ResultSet.TYPE_SCROLL_INSENSITIVE,ResultSet.CONCUR_UPDATABLE);
     ResultSet rs=ps.executeQuery();
     rs.last();
     rs.updateString(2,"xxx"+8);
     rs.updateRow();
     ```

   - 处理Blob类型数据

   - ResultSetMetaDate分析结果集

     ### 			五、Java7的RowSet1.1

   - 可以离线修改数据之后再传送上connection中

   - 可以查询分页

     ### 		        六、使用连接池管理连接

   - DBCP

     ```java
     //创建数据源对象
     BasicDataSource ds=new BasicDataSource();
     //设置连接池所需的驱动
     ds.setDriverClassName("com.mysql.jdbc.Driver");
     //设置数据库的URl
     ds.setUrl("jdbc:mysql://locathost:3306/javaee");
     //设置连接数据库的用户名
     ds.setUsername("root");
     //设置密码
     ds.setPassword("xx");
     //设置连接池初始连接数
     ds.setInitialSize(5);

     ds.setMaxActive(20);

     ds.setMinIdle(2);

     //通过数据源获取数据库连接
     Connection conn=ds.getConnection();

     //用完后记得关掉
     conn.close();
       
     ```

   - C3P0

     ```java
     //C3P0创建连接池实例
     ComboPooledDataSource ds= new CoboPooledDataSource();
     //下面与上面的都相同了
     ```

     - C3P0xml配置实例

       ```xml
       <?xml version="1.0"encoding="UTF-8"?>
       <c3p0-config>
           <default-config>
              <property name="automaticTestTable">con_test</property>
              <property name="checkoutTimeout">30000</property>
              <property name="idleConnectionTestPeriod">30</property>
              <property name="initialPoolSize">10</property>
              <property name="maxIdleTime">30</property>
              <property name="maxPoolSize">100</property>
              <property name="minPoolSize">10</property>
              <property name="maxStatements">200</property>
              <user-overrides user="test-user">
                  <property name="maxPoolSize">10</property>
                  <property name="minPoolSize">1</property>
                  <property name="maxStatements">0</property>
              </user-overrides>
           </default-config>
           <named-config name="toto">
              <property name="initialPoolSize">5</property>
              <property name="maxPoolSize">20</property>
              <property name="driverClass">com.mysql.jdbc.Driver</property>
              <property name="jdbcUrl">jdbc:mysql://localhost:3306/day14</property>
              <property name="user">root</property>
              <property name="password">123456</property>
           </named-config>
       </c3p0-config>
       ```

     - c3p0的JdbcUtil.java

       ```java
       package cn.toto.utils;
        
       import java.sql.Connection;
       import java.sql.ResultSet;
       import java.sql.SQLException;
       import java.sql.Statement;
        
       import javax.sql.DataSource;
        
       import com.mchange.v2.c3p0.ComboPooledDataSource;
        
       public class JdbcUtils {
           private static DataSourceds;
          
           static {
              //ds = new ComboPooledDataSource();//默认的缺省的配置
              ds = new ComboPooledDataSource("toto");//配置文件中设置的内容不用写其他的是因为它可以自动查找classloader下的c3p0-config.xml文件（要注意的是文件的名称必须是c3p0-config.xml）如果文件的名称不是这个，将出现错误。
           }
          
           //获取与指定数据的连接
           public static DataSourcegetSource(){
              return ds;
           }
          
           //获得与指定数据库的连接
           public static Connection getConnection() throws SQLException {
              //从连接池返回一个连接
              return ds.getConnection();
           }
           //释放资源
           public static void release(ResultSet rs,Statement stmt,Connection conn) {
              if(rs!=null){
                  try{
                     rs.close();
                  }catch(SQLException e){
                     e.printStackTrace();
                  }
                  rs = null;
              }
              if(stmt!=null){
                  try{
                     stmt.close();
                  }catch(SQLException e){
                     e.printStackTrace();
                  }
                  stmt=null;
              }
              if(conn!=null){
                  try{
                     conn.close();
                  }catch(SQLException e){
                     e.printStackTrace();
                  }
                  conn = null;
              }
           }
        
       }
       ```