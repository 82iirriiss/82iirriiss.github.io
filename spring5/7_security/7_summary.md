---
sort : 7
---

# 스프링 시큐리티 설정 요약 

## <font color='blue'>1. XML 설정</font>

**<font color='green'>web.xml</font>**   

```xml
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>/WEB-INF/spring/root-context.xml
		/WEB-INF/spring/security-context.xml</param-value>
	</context-param>
```

**<font color='green'>security-context.xml</font>**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:security="http://www.springframework.org/schema/security"
	xsi:schemaLocation="http://www.springframework.org/schema/security http://www.springframework.org/schema/security/spring-security.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	
	<bean id="customAccessDenied" class="org.example.security.CustomAccessDeniedHandler"></bean>
	<bean id="customLoginSuccessHandler" class="org.example.security.CustomLoginSuccessHandler"></bean>
	<bean id="customLogoutSuccessHandler" class="org.example.security.CustomLogoutSuccessHandler"></bean>
<!-- 	<bean id="customNoOpPasswordEncoder" class="org.example.security.CustomNoOpPasswordEncoder"></bean> -->
	<bean id="bcryptPasswordEncoder" class="org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder"/>
	<bean id="customUserDetailsService" class="org.example.security.CustomUserDetailsService"></bean>
	
	<security:http>
		<security:intercept-url pattern="/sample/all" access="permitAll"/>
		<security:intercept-url pattern="/sample/member" access="hasRole('ROLE_MEMBER')"/>
		<security:intercept-url pattern="/sample/admin" access="hasRole('ROLE_ADMIN')"/>
		<security:form-login login-page="/customLogin" authentication-success-handler-ref="customLoginSuccessHandler"/>
		<security:logout logout-url="/customLogout" invalidate-session="true" delete-cookies="remember-me, JSESSIONID" success-handler-ref="customLogoutSuccessHandler"/>
		<security:remember-me data-source-ref="dataSource" token-validity-seconds="604800"/>
	<!-- 	<security:access-denied-handler error-page="/accessError"/> -->
		<security:access-denied-handler ref="customAccessDenied"/>
	</security:http>
	
	<security:authentication-manager>
		<security:authentication-provider user-service-ref="customUserDetailsService">
			<!-- <security:user-service>
				<security:user name="member" password="{noop}member" authorities="ROLE_MEMBER"/>
				<security:user name="admin" password="{noop}admin" authorities="ROLE_MEMBER, ROLE_ADMIN"/>
			</security:user-service> -->
<!-- 			<security:jdbc-user-service data-source-ref="dataSource"/> -->
<!-- 			<security:password-encoder ref="customNoOpPasswordEncoder"/> -->

			<security:password-encoder ref="bcryptPasswordEncoder"/>
			<!-- <security:jdbc-user-service data-source-ref="dataSource" users-by-username-query="select userid, userpw, enabled from tbl_member where userid = ?"
				authorities-by-username-query="select userid, auth from tbl_member_auth where userid = ?"/> -->
		</security:authentication-provider>
	</security:authentication-manager>

</beans>
```

## <font color='blue'>2. JAVA 설정</font>

**<font color='green'>SecurityInitializer.java</font>**   
`AbstractSecurityWebApplicationInitializer`를 상속받은 `SecurityInitializer` 클래스를 생성하는 것만으로 Filter 설정이 완료된다.   

```java
package org.example.config;

import org.springframework.security.web.context.AbstractSecurityWebApplicationInitializer;

public class SecurityInitializer extends AbstractSecurityWebApplicationInitializer {
	// AbstractSecurityWebApplicationInitializer 를 상속받는 클래스를 추가하는 것 만으로,스프링 내부적으로 DelegatingFilterProxy 를 추가하므로
	// 별도의 구현없이도 필터 설정이 완료됨.
}
```

**<font color='green'>SecurityConfig.java</font>** : `WebSecurityConfigurerAdapter`를 상속받는 클래스를 생성한다.  
`configure(HttpSecurity http)` 에는 `http` 관련 시큐리티 설정을 오버라이드 한다.   
`configure(AuthenticationManagerBuilder auth)` 에는 Provider 관련 시큐리티 설정을 오버라이드 한다.   

```java
package org.example.config;

import javax.sql.DataSource;
@Configuration
@EnableWebSecurity
@Log4j
public class SecurityConfig extends WebSecurityConfigurerAdapter{

	@Autowired
	DataSource dataSource;
	
