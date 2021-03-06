---
sort: 2
---

# xml 을 이용한 프로젝트 뼈대 설정
> pom.xml 수정    
> 데이터베이스 설정   
> 스프링 MVC 처리    

## 1. pom.xml 설정
1. java 버전, 스프링 버전 변경
    ```xml
    <properties>
		<java-version>1.8</java-version>
		<org.springframework-version>5.2.12.RELEASE</org.springframework-version>
		<org.aspectj-version>1.6.10</org.aspectj-version>
		<org.slf4j-version>1.6.6</org.slf4j-version>
	</properties>
    <!-- 생략 -->
     <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>2.5.1</version>
        <configuration>
            <source>1.8</source>
            <target>1.8</target>
            <compilerArgument>-Xlint:all</compilerArgument>
            <showWarnings>true</showWarnings>
            <showDeprecation>true</showDeprecation>
        </configuration>
    </plugin>
    ```
2. 스프링 관련 추가 라이브러리 
- spring-tx, spring-jdbc, spring-test

    ```xml
    <!-- https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>${org.springframework-version}</version>
    </dependency>   
    <!-- https://mvnrepository.com/artifact/org.springframework/spring-tx -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-tx</artifactId>
        <version>${org.springframework-version}</version>
    </dependency>
    <!-- spring-test -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>${org.springframework-version}</version>
        <scope>provided</scope>
    </dependency>
    ```

3. DB 연결 관련 라이브러리 추가
- HikariCP, MyBatis, mysql-connector-java, mybatis-spring, Log4jdbc

    ```xml
    <!--DB 관련 추가 라이브러 -->
    <!-- MySQL -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.22</version>
    </dependency>
        <!-- https://mvnrepository.com/artifact/com.zaxxer/HikariCP -->
    <dependency>
        <groupId>com.zaxxer</groupId>
        <artifactId>HikariCP</artifactId>
        <version>3.4.5</version>
    </dependency>
    <!-- MyBatis 3.4.1 -->
    <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.4</version>
    </dependency>
    <!-- MyBatis-Spring -->
    <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis-spring -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis-spring</artifactId>
        <version>2.0.3</version>
    </dependency>
        <!-- https://mvnrepository.com/artifact/org.bgee.log4jdbc-log4j2/log4jdbc-log4j2-jdbc4.1 -->
    <dependency>
        <groupId>org.bgee.log4jdbc-log4j2</groupId>
        <artifactId>log4jdbc-log4j2-jdbc4</artifactId>
        <version>1.16</version>
    </dependency> 
    ```

4. Lombox 추가

    ```xml
    <!-- lombok -->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.0</version>
        <scope>provided</scope>
    </dependency> 
    ```

5. jUnit 버전 변경
- 4.7 -> 4.12

    ```xml
    <!-- Test -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency> 
    ```

5. Servlet 버전 변경
- 2.5 -> 3.0 이상

    ```xml
    <!-- Servlet -->
    <!-- <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>servlet-api</artifactId>
        <version>2.5</version>
        <scope>provided</scope>
    </dependency> -->
    <!-- https://mvnrepository.com/artifact/javax.servlet/javax.servlet-api -->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>3.1.0</version>
        <scope>provided</scope>
    </dependency>
    ```

6. Controller 객체를 json으로 변환하는 라이브러리 추가
- jackson-binder

    ```xml
    <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.11.3</version>
    </dependency>				
    ```

7. Maven Project Update
- 프로젝트 우클릭 > Maven > Update Project...

## 2. 테이블 생성과 더미 데이터 생성
- mysqlWorkbench로 'book_ex'/'book_ex'로 접속한다. (기본 DB는 testdb로 정하였었다.)
- 아래의 스키마를 실행하여 테이블을 생성한다.

    ```sql
    CREATE TABLE `testdb`.`tbl_board` (
    `bno` INT NOT NULL AUTO_INCREMENT,
    `title` NVARCHAR(200) NOT NULL,
    `content` NVARCHAR(2000) NOT NULL,
    `writer` NVARCHAR(50) NOT NULL,
    `regdate` TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    `updatedate` TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (`bno`));
    ```

- 더미 데이터를 5건 정도 입력한다.

    ```sql
    insert into tbl_board ( title, content, writer)
    values ('테스트제목','테스트내용','user00');
    ```

## 3. 데이터베이스 설정

1. `root-context.xml` : DataSource, sqlSession, log4jdbc 설정

    ```xml
    <!-- Root Context: defines shared resources visible to all other web components -->
	<bean id='hikariConfig' class='com.zaxxer.hikari.HikariConfig'>
		<property name='driverClassName' value='net.sf.log4jdbc.sql.jdbcapi.DriverSpy'></property>
		<property name='jdbcUrl' value='jdbc:log4jdbc:mysql://localhost:3306/testdb?serverTimezone=UTC'></property>
		<property name="username" value='book_ex'></property>
		<property name="password" value="book_ex"></property>
	</bean>
	
	<!-- HikariCP configuration -->
	<bean id="dataSource" class="com.zaxxer.hikari.HikariDataSource" destroy-method="close">
		<constructor-arg ref="hikariConfig"/>
	</bean>
	
	<!-- sqlSession -->
	<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="dataSource"></property>
	</bean>
    ```

2. `root-context.xml` : MyBatis 설정
    - namespace 추가
    - mybatis-spring:scan 추가
    
    ```xml
    <beans xmlns:mybatis-spring="http://mybatis.org/schema/mybatis-spring">
    <!-- 생략 -->
    <mybatis-spring:scan base-package="org.example.mapper"/>
    ```

3. `log4jdbc.log4j2.properties` 파일 추가
    - src/main/resource 폴더하위
    
    ```properties
    log4jdbc.spylogdelegator.name=net.sf.log4jdbc.log.slf4j.Slf4jSpyLogDelegator
    #log4jdbc.auto.load.popular.drivers = false
    ```

## 4. JDBC TEST
- src/test/java 폴더 하위 `org.example.persistance` 패키지 생성
- JDBCTest.java 생성 후 테스트코드 작성

```java
@Log4j
public class JDBCTest {

	static {
		try {
			Class.forName("com.mysql.cj.jdbc.Driver");
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
	
	@Test
	public void testConnection() {
		Connection conn = null;
		try{
			conn = 
				DriverManager.getConnection("jdbc:mysql://localhost/testDb?characterEncoding=UTF-8&serverTimezone=UTC",
						"book_ex",
						"book_ex");
			log.info(conn);
			conn.close();
		} catch (SQLException e) {
			
			fail(e.getMessage());
		} 
	}
}
```   
- `run as` > `JUnit Test` 실행 후, conn 객체가 로그에 찍히는지 확인. 

## 5. DataSource & ConnectionPool TEST
- src/test/java 폴더 하위 `org.example.persistance` 패키지
- DataSourceTest.java 생성 후 테스트코드 작성
```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("file:src/main/webapp/WEB-INF/spring/root-context.xml")
@Log4j
public class DataSourceTest {

	@Setter(onMethod = @__({@Autowired}))
	private DataSource ds; 
	
	@Test
	public void testConnection() {
		Connection conn = null;
		try{
			conn = ds.getConnection();
			log.info(conn);
		} catch (Exception e) {
			fail(e.getMessage());
		}
	}
}
```
- `run as` > `JUnit Test` 실행 후, conn 객체가 로그에 찍히는지 확인. 