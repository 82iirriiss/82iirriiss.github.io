---
sort : 2
---

# 로그인과 로그아웃 처리 (security-context 설정과 로그인 관련 Handler 사용법)

## <font color='blue'>1. 접근제한 설정</font>
. security-context.xml 에 설정   
. `security:intercept-url` 네임스페이스 사용

```xml
<!-- 생략 -->
	<security:http>
		<security:intercept-url pattern="/sample/all" access="permitAll"/>
		<security:intercept-url pattern="/sample/member" access="hasRole('ROLE_MEMBER')"/>
		<security:intercept-url pattern="/sample/admin" access="hasRole('ROLE_ADMIN')"/>
	</security:http>
<!-- 생략 -->
```

**<font color='green' style='font-size:large;'># access 속성을 표현하는 두가지 방법</font>**

1. 표현식 : 위와 같이 기본설정이 표현식이다.
2. 권한명 의미하는 문자열

```xml
<security:http auto-config="true" use-expressions="false">
    <security:intercept-url pattern="/sample/member" access="ROLE_MEMBER"/>
    <security:intercept-url pattern="/sample/manager" access="ROLE_MANAGER"/>
    <security:intercept-url pattern="/sample/admin" access="ROLE_ADMIN"/>
</security:http>
```

## <font color='blue'>2. 사용자 접근 에러 메시지 처리</font>
> 권한이 없는 사용자가 페이지에 접근했을 때, 에러 페이지를 보여주는 페이지   

테스트를 위해서 사용자 정보를 하드코딩 한 후, 알아보자.   

. security-context.xml

```xml
<!-- 생략 -->
<security:authentication-manager>
        <security:authentication-provider>
            <security:user-service>
                <security:user name="member" password="{noop}member" authorities="ROLE_MEMBER"/>
                <security:user name="admin" password="{noop}admin" authorities="ROLE_MEMBER, ROLE_ADMIN"/>
            </security:user-service>
        </security:authentication-provider>
    </security:authentication-manager>
<!-- 생략 -->
```

스프링5에서는 `PasswordEncoder`를 꼭 사용해야 하지만, 지금은 테스트를 위해서 PasswordEncoder를 사용하지 않고 사용자들 등록할 수 있도록 'password'앞에 `'{noop}'`을 붙여준다.

권한이 없는 사용자가 페이지 접근시 에러메시지를 설정하는 방법에는 두가지가 있다.  
    
**첫번째는**, security-context.xml의 `security:access-denied-handler` 네임스페이스의 `error-page` 속성을 이용하는 방법이다.

. security-context.xml에 속성을 추가한다.

```xml
<security:http>
    <!-- 생략 -->
    <security:access-denied-handler error-page="/accessError"/>
</security:http>
```

. CommonController 에 `/accessError` 링크에 대한 처리 메소드를 작성한다.

```java
package org.example.controller;

@Controller
@Log4j
public class CommonController {

    @GetMapping("/accessError")
    public void accessDenied(Authentication auth, Model model) {
        log.info("access Denied : " + auth);
        model.addAttribute("msg", "Access Denied!");
    }
}
```

. '/accessError' 의 뷰 'views/accessError.jsp' 를 작성한다.  

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ taglib uri="http://www.springframework.org/security/tags" prefix="sec" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
    <h1>Access Denied Page</h1>
    <h2><c:out value="${SPRING_SECURITY_403_EXCEPTION.getMessage()}"/></h2>
    <h2><c:out value="${msg}"/></h2>
</body>
</html>
```
------------------------------------
**두번째는**, security-context.xml 의 `ref` 속성을 이용한다.   
접속거부를 담당하는 `AccessDeniedHandler 인터페이스`를 구현하는 구현체 `CustomAccessDeniedHandler`를 작성해 Bean 으로 등록 후,   
`security:access-denied-handler` 네임스페이스의 ref 속성에 구현체의 id를 입력한다.

```xml
<bean id="customAccessDenied" class="org.example.security.CustomAccessDeniedHandler"></bean> 
<security:http>
    <!-- 생략 -->
    <security:access-denied-handler ref="customAccessDenied"/>  
</security:http>
```

빈으로 등록했던 구현체 `CustomAccessDeniedHandler.java`를 `org.example.security` 패키지에 작성한다.   

```java
package org.example.security;

@Log4j
public class CustomAccessDeniedHandler implements AccessDeniedHandler{

    @Override
    public void handle(HttpServletRequest request, HttpServletResponse response,
            AccessDeniedException accessDeniedException) throws IOException, ServletException {
        // TODO Auto-generated method stub
        log.error("Access Denied Handler");
        log.error("Redirect...");
            
        response.sendRedirect("/accessError");
    }
}
```

나머지 Controller와 뷰 코딩은 첫번째와 동일하다.

## <font color='blue'>3. 커스텀 로그인 페이지</font>
> 기본적으로 스프링이 로그인 폼을 제공하고, 처리도 해준다.   하지만 사용자가 원하는 폼과 서비스를 위해 커스터마이징 할 수 있다.   

. 로그아웃 기능이 아직 미구현이므로, 로그아웃 상태를 만들기 위해 브라우저 개발도구 > Application > Cookie > SESSIONID 를 삭제한다.   

`security-context.xml`의 `security:form-login` 네임스페이스에 직접 로그인 URL을 지정할 수 있다.   

```xml
<security:http>
<!-- 생략 -->
    <!-- form-login 네임스페이스에 로그인 처리할 링크를 지정한다. -->
    <security:form-login login-page="/customLogin" authentication-success-handler-ref="customLoginSuccessHandler"/>
<!-- 생략 -->	
</security:http>
```

`login-page`에 연결된 URL을 코딩한다.

```java
package org.example.controller;

import lombok.extern.log4j.Log4j;

