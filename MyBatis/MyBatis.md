# MyBatis

## 1 简介

### 1.1 什么是MyBatis

- MyBatis 是一款优秀的**持久层框架**
- 它支持自定义 SQL、存储过程以及高级映射。
- MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。
- MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

Maven仓库

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.6</version>
</dependency>
```

### 1.2 持久化

数据持久化

* 持久化就是将程序在持久状态和瞬时状态转化的过程
* 内存：**断电即失**
* 数据库、io文件持久化

为什么需要持久化？——有些对象不能丢失

### 1.3 持久层

dao层、service层、controller层

* 完成持久化工作的代码块
* 层界限十分明显

### 1.4 为什么要用MyBatis

* 方便
* 传统JDBC过于复杂
* 方便coder将数据存入DB中

## 2 MyBatis程序

搭建环境->导入MyBatis->编写代码->测试

### 2.1 搭建环境

数据库脚本

```sql
create database mybatis_learn;

use mybatis_learn;

CREATE TABLE IF NOT EXISTS `user` (
    `id` int unsigned AUTO_INCREMENT COMMENT 'ID',
    `name` nvarchar (20) COMMENT '账号',
    `pwd` nvarchar (20) COMMENT '密码',
PRIMARY KEY (`id`)) engine = innodb DEFAULT charset = utf8 AUTO_INCREMENT = 1 COMMENT '用户表';

SELECT
    *
FROM
    mybatis_learn.user;

INSERT INTO user(`name`, `pwd`)
        VALUES('test01', '1234'), ('test02', '1234'), ('test03', '1234');
```

`pom.xml`

```xml
</dependency>
  <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
  <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.22</version>
  </dependency>

  <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
  <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.6</version>
  </dependency>

  <!-- https://mvnrepository.com/artifact/junit/junit -->
  <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.13.1</version>
      <scope>test</scope>
  </dependency>
</dependencies>
```

`mybatis-config.xml`

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <typeAliases>
        <!--  给实体类起一个別名,否则需要用全限定名-->
        <package name="com.xxx.entity"/>
    </typeAliases>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url"
                          value="jdbc:mysql://localhost:3306/xxx?useSSL=false&amp;serverTimezone=Asia/Shanghai&amp;characterEncoding=utf-8&amp;autoReconnect=true"/>
                <property name="username" value="root"/>
                <property name="password" value="1234"/>
            </dataSource>
        </environment>
    </environments>
  
    <mappers>
        <mapper resource="mapper/UserMapper.xml"/>
    </mappers>
</configuration>
```

`MybatisUtil`

```java
public class MybatisUtils {
    public static SqlSessionFactory sqlSessionFactory;

    static {
        try {
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static SqlSession getSqlSession() {
        return sqlSessionFactory.openSession();
    }
}
```

### 2.2 编码

`User`

```java
public class User {
    private int id;
    private String name;
    private String pwd;

  	// 构造方法、get、set、toString
}
```

`UserDao`

```
public interface UserDao {
    List<User> getAll();
}
```

### 2.3 测试

`UserMapper.xml`

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--suppress ALL -->
<mapper namespace="com.dennis.dao.UserDao">
    <select id="getAll" resultType="com.dennis.entity.User">
        select *
        from mybatis_learn.user;
    </select>
</mapper>
```

`UserDaoTest`

```java
public class UserDaoTest {
    @Test
    public void test01() {
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserDao mapper = sqlSession.getMapper(UserDao.class);
        List<User> list = mapper.getAll();
        for (User user : list) {
            System.out.println(user);
        }
    }
}
```

## 3 CRUD

mapper中namespace需要和dao中接口的包名一致

* id：对应的dao中的方法名
* resultType：查询结果返回值类型
* parameterType：方法传入参数类型

### 3.1 SELECT

```xml
<select id="getById" resultType="com.dennis.entity.User">
    select *
    from mybatis_learn.user
    where id = #{id}
</select>

