#  SSM框架整合搭建

#### 一、创建Maven项目导入对应jar包

##### 1.设置打包方式为war包

##### 2.导入配置文件

```pom.xml
 <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.3.19</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.3.19</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aspects</artifactId>
            <version>5.3.19</version>
        </dependency>
<!--        AOP Spring外部依赖-->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.9.1</version>
            <scope>runtime</scope>
        </dependency>
<!--        数据库连接池-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.2.9</version>
        </dependency>
        <!-- MySQL驱动 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.29</version>
        </dependency>
        <!--        ServletAPI-->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>4.0.1</version>
            <scope>provided</scope>
        </dependency>
 <!--        mybatis -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.9</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.7</version>
        </dependency>
        <!-- log4j日志 配置log4j2.xml-->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
           <!-- slf4j日志门面的一个具体实现 -->
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.2.11</version>
        </dependency>
        <!--        spring5和thymeleaf整合包 页面用jsp的话不用引入-->
        <dependency>
            <groupId>org.thymeleaf</groupId>
            <artifactId>thymeleaf-spring5</artifactId>
            <version>3.0.15.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.thymeleaf</groupId>
            <artifactId>thymeleaf</artifactId>
            <version>3.0.15.RELEASE</version>
        </dependency>
        <!-- EL JL TL表达式 -->
        <dependency>
            <groupId>jstl</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
<!--单元测试-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.2</version>
            <scope>test</scope>
        </dependency>
```

##### 3.在src/main下创建webapp文件 在项目结构中添加web.xml文件

##### 4.在src/mian/java/com.ssm创建包

​     bean    mapper  service  serviceImpl  comtroller

##### 5.创建对应类用于框架搭建测试

bean包   实现Serializable用于持久层Mybatis的二级缓存

```Account.java
public class Account implements Serializable {
    static final long serialVersionUID = 423333231212L;
    private Integer id;
    private String name;
    private double balance;

    public Account() {
    }

    public Account(Integer id, String name, double balance) {
        this.id = id;
        this.name = name;
        this.balance = balance;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public double getBalance() {
        return balance;
    }

    public void setBalance(double balance) {
        this.balance = balance;
    }

    @Override
    public String toString() {
        return "Account{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", balance=" + balance +
                '}';
    }
}
```

mapper包

```accountMapper
public interface accountMapper {
    /**
     * 查询所有账户信息
     * @return
     */
    List<Account> getAllAccount();

    /**
     * 添加账户信息
     * @param account
     * @return
     */
    int addAccount(Account account);
}
```

service包

```accountService
public interface accountService {

    List<Account> getAllAccount();

    int addAccount(Account account);
}
```

serviceImpl包

```accountServiceImpl.java
public class accountServiceImpl implements accountService {
    @Override
    public List<Account> getAllAccount() {
        System.out.println("service层");
        return null;
    }

    @Override
    public int addAccount(Account account) {
        System.out.println("service层");
        return 0;
    }
}
```

controller包

```accountController.java
public class accountController {
}
```

##### 6.在resources文件夹创建log4j2.xml

```log4j2.xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT">
            <PatternLayout pattern="%d %-5p [%t] %C{2} (%F:%L) - %m%n"/>
        </Console>
        <RollingFile name="RollingFile" fileName="logs/strutslog1.log"
                     filePattern="logs/$${date:yyyy-MM}/app-%d{MM-dd-yyyy}-%i.log.gz">
            <PatternLayout>
                <Pattern>%d{MM-dd-yyyy} %p %c{1.} [%t] -%M-%L- %m%n</Pattern>
            </PatternLayout>
            <Policies>
                <TimeBasedTriggeringPolicy />
                <SizeBasedTriggeringPolicy size="1 KB"/>
            </Policies>
            <DefaultRolloverStrategy fileIndex="max" max="2"/>
        </RollingFile>
    </Appenders>
    <Loggers>
        <logger name="java.sql">
            <level value="debug"/>
        </logger>
        <logger name="org.apache.ibatis">
            <level value="info"/>
        </logger>
        <Root level="debug">
            <AppenderRef ref="STDOUT"/>
        </Root>
    </Loggers>
</Configuration>
```

#### 二、搭建Spring框架

##### 1.在resources文件夹下创建ApplicationContext.xml   Spring配置文件

```ApplicationConfig.xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context  http://www.springframework.org/schema/context/spring-context.xsd">
  <!--spring注解扫描开启  但是不扫描controller层-->
  <context:component-scan base-package="com.ssm">
    <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
  </context:component-scan>
</beans>
```

##### 2.添加注解

```java
@Service("accountService")
public class accountServiceImpl implements accountService
```

##### 3.测试Spring框架

