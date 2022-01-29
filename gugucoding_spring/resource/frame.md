# Spring legecy project - frame / 2번 문제 답안

- [프로젝트 생성 및 설정](https://github.com/sonchanwoo/workbook/blob/main/gugucoding_spring/resource/1_answer.md)

<br/>

- pom.xml > 추가

```xml
<properties>
	<java-version>11</java-version>
	<org.springframework-version>5.0.7.RELEASE</org.springframework-version>
	<org.aspectj-version>1.6.10</org.aspectj-version>
	<org.slf4j-version>1.6.6</org.slf4j-version>
</properties>

		<dependency>
			<groupId>log4j</groupId>
			<artifactId>log4j</artifactId>
			<version>1.2.17</version>
		</dependency>

		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>
			<version>3.1.0</version>
			<scope>provided</scope>
		</dependency>

		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.12</version>
			<scope>test</scope>
		</dependency>

		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<version>1.18.22</version>
			<scope>provided</scope>
		</dependency>

        <!-- MyBatis를 위한 라이브러리들 -->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>8.0.27</version>
		</dependency>
		<dependency>
			<groupId>org.bgee.log4jdbc-log4j2</groupId>
			<artifactId>log4jdbc-log4j2-jdbc4</artifactId>
			<version>1.16</version>
		</dependency>
		<dependency>
			<groupId>com.zaxxer</groupId>
			<artifactId>HikariCP</artifactId>
			<version>5.0.0</version>
		</dependency>
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis</artifactId>
			<version>3.5.7</version>
		</dependency>
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis-spring</artifactId>
			<version>1.3.2</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-jdbc</artifactId>
			<version>${org.springframework-version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-test</artifactId>
			<version>${org.springframework-version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-tx</artifactId>
			<version>5.3.15</version>
		</dependency>

        <plugin>
			<groupId>org.apache.maven.plugins</groupId>
			<artifactId>maven-compiler-plugin</artifactId>
			<version>2.5.1</version>
			<configuration>
				<source>${java-version}</source>
				<target>${java-version}</target>
				<compilerArgument>-Xlint:all</compilerArgument>
				<showWarnings>true</showWarnings>
				<showDeprecation>true</showDeprecation>
			</configuration>
		</plugin>
```

<br/>

- log4jdbc.log4j2.properties > resource에 추가

```properties
log4jdbc.spylogdelegator.name=net.sf.log4jdbc.log.slf4j.Slf4jSpyLogDelegator
```

<br/>

- root-context > 추가 & 드라이버 부분 수정 & 네임스페이스

```xml
	<bean id="hikariConfig" class="com.zaxxer.hikari.HikariConfig">
		<property name="driverClassName"
			value="net.sf.log4jdbc.sql.jdbcapi.DriverSpy"></property>
		<property name="jdbcUrl"
			value="jdbc:log4jdbc:mysql://localhost:3306/****">
		</property>
		<property name="username" value="****"></property>
		<property name="password" value="****"></property>
	</bean>

	<bean id="dataSource" class="com.zaxxer.hikari.HikariDataSource"
		destroy-method="close">
		<constructor-arg ref="hikariConfig" />
	</bean>

	<bean id="sqlSessionFactory"
		class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="dataSource"></property>
        <property name="configLocation" value="classpath:/mybatis-config.xml"></property>
	</bean>

    <!-- 패키지 자신의 환경에 맞게 수정해서 사용 -->
    <context:component-scan base-package="com.company.service"></context:component-scan>

	<mybatis-spring:scan base-package="com.company.dao" />
```

<br/>

- mybatis-config.xml > resource에 추가

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration 
    PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-config.dtd">
    
<configuration>
  <typeAliases>
    
    <!-- 사용하지 않으면 주석처리 -->
    <typeAlias type="" alias="" />
    
  </typeAliases>
</configuration>
```

<br/>



- SqlSessionFactoryTest.java > test > dao추가 & 성공시키기

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("file:src/main/webapp/WEB-INF/spring/root-context.xml")
@Log4j
public class SqlSessionFactoryTests {    
    
    @Setter(onMethod_ = @Autowired)
    private SqlSessionFactory factory;
    
    @Test
    public void testFactory() {
        assertNotNull(factory);
    }
}

```

<br/>

- MyDAO.xml > resources에 패키지와 같은 경로(꼭 폴더하나씩!!)에 추가

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- 패키지명 자신에 환경에 맞게 수정해서 사용 -->
<mapper namespace="com.company.dao.MyDAO">
	<select id="getTime" resultType="String">
		<![CDATA[
			select now()
		]]>
	</select>
</mapper>
```

<br/>

- web.xml > 추가

```xml
	<filter>
		<filter-name>encoding</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>UTF-8</param-value>
		</init-param>
	</filter>

	<filter-mapping>
		<filter-name>encoding</filter-name>
		<servlet-name>appServlet</servlet-name>
	</filter-mapping>
```

<br/>

- java 파일들 > 패키지 작성 후 추가 (컨트롤러는 수정)

```java
public interface MyDAO {

    //매퍼를 사용하지 아니할 경우 어노테이션 사용
    //@Select("select now()")
    public String getTime();
}
```

```java
public interface MyService {
    String getTime();
}
```

```java
@Service
@AllArgsConstructor
public class MyServiceImpl implements MyService{

    private MyDAO dao;
    
    @Override
    public String getTime() {
        return dao.getTime();
    }
}
```

```java
@Controller
@AllArgsConstructor
public class HomeController {
    
    private MyService service;
    
	@RequestMapping(value = "/", method = RequestMethod.GET)
	public String home(Model model) {
	    model.addAttribute("time", service.getTime());
	
	    return "home";
	}	
}
```

<br/>

- jsp파일 수정

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt" %>
<html>
<head>
	<title>Home</title>
</head>
<body>
<h1>
	Time : <c:out value="${time}"></c:out>
    <!-- <fmt:formatDate pattern="yyyy/MM/dd" value="${}"/> -->
</h1>
</body>
</html>
```

<br/>

- Tests > 패키지 지정 후 추가한 뒤, 모두 성공시키세요

```java
@RunWith(SpringJUnit4ClassRunner.class)
@WebAppConfiguration//WebApplicationContext를 사용하가 위함
@ContextConfiguration({
    "file:src/main/webapp/WEB-INF/spring/root-context.xml",
"file:src/main/webapp/WEB-INF/spring/appServlet/servlet-context.xml"})
@Log4j
public class HomeControllerTest {

    @Setter(onMethod_=@Autowired)
    private WebApplicationContext ctx;

    private MockMvc mockMvc;

    @Before
    public void setUp() {
        this.mockMvc = MockMvcBuilders.webAppContextSetup(ctx).build();
    }

    @Test
    public void testTime() throws Exception{
        ModelAndView modelAndView = mockMvc.perform(MockMvcRequestBuilders.get("/"))//원하는 방식과, 경로
                .andReturn().getModelAndView();

        ModelMap modelMap = modelAndView.getModelMap();

        String viewName = modelAndView.getViewName();


        log.info("\n...................\n modelMap : "
                +modelMap+"\n viewName : "
                +viewName+"\n...................");
    }
}
```

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("file:src/main/webapp/WEB-INF/spring/root-context.xml")
@Log4j
public class MyDAOTest {
    @Setter(onMethod_ = @Autowired)
    private MyDAO dao;
    
    @Test
    public void testDAO() {
        assertNotNull(dao);
    }
    
    @Test
    public void testGetTime() {
        dao.getTime();
    }
}
```

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("file:src/main/webapp/WEB-INF/spring/root-context.xml")
@Log4j
public class MyServiceTest {
    
    @Setter(onMethod_ = @Autowired)
    private MyService service;
    
    @Test
    public void test() {
        assertNotNull(service);
    }
    
    @Test
    public void testGetTime() {
        log.info(service.getTime()+".................................................");
    }
}
```