	@Override
	public void configure(HttpSecurity http) throws Exception{
		/*Http 관련 설정
		 * intercept-url
		 * login/logout
		 * remember-me
		 * */
		
		http.authorizeRequests()
		.antMatchers("/sample/all").permitAll()
		.antMatchers("/sample/admin").access("hasRole('ROLE_ADMIN')")
		.antMatchers("/sample/member").access("hasRole('ROLE_MEMBER')");
		
		http.formLogin()
		.loginPage("/customLogin")
		.loginProcessingUrl("/login")
		.successHandler(loginSuccessHandler());
		
		http.logout()
		.logoutUrl("/customLogout")
		.invalidateHttpSession(true)
		.deleteCookies("remember-me", "JSESSION_ID");
		
		http.rememberMe()
		.key("example")
		.tokenRepository(persistentTokenRepository())
		.tokenValiditySeconds(604800);

	}
	
	
	protected void configure(AuthenticationManagerBuilder auth) throws Exception {
		log.info("configure..........");
		auth.inMemoryAuthentication().withUser("admin").password("{noop}admin").roles("ADMIN");
		auth.inMemoryAuthentication().withUser("member").password("{noop}member").roles("MEMBER");
		
//		String queryUser = "select userid, userpw, enabled from tbl_member where userid = ? ";
//		String queryDetails = "select userid, auth from tbl_member_auth where userid= ? ";
		
		auth.jdbcAuthentication()
		.dataSource(dataSource);
		
//		.passwordEncoder(passwordEncoder())
//		.usersByUsernameQuery(queryUser)
//		.authoritiesByUsernameQuery(queryDetails);
		
		auth.userDetailsService(customUserService())
		.passwordEncoder(passwordEncoder());
	}
	
	@Bean
	public AuthenticationSuccessHandler loginSuccessHandler() {
		return new CustomLoginSuccessHandler();
	}
	
	@Bean
	public PasswordEncoder passwordEncoder() {
		return new BCryptPasswordEncoder();
	}
	
	@Bean
	public UserDetailsService customUserService() {
		return new CustomUserDetailsService();
	}
	
	public PersistentTokenRepository persistentTokenRepository() {
		JdbcTokenRepositoryImpl repo = new JdbcTokenRepositoryImpl();
		repo.setDataSource(dataSource);
		return repo;
	}
}

```

**<font color='green'>WebConfig.java</font>** : getRootConfigClasses()에 `SecurityConfig.class` 추가

```java
package org.example.config;

import javax.servlet.Filter;
import javax.servlet.ServletRegistration.Dynamic;

import org.springframework.web.filter.CharacterEncodingFilter;
import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;

public class WebConfig extends
AbstractAnnotationConfigDispatcherServletInitializer {

	@Override
	protected Class<?>[] getRootConfigClasses() {
		// TODO Auto-generated method stub
		return new Class[] {RootConfig.class, SecurityConfig.class};
	}

	@Override
	protected Class<?>[] getServletConfigClasses() {
		// TODO Auto-generated method stub
		return new Class[] {ServletConfig.class};
	}

	@Override
	protected String[] getServletMappings() {
		// TODO Auto-generated method stub
		return new String[] {"/"};
	}

	@Override
	protected void customizeRegistration(Dynamic registration) {
		// TODO Auto-generated method stub
		registration.setInitParameter("throwExceptionIfNoHandlerFound", "true");
	}
	
	@Override
	protected Filter[] getServletFilters() {
		// TODO Auto-generated method stub
		CharacterEncodingFilter characterEncodingFilter = new CharacterEncodingFilter();
		characterEncodingFilter.setEncoding("UTF-8");
		characterEncodingFilter.setForceEncoding(true);
		
		return new Filter[] {characterEncodingFilter};
	}
}
```

## <font color='blue'>3. 어노테이션 설정 (XML이나 JAVA로 시큐리티 설정을 한 후 부가적으로 사용)</font>

**체크리스트**   
- security-context.xml 추가
- org.example.security 및 이하 패키지 추가
- org.example.domain 내에 MemberVO 와 AuthVO 추가
- web.xml 에서 security-context.xml 설정과 필터 추가
- MemberMapper 인터페이스와 MemberMapper.xml 추가
- org.example.controller 패키지의 CommonController 추가

**<font color='green'>XML 설정의 경우, sevlet-context.xml</font>**   
특이하게 security-context.xml이 아닌 `servlet-context.xml`에 설정을 추가한다.    
먼저 security 네임스페이스(버전번호는 제거)를 추가한 후, 설정을 추가한다.      

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans:beans xmlns="http://www.springframework.org/schema/mvc"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:beans="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:security="http://www.springframework.org/schema/security"
	xsi:schemaLocation="http://www.springframework.org/schema/security http://www.springframework.org/schema/security/spring-security.xsd
		http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd
		http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 생략 -->
	<security:global-method-security pre-post-annotations="enabled" secured-annotations="enabled"/>
</beans:beans>
```

**<font color='green'>Java 설정의 경우, ServletConfig.java</font>**   

```java
package org.example.config;

@EnableWebMvc
@ComponentScan(basePackages = {"org.example.controller"})
@EnableGlobalMethodSecurity(prePostEnabled = true, securedEnabled = true)
public class ServletConfig implements WebMvcConfigurer {
    // 생략
}
```

**<font color="green">예제</font>**

```java
package org.example.controller;

@Controller
@RequestMapping("/sample/*")
@Log4j
public class SecuritySampleController {
    
    // 생략
	
	@PreAuthorize("hasAnyRole('ROLE_ADMIN','ROLE_MEMBER')")
	@GetMapping("/annoMember")
	public void doMember2() {
		
		log.info("logined annotation member");
	}
	
	
	@Secured({"ROLE_ADMIN"})
	@GetMapping("/annoAdmin")
	public void doAdmin2() {
		
		log.info("logined annotation admin");
	}
}

```