```testSpring.java
public class testSpring {
    @Test
    public  void testSpring(){
        ApplicationContext ac=new ClassPathXmlApplicationContext("ApplicationContext.xml");
        accountService accountService=ac.getBean("accountService", accountService.class);
        accountService.getAllAccount();
    }
}
```

##### 4.控制台输出内容

  service层      配置成功

正常输出并且日志报错的话就是log4j2的配置文件的问题

#### 三、搭建SpringMVC框架

##### 1.配置web.xml文件

 配置编码过滤器

```编码过滤器
<!--    配置编码过滤器-->
<filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <init-param>
        <param-name>forceRequestEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

配置前端控制器

```前端控制器
<!--    配置SpringMVC的前端控制器，对浏览器发送的请求进行统一处理-->
<servlet>
    <servlet-name>SpringMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--        配置SpringMVC配置文件的位置和名称-->
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:SpringMVC.xml</param-value>
    </init-param>
    <!--        将前端控制器DispatcherServlet的初始化提前到服务器启动时  不然在一次访问初始化时间太长-->
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>SpringMVC</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

选择配置REST架构风格

```rest架构风格
<!--    配置处理请求方式为put和delete的HiddenHttpMethodFilter-->
<filter>
    <filter-name>hiddenHttpMethodFilter</filter-name>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>hiddenHttpMethodFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

##### 2.配置SpringMVC.xml文件

  文件名要和web.xml中前端控制器<param-value>classpath:**SpringMVC.xml**</param-value>吻合

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/context  http://www.springframework.org/schema/context/spring-context.xsd
http://www.springframework.org/schema/mvc  http://www.springframework.org/schema/mvc/spring-mvc.xsd">
<!--    开启注解扫描 只扫描controller注解-->
<context:component-scan base-package="com.ssm">
    <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>

    <!--    视图解析器-->
    <!-- 配置Thymeleaf视图解析器 -->
    <bean id="viewResolver" class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
        <property name="order" value="1"/>
        <property name="characterEncoding" value="UTF-8"/>
        <property name="templateEngine">
            <bean class="org.thymeleaf.spring5.SpringTemplateEngine">
                <property name="templateResolver">
                    <bean class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">
                        <!-- 视图前缀 -->
                        <property name="prefix" value="/WEB-INF/templates/"/>
                        <!-- 视图后缀 -->
                        <property name="suffix" value=".html"/>
                        <property name="templateMode" value="HTML5"/>
                        <property name="characterEncoding" value="UTF-8"/>
                    </bean>
                </property>
            </bean>
        </property>
    </bean>
    <!-- 视图控制器-->
    <mvc:view-controller path="/" view-name="index"></mvc:view-controller>
    <mvc:annotation-driven/>
    <!--处理静态资源，例如html、js、css、jpg 若只设置该标签，则只能访问静态资源，其他请求则无法访问 此时必须设置<mvc:annotation-driven/>解决问题 -->
    <mvc:default-servlet-handler/>
    <!-- 开启mvc注解驱动 -->
    <mvc:annotation-driven>
        <mvc:message-converters>
            <!-- 处理响应中文内容乱码 -->
            <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                <property name="defaultCharset" value="UTF-8"/>
                <property name="supportedMediaTypes">
                    <list>
                        <value>text/html</value>
                        <value>application/json</value>
                    </list>
                </property>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>

</beans>
```

如果使用JSP的话视图解析器如下配置

```
<!--    视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/templates/"></property>
        <property name="suffix" value=".jsp"></property>
    </bean>
```

##### 3.在webapp\WEB-INF\下创建templates文件夹

##### 4.创建视图

  在templates文件夹下创建index.html 和 allAccount.html视图

index.html

```index.html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<a th:href="@{/accountGetAll}">查询所有账户信息</a>
</body>
</html>
```

allAccount.html

```allAccount.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
查询所有信息页面
</body>
</html>
```

##### 5.编写controller层

```accountController.java
@Controller
public class accountController {
    @RequestMapping("/accountGetAll")
    public  String getAllAccount(){
        System.out.println("控制层");
        return "allAccount";
    }
}
```

##### 6.配置Tomcat服务器并运行

   配置成功情况下  index页面会跳转到allAccount页面 idea控制台会打印控制层

######    控制台打印中文乱码解决方案

​           1）File  2）settings  3）editor  4）File Encodings 都设置成UTF-8

​           5）打开项目所配置的tomcat页面在 VM options 输入-Dfile.encoding=UTF-8

#### 四、Spring和SpringMVC整合

##### 1.配置Spring监听器

在web.xml中添加配置（在前端控制器之后就可以 也就是在加载完SpringMVC.xml之后加载）Spring配置文件名称<param-value>classpath:**ApplicationContext.xml**</param-value>一定要一致

