---
sort : 3
---

# JDBC의 인증/권한 데이터와 시큐리티 연결하기
> 인증과 권한 처리 : Authentication Manager   
> 인증/권한 정보 제공 : Provider <- UserDetailsService 인터페이스 구현체   
> UserDetailsService 구현체 API : CachingUserDetailsService, InMemoryUserDetailsManager, JdbcDaoImpl, JdbcUserDetailsManager, LdapUserDetailsManager, LdapUserDetailsService 등   

## <font color='blue'>1. 스프링 시큐리티가 JDBC를 이용하기 위한 테이블 설정</font>
> JdbcUserDetailsManager 클래스 이용   
> 스프링 시큐리티가 미리 정해놓은 인증/권한 관련 쿼리 :[참조](https://github.com/spring-projects/spring-security/blob/master/core/src/main/java/org/springframework/security/provisioning/JdbcUserDetailsManager.java)   

**<font color='green' style='font-size:large;'>1-1. 스프링 시큐리티가 정해놓은 테이블 스키마를 생성해서 사용하는 방법</font>**

**<font color='DarkOrange'>지정된 테이블 스키마 작성</font>**   

. MySql 기준   

```sql
create table users(
	username varchar(50) not null,
    password varchar(50) not null,
    enabled char(1)default '1',
    primary key (username));
    
create table authorities(
	username varchar(50) not null,
    authority varchar(50) not null);

-- 테스트를 위한 dump 데이터를 입력
insert into users (username, password ) values ('user00','pw00');
insert into users (username, password ) values ('member00','pw00');
insert into users (username, password ) values ('admin00','pw00');

insert into authorities (username, authority) values ('user00', 'ROLE_USER');
insert into authorities (username, authority) values ('member00', 'ROLE_MANAGER');
insert into authorities (username, authority) values ('admin00', 'ROLE_MANAGER');
insert into authorities (username, authority) values ('admin00', 'ROLE_ADMIN');
```

**<font color='DarkOrange'>security-context.xml 설정</font>**   

authentication-provider 하위에 `jdbc-user-service` 를 설정한다.      

data-source-ref의 값인 'dataSource' 가 root-context.xml에 등록되어 있어야 한다.   

```xml
	<security:authentication-manager>
		<security:authentication-provider>
			<security:jdbc-user-service data-source-ref="dataSource"/>
		</security:authentication-provider>
	</security:authentication-manager>
```

**<font color='DarkOrange'>로그인 테스트</font>**   

탐캣을 실행하고 http://localhost:8080/sample/admin 으로 접속하여 'admin00', 'pw00' 으로 로그인 한다.   

console에 아래와 같이 쿼리가 실행되는 것을 확인한다.   

아직, PasswordEncoder를 등록하지 않아서 에러가 발생하지만 쿼리 결과가 실행되는 것을 확인 할 수 있다.      

```
jdbc.sqltiming - select username,password,enabled from users where username = 'admin00' 
INFO : jdbc.resultsettable - 
|---------|-------------|
|username |authority    |
|---------|-------------|
|[unread] |ROLE_MANAGER |
|[unread] |ROLE_ADMIN   |
|---------|-------------|

(..생략..)

// PasswordEncoder 없다는 에러 발생, spring5는 반드시 있어야 함.
java.lang.IllegalArgumentException: There is no PasswordEncoder mapped for the id "null"
```

**<font color='green' style='font-size:large;'>1-2. 기존에 작성된 테이블 스키마를 사용하는 방법</font>**   

**<font color='DarkOrange'>기존의 테이블 스키마</font>**   

```sql
create table tbl_member(
	userid varchar(50) not null primary key,
    userpw varchar(100) not null,
    username varchar(100) not null,
    regdate timestamp default current_timestamp,
    updatedate timestamp default current_timestamp,
    enabled char(1) default '1'
);


create table tbl_member_auth(
	userid varchar(50) not null,
    auth varchar(50) not null
);
```

**<font color='DarkOrange'>BCryptPasswordEncoder 사용하여 PasswordEncoder 등록하기</font>**   

> **BCryptPasswordEncoder** : 태생자체가 패스워드를 저장하는 용도로 설계됨, 암호화는 가능하지만 복호화는 안됨.   
> BCryptPasswordEncoder는 스프링 시큐리티 API 에 이미 정의되어 있음.   

security-context.xml 에 스프링 시큐리티 API 인 BCryptPasswordEncoder 를 빈으로 등록하고, password-encoder 로 추가한다.   

```xml
<!-- 생략 -->
<bean id="bcryptPasswordEncoder" class="org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder"/>
<!-- 생략 -->
<security:authentication-manager>
    <security:authentication-provider>
        <security:password-encoder ref="bcryptPasswordEncoder"/>
    </security:authentication-provider>
</security:authentication-manager>
```

**<font color='DarkOrange'>PasswordEncoder 구현하여 사용자 PasswordEncoder를 만들어보기</font>**   

encode()와 matches() 만 구현하면 되는데, 하기 예제는 인코딩을 하지 않는 로직이다.   

```java
package org.example.security;

import org.springframework.security.crypto.password.PasswordEncoder;

public class CustomNoOpPasswordEncoder implements PasswordEncoder{

	@Override
	public String encode(CharSequence rawPassword) {
		// TODO Auto-generated method stub
		return rawPassword.toString();
	}

	@Override
	public boolean matches(CharSequence rawPassword, String encodedPassword) {
		// TODO Auto-generated method stub
		return rawPassword.toString().equals(encodedPassword);
	}

}
```

작성한 사용자 passwordEncoder 를 Bean 으로 등록하고, security 설정에 적용한다.   

```xml
<bean id="customNoOpPasswordEncoder" class="org.example.security.CustomNoOpPasswordEncoder"></bean>
<!-- 생략 -->
	<security:authentication-manager>
		<security:authentication-provider>
 			<security:password-encoder ref="customNoOpPasswordEncoder"/>
		</security:authentication-provider>
	</security:authentication-manager>
```


**<font color='DarkOrange'>스프링 시큐리티 기본 스키마에 맞추어 인증/권한 쿼리 등록하기</font>**   

security-context.xml 에 `user-by-username-query` 와 `authorities-by-username-query` 속성에 스프링 시큐리티에서 필요로 하는 인증/권한 정보를 가져 올 수 있는 쿼리를 등록한다.   

```xml
	<security:authentication-manager>
		<security:authentication-provider>
			<security:password-encoder ref="bcryptPasswordEncoder"/>
			<security:jdbc-user-service data-source-ref="dataSource" 
            users-by-username-query="select userid, userpw, enabled from tbl_member where userid = ?"
			authorities-by-username-query="select userid, auth from tbl_member_auth where userid = ?"/>
		</security:authentication-provider>
	</security:authentication-manager>
```

**<font color='DarkOrange'>테스트 하기</font>**   

Junit Test 를 이용해 사용자 정보와 권한 정보의 덤프 데이터를 등록한다. 그러면 패스워드가 인코딩된 사용자 정보가 입력될 것이다.   

```java
package org.example.security;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration({ "file:src/main/webapp/WEB-INF/spring/root-context.xml",
					  	"file:src/main/webapp/WEB-INF/spring/security-context.xml" })
@Log4j
public class MapperTests {

	@Setter(onMethod_ = @Autowired)
	private PasswordEncoder pwencoder;
	
	@Setter(onMethod_ = @Autowired)
	private DataSource dataSource;
	
//	@Test
//  사용자 정보 등록 
	public void testInsertMember() {
		
		String sql = "insert into tbl_member(userid, userpw, username) values (?, ?, ?);";
		
		for(int i = 0; i < 100; i++) {
			
			Connection con = null;
			PreparedStatement psmt = null;
			try {
				con = dataSource.getConnection();
				psmt = con.prepareStatement(sql);
				
				psmt.setString(2, pwencoder.encode("pw"+i));
				
				if(i<80) {
					psmt.setString(1, "user"+i);
					psmt.setString(3, "일반사용자"+i);
				}else if(i<90) {
					psmt.setString(1, "manager"+i);
					psmt.setString(3, "운영자"+i);
				}else {
					psmt.setString(1, "admin"+i);
					psmt.setString(3, "관리자"+i);
				}
				
				psmt.executeUpdate();
				
			} catch (SQLException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}finally {
				if(psmt != null) {try {psmt.close();} catch(Exception e) {}}
				if(con != null) {try {con.close();}catch(Exception e) {}}
			}
		}
	}
	

    // 권한 정보 등록 
	@Test
	public void testInsertAuth() {
		
		String sql = "insert into tbl_member_auth (userid, auth) values(?, ?);";
		
		for(int i = 0; i < 100; i++) {
			
			Connection con = null;
			PreparedStatement psmt = null;
			try {
				 con = dataSource.getConnection();
				 psmt = con.prepareStatement(sql);
				 
				 if(i<80) {
					 psmt.setString(1, "user"+i);
					 psmt.setString(2, "ROLE_USER");
				 }else if(i<90) {
					 psmt.setString(1, "manager"+i);
					 psmt.setString(2, "ROLE_MEMBER");
				 }else {
					 psmt.setString(1, "admin"+i);
					 psmt.setString(2, "ROLE_ADMIN");
				 }
				 
				 psmt.executeUpdate();
				 
			} catch (SQLException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}finally {
				if (psmt != null) {try{psmt.close();}catch(Exception e) {}}
				if (con != null) {try{con.close();}catch(Exception e) {}}
			}
		};
	}
}

```

탐캣을 실행 시키고, http://localhost:8080/sample/admin 으로 접속하여 유효한 ID와 PW로 로그인한다.   
admin 페이지이므로, 'admin90', 'pw90' 으로 로그인하면 될것이다. 
로그인이 성공하면, 콘솔에 아래와 같이 정상적으로 조회된 정보가 보여진다.      

```
INFO : jdbc.sqltiming - select userid, userpw, enabled from tbl_member where userid = 'admin90' 
...
INFO : jdbc.resultsettable - 
|--------|-------------------------------------------------------------|--------|
|userid  |userpw                                                       |enabled |
|--------|-------------------------------------------------------------|--------|
|admin90 |$2a$10$P42FSZc7yaZBkgrH0HwKyOBzUqiS7aVt2FyFuBPCUcQ/JDsSklKcK |true    |
|--------|-------------------------------------------------------------|--------|
...
INFO : jdbc.sqltiming - select userid, auth from tbl_member_auth where userid = 'admin90' 
...
INFO : jdbc.resultsettable - 
|---------|-----------|
|userid   |auth       |
|---------|-----------|
|[unread] |ROLE_ADMIN |
|---------|-----------|
```