SqlSession sqlSession = MybatisUtils.getSqlSession();
UserDao mapper = sqlSession.getMapper(UserDao.class);
User user = mapper.getById(1);
System.out.println("user = " + user);
```

### 3.2 INSERT

```xml
<insert id="addUser" parameterType="com.dennis.entity.User">
    INSERT INTO mybatis_learn.user(`name`, `pwd`)
    VALUES (#{name}, #{pwd})
</insert>

SqlSession sqlSession = MybatisUtils.getSqlSession();
UserDao mapper = sqlSession.getMapper(UserDao.class);
User user = new User("test04", "1234");
int i = mapper.addUser(user);
System.out.println("i = " + i);
sqlSession.commit();
```

### 3.3 DELETE

```xml
<delete id="deleteUser" parameterType="int">
    delete
    from mybatis_learn.user
    where id = #{id}
</delete>

SqlSession sqlSession = MybatisUtils.getSqlSession();
UserDao mapper = sqlSession.getMapper(UserDao.class);
User user = new User(5, "test04", "12345");
int i = mapper.updateUser(user);
System.out.println("i = " + i);
sqlSession.commit();
```

### 3.4 UPDATE

```xml
<update id="updateUser" parameterType="com.dennis.entity.User">
    update mybatis_learn.user
    set name = #{name},
        pwd=#{pwd}
    where id = #{id}
</update>

SqlSession sqlSession = MybatisUtils.getSqlSession();
UserDao mapper = sqlSession.getMapper(UserDao.class);
int i = mapper.deleteUser(5);
System.out.println("i = " + i);
sqlSession.commit();
```

### 3.5 MAP

```xml
<insert id="addUserMap" parameterType="map">
    INSERT INTO mybatis_learn.user(`name`, `pwd`)
    VALUES (#{username}, #{password})
</insert>

SqlSession sqlSession = MybatisUtils.getSqlSession();
UserDao mapper = sqlSession.getMapper(UserDao.class);
Map<String, Object> map = new HashMap<>();
map.put("username", "test04");
map.put("password", "1234");
int i = mapper.addUserMap(map);
System.out.println("i = " + i);
sqlSession.commit();
```

### 3.6 模糊查询

```xml
<select id="getByName" resultType="com.dennis.entity.User" parameterType="string">
    select *
    from mybatis_learn.user
    where name like #{name}
</select>

SqlSession sqlSession = MybatisUtils.getSqlSession();
UserDao mapper = sqlSession.getMapper(UserDao.class);
List<User> list = mapper.getByName("%s%");
for (User user : list) {
    System.out.println(user);
}
```

## 4 配置解析

`mybatis-config.xml`属性配置

### 4.1 事务管理器（transactionManager）

在 MyBatis 中有两种类型的事务管理器（也就是 type="[JDBC|MANAGED]"）：

- JDBC – 这个配置直接使用了 JDBC 的提交和回滚设施，它依赖从数据源获得的连接来管理事务作用域。
- MANAGED – 这个配置几乎没做什么。它从不提交或回滚一个连接，而是让容器来管理事务的整个生命周期（比如 JEE 应用服务器的上下文）。

### 4.2 数据源（dataSource、连接池POOLED）

dataSource 元素使用标准的 JDBC 数据源接口来配置 JDBC 连接对象的资源。

- 大多数 MyBatis 应用程序会按示例中的例子来配置数据源。虽然数据源配置是可选的，但如果要启用延迟加载特性，就必须配置数据源。

有三种内建的数据源类型（也就是 type="[UNPOOLED|POOLED|JNDI]"）

### 4.3 属性（properties）

属性可以在外部进行配置，并可以进行动态替换。你既可以在典型的 Java 属性文件中配置这些属性，也可以在 properties 元素的子元素中设置

`db.properties`

```properties
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://121.40.215.18:3306/mybatis_learn?useSSL=false&serverTimezone=Asia/Shanghai&characterEncoding=utf-8&autoReconnect=true
username=root
password=root
```

### 4.4 类型别名（typeAliases）

类型别名可为 Java 类型设置一个缩写名字。 

它仅用于 XML 配置，意在降低冗余的全限定类名书写。

`mybatis-config.xml`

```xml
<typeAliases>
    <package name="com.soft.entity"/>
</typeAliases>
```

```xml
<typeAliases>
    <typeAlias alias="User" type="com.soft.entity.User"/>
</typeAliases>
```

```java
@Alias("author")
public class Author {
    ...
}
```

三种方式：配置包名、类的别名、注解

### 4.5 映射器（mappers）

方式一

```xml
<!-- 使用相对于类路径的资源引用 -->
<mappers>
  <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
  <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
  <mapper resource="org/mybatis/builder/PostMapper.xml"/>
</mappers>
```

方式二

```xml
<!-- 使用映射器接口实现类的完全限定类名 -->
<mappers>
  <mapper class="org.mybatis.builder.AuthorMapper"/>
  <mapper class="org.mybatis.builder.BlogMapper"/>
  <mapper class="org.mybatis.builder.PostMapper"/>
</mappers>
```

方式三

```xml
<!-- 将包内的映射器接口实现全部注册为映射器 -->
<mappers>
  <package name="org.mybatis.builder"/>
</mappers>
```

Tips：使用方式二、方式三接口名需要和配置文件同名且在同一包下

### 4.6 作用域（Scope）和生命周期

不同作用域和生命周期类别是至关重要的，因为错误的使用会导致非常严重的并发问题

SqlSessionFactoryBuilder：局部方法变量，一旦创建了 SqlSessionFactory，就不再需要它了

SqlSessionFactory：SqlSessionFactory 一旦被创建就应该在应用的运行期间一直存在，没有任何理由丢弃它或重新创建另一个实例，使用 SqlSessionFactory 的最佳实践是在应用运行期间不要重复创建多次，因此 SqlSessionFactory 的最佳作用域是应用作用域。

SqlSession：连接到连接池的请求，用完需要关闭，否则资源被占用

## 5 XML映射

### 5.1 resultMap 结果集映射

```
原：id	name	pwd
映射：id	username	password
```

```xml
<resultMap id="UserMap" type="User">
    <result column="id" property="id"></result>
    <result column="name" property="username"></result>
    <result column="pwd" property="password"></result>
</resultMap>

<select id="getAll" resultMap="UserMap">
    select *
    from mybatis_learn.user
</select>
```

ResultMap 的设计思想是，对简单的语句做到零配置，对于复杂一点的语句，只需要描述语句之间的关系就行了。

## 6 日志

### 6.1 日志工厂

如果数据库操作出现异常，日志将是最好的解决方案

Mybatis 通过使用内置的日志工厂提供日志功能。内置日志工厂将会把日志工作委托给下面的实现之一：

- SLF4J
- Apache Commons Logging
- Log4j 2
- Log4j (deprecated since 3.5.9)
- JDK logging

MyBatis 内置日志工厂基于运行时自省机制选择合适的日志工具。它会使用第一个查找得到的工具（按上文列举的顺序查找）。如果一个都未找到，日志功能就会被禁用。

`默认日志工厂`

```xml
<settings>
    <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
```

### 6.2 Log4j

* 通过Log4j可以控制每一条日志的输出格式
* 定义每条日志信息的级别，能够更加细致的控制日志生成过程
* 通过配置文件灵活的配置，不需要修该应用的代码

`pom.xml`

```xml
<!-- https://mvnrepository.com/artifact/log4j/log4j -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

`log4j.properties`

```properties
log4j.rootLogger=DEBUG,console,file
#控制台输出的相关设置
log4j.appender.console=org.apache.log4j.ConsoleAppender
log4j.appender.console.Target=System.out
log4j.appender.console.Threshold=DEBUG
log4j.appender.console.layout=org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=[%c]-%m%n
#文件输出的相关设置
log4j.appender.file=org.apache.log4j.RollingFileAppender
log4j.appender.file.File=./src/log/logs.log
log4j.appender.file.MaxFileSize=10mb
log4j.appender.file.Threshold=DEBUG
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=[%p][%d{yy-MM-dd}][%c]%m%n
#日志输出级别
log4j.logger.org.mybatis=DEBUG
log4j.logger.java.sql=DEBUG
log4j.logger.java.sql.Statement=DEBUG
log4j.logger.java.sql.ResultSet=DEBUG
log4j.logger.java.sql.PreparedStatement=DEBUG
```

`mybatis-config.xml`

```xml
<settings>
    <setting name="logImpl" value="LOG4J"/>
</settings>
```

## 7 分页

### 7.1 limit分页

`dao.java`

```java
public interface PeopleMapper {
    List<People> limit(Map<String, Object> map);
}
```

`mapper.xml`

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.mybatis.DAO.PeopleMapper">
    <select id="limit" parameterType="map" resultType="people">
        select * from mybatis.people limit #{startindex},#{pagesize};
    </select>
</mapper>
```

### 7.2 PageHelper分页插件

`pom.xml`

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>最新版本</version>
</dependency>
```

`mybatis-config.xml`

```xml
<plugins>
    <plugin interceptor="com.github.pagehelper.PageInterceptor">
        <property name="helperDialect" value="mysql"/>
        <property name="rowBoundsWithCount" value="true"/>
        <property name="offsetAsPageNum" value="true"/>
        <property name="reasonable" value="true"/>
        <property name="supportMethodsArguments	" value="true"/>
    </plugin>
</plugins>
```

`ServiceImpl.java`

```java
public PageInfo<Book> listBooks(String searchParam, int page, int size) {
    // set paging start
    PageHelper.startPage(page, size);
    List<Book> list = bookDAO.listBooks(searchParam);
    PageInfo<Book> info = new PageInfo<>(list);
    return info;
}
```

## 8 使用注解开发

### 8.1 面向接口编程

解耦合，可拓展，提高复用，分层开发。

在一个面向对象的系统中，系统的各个功能是由许多不同对象协作完成的，在这种情况下，各个对象内部是如何实现的对于系统设计人员来说不那么重要。

各个对象之间的协作关系成为系统设计的关键，小到不同类之间的通信，大到各模块之间的交互，在系统设计之初都是要着重考虑的，这也是系统设计的主要工作内容

### 8.2 使用注解

`mybatis-config.xml`

```xml
<mappers>
    <mapper class="com.xxx.dao.xxxDao"/>
</mappers>
```

`dao.java`

```java
public interface BlogMapper {
		@Select("SELECT * FROM blog WHERE id = #{id}")
		Blog selectBlog(int id);
}
```

本质：反射机制实现

底层：动态代理

### 8.3 Mybatis执行流程

1. Resource 获取加载全局配置文件
2. 实例化SqlSessionFactoryBuilder构造器
3. 解析配置文件流XMLConfigBuilder
4. 返回Configuration所以配置信息
5. 实例化SqlSessionFactory对象
6. transaction事务管理器
7. 创建executor执行器
8. 创建SqlSession
9. 实现CRUD

### 8.4 CRUD注解

```java
// 自动提交
public static SqlSession getSqlSession() {
    return sqlSessionFactory.openSession(true);
}
```

@Param注解

* 基本数据类型的参数或String类型，需要加
* 引用数据类型不需要加
* 只有一个基本数据类型可以忽略
* 在SQL中引用的就是@Param中设定的属性名

## 9 多表联查

```sql
CREATE TABLE IF NOT EXISTS `teacher` (
    `id` int unsigned AUTO_INCREMENT,
    `name` VARCHAR(30) DEFAULT NULL,
    PRIMARY KEY (`id`)) ENGINE = INNODB DEFAULT CHARSET = utf8 AUTO_INCREMENT = 1;    
INSERT INTO teacher (`id`, `name`)
        VALUES(1, '王老师');
        
CREATE TABLE `student` (
    `id` int unsigned AUTO_INCREMENT,
    `name` VARCHAR(30) DEFAULT NULL,
    `tid` int unsigned DEFAULT NULL,
    PRIMARY KEY (`id`)) ENGINE = INNODB DEFAULT CHARSET = utf8 AUTO_INCREMENT = 1;
INSERT INTO `student` (`id`, `name`, `tid`)
        VALUES('1', '小明', '1');
INSERT INTO `student` (`id`, `name`, `tid`)
        VALUES('2', '小红', '1');
INSERT INTO `student` (`id`, `name`, `tid`)
        VALUES('3', '小张', '1');
INSERT INTO `student` (`id`, `name`, `tid`)
        VALUES('4', '小李', '1');
INSERT INTO `student` (`id`, `name`, `tid`)
        VALUES('5', '小王', '1');
```

在ResultMap中，对象：associate 集合：collection

### 9.1 多对一关系

方式一：按查询嵌套查询

```xml
<mapper namespace="com.xxx.dao.StudentDao">
    <resultMap id="stuMap" type="student">
        <result property="id" column="id"/>
        <result property="name" column="name"/>
        <!--对象：associate-->
        <!--集合：collection-->
        <association property="tch" column="tid" javaType="teacher"
                     select="getById"/>
    </resultMap>
    <select id="getAll" resultMap="stuMap">
        select *
        from student;
    </select>

    <select id="getById" resultType="teacher">
        select *
        from teacher
        where id = #{tid}
    </select>
</mapper>
```

方式二：按结果嵌套查询

```xml
<mapper namespace="com.xxx.dao.StudentDao">
    <resultMap id="stuMap" type="student">
        <result property="id" column="sid"/>
        <result property="name" column="sname"/>
        <association property="tch" javaType="teacher">
            <result property="id" column="tid"/>
            <result property="name" column="tname"/>
        </association>
    </resultMap>

    <select id="getAll" resultMap="stuMap">
        select s.id sid, s.name sname, t.id tid, t.name tname
        from student s
                 left join teacher t on s.tid = t.id
    </select>
</mapper>
```

### 9.2 一对多关系

```xml
<mapper namespace="com.xxx.dao.TeacherDao">
    <resultMap id="tchMap" type="teacher">
        <result property="id" column="tid"/>
        <result property="name" column="tname"/>
        <collection property="stus" ofType="student">
            <result property="id" column="sid"/>
            <result property="name" column="sname"/>
        </collection>
    </resultMap>
    <select id="getById" resultMap="tchMap">
        select t.id tid, t.name tname, s.id sid, s.name sname
        from teacher t
                 left join student s on t.id = s.tid
        where tid = #{tid}
    </select>
</mapper>
```

## 10 动态SQL

动态SQL：根据不同的条件生成不同的SQL语句

Mybatis提供了以下四种标签帮助开发者构造动态SQL

- if
- choose (when, otherwise)
- trim (where, set)
- foreach

```sql
CREATE TABLE `blog`(
`id` VARCHAR(50) NOT NULL COMMENT '博客id',
`title` VARCHAR(100) NOT NULL COMMENT '博客标题',
`author` VARCHAR(30) NOT NULL COMMENT '博客作者',
`create_time` DATETIME NOT NULL COMMENT '创建时间',
`views` INT(30) NOT NULL COMMENT '浏览量'
)ENGINE=INNODB DEFAULT CHARSET=utf8;
```

Tips：`<setting name="mapUnderscoreToCamelCase" value="true"/>`开启驼峰命名转换

### 10.1 if标签

```xml
<select id="findActiveBlogWithTitleLike" resultType="Blog">
  SELECT * FROM BLOG
  WHERE state = ‘ACTIVE’
  <if test="title != null">
    AND title like #{title}
  </if>
</select>
```

如果不传入 “title”，那么所有处于 “ACTIVE” 状态的 BLOG 都会返回；如果传入了 “title” 参数，那么就会对 “title” 一列进行模糊查找并返回对应的 BLOG 结果

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <if test="title != null">
    AND title like #{title}
  </if>
  <if test="author != null and author.name != null">
    AND author_name like #{author.name}
  </if>
</select>
```

### 10.2 choose标签

不想使用所有的条件，而只是想从多个条件中选择一个使用，类似switch语句

传入了 “title” 就按 “title” 查找，传入了 “author” 就按 “author” 查找的情形。若两者都没有传入，就返回标记为 featured 的 BLOG

```xml
<select id="findActiveBlogLike" resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
</select>
```

### 10.3 trim标签

*where* 元素只会在子元素返回任何内容的情况下才插入 “WHERE” 子句。而且，若子句的开头为 “AND” 或 “OR”，*where* 元素也会将它们去除

```xml
<select id="findActiveBlogLike" resultType="Blog">
  SELECT * FROM BLOG
  <where>
    <if test="state != null">
         state = #{state}
    </if>
    <if test="title != null">
        AND title like #{title}
    </if>
    <if test="author != null and author.name != null">
        AND author_name like #{author.name}
    </if>
  </where>
</select>
```

trim标签一般用于去除sql语句中多余的and关键字，逗号，或者给sql语句前拼接 “where“、“set“以及“values(“ 等前缀，或者添加“)“等后缀，可用于选择性插入、更新、删除或者条件查询等操作。

```xml
<trim prefix="WHERE" prefixOverrides="AND |OR ">
    <if test="state != null">
      state = #{state}
    </if> 
    <if test="title != null">
      AND title like #{title}
    </if>
    <if test="author != null and author.name != null">
      AND author_name like #{author.name}
    </if>
</trim>
```

prefix：前缀　　　　　　

prefixoverride：去掉第一个and或者是or

```xml
<update id="updateAuthorIfNecessary">
  update Author
    <set>
      <if test="username != null">username=#{username},</if>
      <if test="password != null">password=#{password},</if>
      <if test="email != null">email=#{email},</if>
      <if test="bio != null">bio=#{bio}</if>
    </set>
  where id=#{id}
</update>
```

```xml
<update id="updateAuthorIfNecessary">
  update Author
	<trim prefix="SET" suffixOverrides=",">
			<if test="username != null">username=#{username},</if>
      <if test="password != null">password=#{password},</if>
      <if test="email != null">email=#{email},</if>
      <if test="bio != null">bio=#{bio}</if>
	</trim>
  where id=#{id}
</update>
```

suffix：后缀

suffixoverride：去掉最后一个逗号（也可以是其他的标记，就像是上面前缀中的and一样）

### 10.4 SQL片段

某些情况下，一些功能的部分抽取出来，方便复用

1. SQL标签抽取公共的部分
2. 在需要使用的地方使用include标签引用

```xml
<sql id="xxx">
  ...
</sql>

<include refid="xxx"></include>
```

Tips：

* 最好基于单表定义SQL标签
* 不要存在where标签

### 10.5 foreach标签

- **collection:** 需做foreach(遍历)的对象，作为入参时，list、array对象时，collection属性值分别默认用"list"、"array"代替，Map对象没有默认的属性值。但是，在作为入参时可以使用@Param(“keyName”)注解来设置自定义collection属性值，设置keyName后，list、array会失效；
- **item：** 集合元素迭代时的别名称，该参数为必选项；
- **index：** 在list、array中，index为元素的序号索引。但是在Map中，index为遍历元素的key值，该参数为可选项；
- **open：** 遍历集合时的开始符号，通常与close=")"搭配使用。使用场景IN(),values()时，该参数为可选项；
- **separator：** 元素之间的分隔符，类比在IN()的时候，separator=",",最终所有遍历的元素将会以设定的（,）逗号符号隔开，该参数为可选项；
- **close：** 遍历集合时的结束符号，通常与open="("搭配使用，该参数为可选项；

#### 10.5.1 传入List

如果传入的参数类型为List时，collection的默认属性值为list,同样可以使用@Param注解自定义keyName

```java
List<User> getUserInfo(@Param("userName") List<String> userName);
```

```xml
<select id="getUserInfo" resultType="com.test.User">
    SELECT
      *
    FROM user_info
      where
      <if test="userName!= null and userName.size() > 0">
          USERNAME IN
          <foreach collection="userName" item="value" separator="," open="(" close=")">
              #{value}
          </foreach>
      </if>
</select>
```

#### 10.5.2 传入Array

如果传入的参数类型为Array时，collection的默认属性值为array,同样可以使用@Param注解自定义keyName

```java
List<User> getUserInfo(@Param("userName") String[] userName);
```

```xml
<select id="getUserInfo" resultType="com.test.User">
    SELECT
        *
    FROM user_info
    where
    <if test="userName!= null and userName.length() > 0">
        USERNAME IN
        <foreach collection="userName" item="value" separator="," open="(" close=")">
            #{value}
        </foreach>
    </if>
</select>
```

#### 10.5.3 传入Map

如果传入的参数类型为Map时，collection的属性值可为三种情况：（1.遍历map.keys;2.遍历map.values;3.遍历map.entrySet()）

在list复杂时，选择数据组装传递

```java
List<User> getUserInfo(@Param("user") Map<String,String> user);
```

eg: SELECT * FROM user_info WHERE (USERNAME,AGE) IN (('张三','26'),('李四','58'),('王五','27'),......);

第一种：获取Map的键

```xml
<select id="getUserInfo" resultType="com.test.User">
    SELECT
        *
    FROM user_info
    where
    <if test="user!= null and user.size() >0">
        (USERNAME) IN
        <foreach collection="user.keys" item="key"  separator="," open="(" close=")">
            #{key}
        </foreach>
    </if>
</select>
```

第二种：获取Map的值

```xml
<select id="getUserInfo" resultType="com.test.User">
    SELECT
        *
    FROM user_info
    where
    <if test="user!= null and user.size() >0">
        (USERNAME) IN
        <foreach collection="user.values" item="value"  separator="," open="(" close=")">
            #{value}
        </foreach>
    </if>
</select>
```

第一种：获取Map的键值对

```xml
<select id="getUserInfo" resultType="com.test.User">
    SELECT
        *
    FROM user_info
    where
    <if test="user!= null and user.size() >0">
        (USERNAME,AGE) IN
        <foreach collection="user.entrySet()" item="value" index="key" separator="," open="(" close=")">
            (#{key},#{value})
        </foreach>
    </if>
</select>
```

## 11 缓存

### 11.1 简介

什么是缓存[cache]：

* 保存在内存中的临时数据
* 将用户经常查询的数据放在缓存中，用户查询数据就不用从数据库中查询，从缓存中查询，从而提高效率，解决了高并系统的性能问题

为什么使用缓存：减少与数据库的交互次数，减少系统开销，提高系统效率

什么样的数据使用缓存：经常查询且不经常修改的数据

### 11.2 Mybatis缓存

Mybatis包含了一个非常强大的查询缓存特性，可以非常方便的定制和配置缓存

Mybatis系统中默认定义了两级缓存：**一级缓存**和**二级缓存**

* 默认情况下，只开启了一级缓存（SqlSession级别的缓存，也称为本地缓存）
* 二级缓存需要手动开启和配置，是基于namespace级别的缓存
* 为了提高扩展性，Mybatis定义了缓存接口cache，可以通过cache定义二级缓存

### 11.3 一级缓存

本地缓存

* 与数据库同一次会话期间查询到的数据会放在本地缓存中
* 以后如果需要获得相同的数据，直接从缓存中取，不需要再从数据库中查询

```java
User user1 = mapper.queryById(1);
System.out.println("user = " + user1);
System.out.println("===============");
User user2 = mapper.queryById(1);
System.out.println("user = " + user2);
```

SQL只执行一次，从缓存中取数据

缓存失效：增删改操作可能会改变原来的参数

总结：一级缓存只在一次SqlSession中有效

### 11.4 二级缓存

全局缓存

一级缓存作用域太低，基于namespace级别的缓存，一个命名空间，对应一个二级缓存

工作机制：

* 一个会话查询一条数据，这个数据就会被放在当前会话的一级缓存中
* 如果当前会话关闭了，这个会话对应的一级缓存就没了，但如果是想要的，会话关闭了，一级缓存中的数据会被保存到二级缓存中
* 新的会话查询信息，就可以从二级缓存中获取
* 不同的mapper查询的数据会放在自己对应的缓存中

显示的开启全局缓存

```xml
<setting name="cacheEnabled" value="true"/>
```

默认情况下，只启用了本地的会话缓存，它仅仅对一个会话中的数据进行缓存

`mapper.xml`

```xml
<cache
  eviction="FIFO"
  flushInterval="60000"
  size="512"
  readOnly="true"/>
```

- 映射语句文件中的所有 select 语句的结果将会被缓存。
- 映射语句文件中的所有 insert、update 和 delete 语句会刷新缓存。
- 缓存会使用最近最少使用算法（LRU, Least Recently Used）算法来清除不需要的缓存。
- 缓存不会定时进行刷新（也就是说，没有刷新间隔）。
- 缓存会保存列表或对象（无论查询方法返回哪种）的 1024 个引用。
- 缓存会被视为读/写缓存，这意味着获取到的对象并不是共享的，可以安全地被调用者修改，而不干扰其他调用者或线程所做的潜在修改。

```java
SqlSession sqlSession1 = MybatisUtils.getSqlSession();
UserDao mapper1 = sqlSession1.getMapper(UserDao.class);
User user1 = mapper1.queryById(1);
System.out.println("user = " + user1);
sqlSession1.close();
System.out.println("===============");
SqlSession sqlSession2 = MybatisUtils.getSqlSession();
UserDao mapper2 = sqlSession2.getMapper(UserDao.class);
User user2 = mapper2.queryById(1);
System.out.println("user = " + user2);
sqlSession2.close();
```

总结：只要开启了二级缓存，在同一个mapper中有效

### 11.5 缓存的顺序

```markdown
1. 二级缓存
2. 一级缓存
3. 查询数据库
```
