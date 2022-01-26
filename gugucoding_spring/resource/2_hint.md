- pom.xml 적용

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

- log4jdbc.log4j2.properties

```properties
log4jdbc.spylogdelegator.name=net.sf.log4jdbc.log.slf4j.Slf4jSpyLogDelegator
```

<br/>

- root-context (네임스페이스까지)

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
	</bean>
```

<br/>

- SqlSessionFactoryTest.java

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

- MyDAO.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
 
<mapper namespace="">

</mapper>
```

<br/>