```
<!--配置 Spring 的监听器-->
<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
<!--设置配置文件路径-->
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:ApplicationContext.xml</param-value>
</context-param>
```

##### 2.在controller层调用service层方法

```accountController.java
@Controller
public class accountController {
    @Autowired
    private accountService accountService;
    @RequestMapping("/accountGetAll")
    public  String getAllAccount(){
        System.out.println("控制层");
        accountService.getAllAccount();
        return "allAccount";
    }
}
```

   重新运行服务器后 idea控制台打印  控制层、service层 则整合成功

#### 五、搭建Mybatis框架

##### 1.resources文件夹下创建mybatis配置文件（mybatis-config.xml）

```mybatis-config.xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <properties resource="jdbc.properties"/>

    <typeAliases>
        <package name="com.ssm.bean"/>
    </typeAliases>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
    <!--    引入映射文件-->
    <mappers>
        <package name="com.ssm.mapper"></package>
    </mappers>
</configuration>
```

##### 2.创建连接数据库信息文件（jdbc.properties）

```jdbc.properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/   你的数据库名
jdbc.username=root
jdbc.password=root
```

##### 3.创建mapper类对应的mapper.xml文件

 在resources文件下创建和类相同的路径  com/ssm/mapper

创建类名对应的xml名文件    accountMapper.xml

```accountMapper.xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ssm.mapper.accountMapper">
    <select id="getAllAccount" resultType="account">
     select * from account;
    </select>

</mapper>
```

名称空间要和类名对应

##### 4.在数据库创建对应bean的表

##### 5.编写mybatis测试类并运行

```testMybatis.class
public class testMybatis {
    @Test
    public void testMybatis(){
        try {
            InputStream is= Resources.getResourceAsStream("mybatis-config.xml");
            SqlSessionFactory sqlSessionFactory=new SqlSessionFactoryBuilder().build(is);
            SqlSession sqlSession=sqlSessionFactory.openSession(true);
            accountMapper accountMapper=sqlSession.getMapper(accountMapper.class);
            List<Account> list=accountMapper.getAllAccount();
            list.forEach(account -> System.out.println(account));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

  idea控制台中输出数据库信息  mybatis就搭建成功了

#### 六、Spring和Mybatis整合

##### 1.修改Spring的配置文件

 把mybatis配置文件mybatis-config.xml整合至ApplicationContext.xml中

```ApplicationContext.xml
<!--  引入jdbc.properties-->
  <context:property-placeholder location="jdbc.properties"/>
<!--  配置数据库连接池-->
  <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" destroy-method="close">
    <property name="url" value="${jdbc.url}" />
    <property name="username" value="${jdbc.username}" />
    <property name="password" value="${jdbc.password}" />
    <property name="driverClassName" value="${jdbc.driver}" />
  </bean>
<!--  配置SqlSessionFactory工厂-->
  <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource"></property>
  </bean>
<!--  引入映射文件mapper-->
  <bean id="mapperScannerConfigurer" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="basePackage" value="com.ssm.mapper"></property>
  </bean>
```

ApplicationContext.xml中添加事务管理

```ApplicationContext.xml
<!--创建事务管理器-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager" >
  <!--注入数据源-->
  <property name="dataSource" ref="dataSource"/>
</bean>
<!--开启事务注解-->
<tx:annotation-driven transaction-manager="transactionManager"/>
```

删除mybatis-config.xml文件

将mapper配置文件中的名称空间改成全路径

```accountMapper.xml
<mapper namespace="com.ssm.mapper.accountMapper">
```

##### 2.服务层调用持久层

```accountServiceImpl.class
@Autowired
com.ssm.mapper.accountMapper accountMapper;
@Override
public List<Account> getAllAccount() {

    System.out.println("service层");
    return  accountMapper.getAllAccount();
}
```

##### 3.控制层调用服务层

```accountController.class
@Controller
public class accountController {
    @Autowired
    private accountService accountService;

    @RequestMapping("/accountGetAll")
    public  String getAllAccount(){
        System.out.println("控制层");
        List<Account> list=accountService.getAllAccount();
        list.forEach(account -> System.out.println(account));
        return "allAccount";
    }
}
```

##### 4.持久层标注注解

```
@Repository
public interface accountMapper 
```

##### 5.启动服务器

​    跳转页面后 idea控制台输出了数据的信息 就表示整合完毕

   如果启动服务报出ERROR：Could not load properties; nested exception is java.io.FileNotFoundException: Could not open ServletContext resource [/jdbc.properties]

​    找到Spring的配置文件 引入jdbc.properties文件的地方

```
<context:property-placeholder location="jdbc.properties"/>
```

​    将路径改成

```
<context:property-placeholder location="classpath*:jdbc.properties"/>
```

   就可以解决问题

​                                                                                                                                                                                                       2022.5.17
