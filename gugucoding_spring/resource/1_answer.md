- 프로젝트 생성

![](https://github.com/sonchanwoo/workbook/blob/main/gugucoding_spring/resource/new.png?raw=true)

![](https://github.com/sonchanwoo/workbook/blob/main/gugucoding_spring/resource/base_package.png?raw=true)

<br/>

- utf-8, default browser 설정

<br/>

- pom.xml 수정 + update project

```xml
<?xml version="1.0" encoding="UTF-8"?>
...
	<properties>
		<java-version>11</java-version>
		<org.springframework-version>5.0.7.RELEASE</org.springframework-version>
		<org.aspectj-version>1.6.10</org.aspectj-version>
		<org.slf4j-version>1.6.6</org.slf4j-version>
	</properties>
...
		<dependency>
			<groupId>log4j</groupId>
			<artifactId>log4j</artifactId>
			<version>1.2.17</version>
		</dependency>
...
				
		<!-- Servlet -->
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>
			<version>3.1.0</version>
			<scope>provided</scope>
		</dependency>

...
	
		<!-- Test -->
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.12</version>
			<scope>test</scope>
		</dependency>        
	</dependencies>
    <build>
        <plugins>

...

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

...
        </plugins>
    </build>
</project>

```

<br/>

- tomcat module - path( /controller -> / ) 수정

![](https://github.com/sonchanwoo/workbook/blob/main/gugucoding_spring/resource/tomcat_path.png?raw=true)

<br/>

- 화면 확인

![](https://github.com/sonchanwoo/workbook/blob/main/gugucoding_spring/resource/hello.png?raw=true)