@Controller
@Log4j
public class CommonController {

	@GetMapping("/accessError")
	public void accessDenied(Authentication auth, Model model) {
		log.info("access Denied : " + auth);
		model.addAttribute("msg", "Access Denied!");
	}
	
    // 커스텀 로그인 페이지 
	@GetMapping("/customLogin")
	public void customLogin(String error, String logout, Model model) {
		
		log.info("error:" + error);
		log.info("logout:" + logout);
		
		if(error != null) {
			model.addAttribute("error", "Login Error check Your Account");
		}
		
		if(logout != null) {
			model.addAttribute("logout", "Logout!!");
		}
	}
}
```

로그인 뷰 페이지 `views/customLogin.jsp`를 작성한다.   

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ taglib uri="http://www.springframework.org/security/tags" prefix="sec" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>Custom Login Page</h1>
	<h2><c:out value="${error}"/></h2>
	<h2><c:out value="${logout}"/></h2>
	
	<form method="post" action="/login">
		<div>
			<input type="text" name="username" value="admin">
		</div>
		<div>
			<input type="password" name="password" value="admin">
		</div>
		<div>
			<input type="submit">
		</div>
		<input type="hidden" name="${_csrf.parameterName}" value="${_csrf.token}"/>
	</form>
</body>
</html>
```

. 요청방식은 꼭 `post`로 지정할 것! (CSRF 토큰 함께 보내야 함)  

. 요청 주소는 꼭 `/login` 으로 할 것! ( 스프링 내부로직 호출)   

. `${_csrf.token}` 구문 꼭 함께 전송 할 것!   



## <font color='blue'>4. 로그인 성공과 AuthenticationSuccessHandler</font>

> 로그인 성공 이후에, 특별한 동작을 하도록 제어하고 싶을 때, **`AuthenticationSuccessHandler`** 인터페이스를 구현해서 설정 할 수 있다.   

(1) `AuthenticationSuccessHandler`을 구현한 `CustomLoginSuccessHandler` 클래스 작성한다.

```java
package org.example.security;

@Log4j
public class CustomLoginSuccessHandler implements AuthenticationSuccessHandler{

	@Override
	public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response,
			Authentication auth) throws IOException, ServletException {
		// TODO Auto-generated method stub
		log.warn("Login Success!");
		
		List<String> roleNames = new ArrayList<>();
		auth.getAuthorities().forEach(authority -> {
			roleNames.add(authority.getAuthority());
		});
		
		log.warn("ROLE NAMES:" + roleNames);
		
		if(roleNames.contains("ROLE_ADMIN")) {
			response.sendRedirect("/sample/admin");
			return;
		}
		
		if(roleNames.contains("ROLE_MEMBER")) {
			response.sendRedirect("/sample/member");
			return;
		}
		
		response.sendRedirect("/");
	}
}
```

(2) security-context.xml 에 구현체를 빈으로 등록하고 form-login 속성에 추가한다.   

```xml
<!-- 생략 -->
<bean id="customLoginSuccessHandler" class="org.example.security.CustomLoginSuccessHandler"></bean>
<security:http>
<!-- 생략 -->
    <security:form-login login-page="/customLogin" authentication-success-handler-ref="customLoginSuccessHandler"/>
    <!-- 생략 -->
</security:http>
```

. `CustomLoginSuccessHandler` 클래스 빈으로 등록하고,  `security:form-login` 네임스페이스의 `authentication-success-handler-ref` 속성에 id 를 지정해 준다.   

. 예제는, 로그인이 성공하면, 권한에 따라 특정 페이지로 강제로 이동하는 로직이다.   

## <font color='blue'>5. 로그아웃 처리와 LogoutSuccessHandler</font>
> 특정 URL 을 지정하고, 로그아웃 성공 후, 직접 로직을 핸들링 할 수 있다.   

(1) security-context.xml 에 `security:logout` 네임스페이스에 속성을 지정한다.   

```xml
<bean></bean>
<security:http>
	<!-- 생략 -->
    <security:logout logout-url="/customLogout" invalidate-session="true"/>
    <!-- 생략 -->	
</security:http>
```

. `logout-url` 속성에 로그아웃 처리 URL을 지정할 수 있다.   

. `invalidate-session=true` 는, 로그아웃하면, 세션을 삭제 처리한다.   

(2) 로그아웃 버튼을 생성할 페이지에 로그아웃을 위한 form을 작성한다.   

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta http-equiv='Content-Type' content='text/html; charset=UTF-8'>
<title>Insert title here</title>
</head>
<body>
<h1>/sample/admin page</h1>
<!-- <a href="/customLogout">Logout</a> -->
<form method="post" action="/customLogout">
	<input type="hidden" name="${_csrf.parameterName}" value="${_csrf.token}"/>
	<button>로그아웃</button>
</form>
</body>
</html>
```

. `action`에는, security-context.xml 에서 지정한 `/customLogout` URL 을 지정한다.   

. 전송방식은 꼭 `post` 로 해야하며, `_csrf.token` 을 함께 전송해야 한다.   

. 실제 실행하여 로그아웃 버튼을 누르면, 스프링 내부 로직에 따라 로그인 페이지로 이동하는데 변경하고 싶으면 **`security:logout`의 `logout-success-url` 속성에 로그아웃 후 이동할 URL을 입력**한다.  

. **로그아웃 처리 성공 후, 사용자가 추가적인 로직을 넣고 싶다면**, `LogoutSuccessHandler` 인터페이스를 구현하여 빈에 등록하고,  그 ID 를 `security:logout`의 `success-handler-ref` 속성에 입력하면 된다. (로그인과 비슷하다.)    

. 'logout-success-url' 속성과 'success-handler-ref' 속성은, 둘중 하나만 사용할 수 있다.   