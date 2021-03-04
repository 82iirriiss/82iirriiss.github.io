---
sort : 6
---

# 자동로그인 (remember-me)
> 최근 웹페이지들은 '자동로그인', '로그인 기억하기' 라는 이름으로 한번 로그인하면 일정 시간 동안 다시 로그인을  하지 않아도 되는 기능을 구현하고 있다.   
> 스프링 시큐리티는 ①메모리상에서 처리 ,②데이터베이스 이용하여 간단한 설정만으로 구현이 가능하다.   
> `<security:remember-me>` 태그를 이용하여 설정한다.   

**<font color='green' style='font-size:large;'>1. remember-me 태그의 속성들</font>**   
- key : 쿠키에 사용되는 값을 암호화하기 위한 키값
- data-source-ref : remember-me 를 데이터베이스를 이용하여 설정할 때 사용하는 DataSource 설정
- remember-me-cookie : 브라우저에 보관되는 쿠키의 이름 지정, 기본은 'remember-me' 
- remember-me-parameter : 화면상에서 remember-me를 설정하기 위한 input의 name
- token-validity-seconds : 쿠키의 유효시간 지정



**<font color='green' style='font-size:large;'>2. 데이터베이스를 이용하여 자동로그인 처리하기</font>**    
> ①지정된 테이블과 지정된 SQL을 사용하는 방법, ②직접 구현하는 방법 두가지가 있다.   
> 여기서는, 지정된 테이블을 사용하는 예제를 다룰 것이다.   

**지정된 테이블 스키마 생성하기**   

```sql
create table persistent_logins (
		username varchar(64) not null,
        series varchar(64) primary key,
        token varchar(64) not null,
        last_used timestamp not null
);
```

**security-context.xml 에 설정 추가하기**

데이터소스만 지정해 주면 된다. 
(` <security:remember-me data-source-ref="dataSource" token-validity-seconds="604800"/>`)

```xml
<security:http>
		<security:intercept-url pattern="/sample/all" access="permitAll"/>
		<security:intercept-url pattern="/sample/member" access="hasRole('ROLE_MEMBER')"/>
		<security:intercept-url pattern="/sample/admin" access="hasRole('ROLE_ADMIN')"/>
		<security:form-login login-page="/customLogin" authentication-success-handler-ref="customLoginSuccessHandler"/>
		<security:logout logout-url="/customLogout" invalidate-session="true" delete-cookies="remember-me, JSESSIONID" success-handler-ref="customLogoutSuccessHandler"/>
		<!-- 여기 -->
        <security:remember-me data-source-ref="dataSource" token-validity-seconds="604800"/>
		<security:access-denied-handler ref="customAccessDenied"/>
	</security:http>
```

**로그아웃 시, 쿠키 제거하기**

security-context.xml 의 &lt;security:logout&gt;태그에 `delete-cookies` 속성을 추가한다.

```xml
<security:http>
		<security:intercept-url pattern="/sample/all" access="permitAll"/>
		<security:intercept-url pattern="/sample/member" access="hasRole('ROLE_MEMBER')"/>
		<security:intercept-url pattern="/sample/admin" access="hasRole('ROLE_ADMIN')"/>
		<security:form-login login-page="/customLogin" authentication-success-handler-ref="customLoginSuccessHandler"/>
        <!-- 여기 -->
		<security:logout logout-url="/customLogout" invalidate-session="true" delete-cookies="remember-me, JSESSIONID" success-handler-ref="customLogoutSuccessHandler"/>
        <security:remember-me data-source-ref="dataSource" token-validity-seconds="604800"/>
		<security:access-denied-handler ref="customAccessDenied"/>
	</security:http>
```
