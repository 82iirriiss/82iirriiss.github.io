---
sort: 5
---

# JSP 에서 시큐리티 태그 사용하기

> UserDetails 를 구현한 User 객체를 상속받아 작성한 CustomUser 객체에서 리턴해 주는 정보를 불러올 수 있다.   

**<font color='green' style='font-size:large;'>1. jsp에 시큐리티 태그 지시어 추가</font>**   

```jsp
<%@ taglib uri="http://www.springframework.org/security/tags" prefix="sec" %>
```

**<font color='green' style='font-size:large;'>2. 화면에 사용자 정보 보여주기</font>**   

`sec:authentication` 이라는 태그를 이용하며, `principal` 이라는 속성을 불러와 사용한다.   

```jsp
<p>principal : <sec:authentication property="principal"/></p>
<p>MemberVO : <sec:authentication property="principal.member"/></p>
<p>사용자이름 : <sec:authentication property="principal.member.userName"/></p>
<p>사용자아이디 : <sec:authentication property="principal.username"/></p>
<p>사용자권한리스트 : <sec:authentication property="principal.member.authList"/></p>
```

**<font color='green' style='font-size:large;'>3. 표현식 이용해서 동적화면 구성하기</font>**   

스프링 시큐리티의 표현식은 security-context.xml 에서도 사용할 수 있다.   

- hasRole([ role ]), hasAuthority([ authority ]) : 해당 권한이 있으면 true
- hasAnyRole([role1, role2]), hasAnyAuthority([ authority ]) : 여러 권한들 중 하나라도 해당하는 권한이 있으면 true
- principal : 현재 사용자의 정보
- permitAll : 모든 사용자에게 허용
- denyAll : 모든 사용자에게 거부
- isAnonymous() : 익명의 사용자의 경우 true
- isAuthenticated() : 인증된 사용자면 true
- isFullyAuthenticated() : Remember-me 로 인증된 것이 아닌 인증된 사용자인 경우 true

예시   

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://www.springframework.org/security/tags" prefix="sec" %>

<!DOCTYPE html>
<html>
<head>
<meta http-equiv='Content-Type' content='text/html; charset=UTF-8'>
<title>Insert title here</title>
</head>
<body>
<sec:authorize access="isAnonymous()">
<a href="/customLogin">로그인</a>
</sec:authorize>
<sec:authorize access="isAuthenticated()">
<a href="/customLogout">로그아웃</a>
</sec:authorize>
<h1>/sample/all page</h1>
</body>
</html>
```