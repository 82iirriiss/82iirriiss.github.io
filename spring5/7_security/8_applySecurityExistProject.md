---
sort: 8
---

# 기존 프로젝트에 시큐리티 접목

## <font color='blue'>로그인 페이지 처리</font>   

**체크리스트**
- JSTL/시큐리티 태그 사용하도록 선언
- CSS/JS 파일의 링크를 `절대경로`로 설정 (/resources/css/....)
- <form>  태그내의 input 태그의 name 속성을 스프링 시큐리티에 맞게 수정
- CSRF 토큰 항목 추가
- JavaScript 통한 로그인 처리

**customLogin.jsp**

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ taglib uri="http://www.springframework.org/security/tags" prefix="sec" %>
<!DOCTYPE html>
<html>
<head>

    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta name="description" content="">
    <meta name="author" content="">

    <title>Login</title>

    <!-- Custom fonts for this template-->
    <link href="/resources/vendor/fontawesome-free/css/all.min.css" rel="stylesheet" type="text/css">
    <link
        href="https://fonts.googleapis.com/css?family=Nunito:200,200i,300,300i,400,400i,600,600i,700,700i,800,800i,900,900i"
        rel="stylesheet">

    <!-- Custom styles for this template-->
    <link href="/resources/css/sb-admin-2.min.css" rel="stylesheet">

</head>

<body class="bg-gradient-primary">

    <div class="container">

        <!-- Outer Row -->
        <div class="row justify-content-center">

            <div class="col-xl-10 col-lg-12 col-md-9">

                <div class="card o-hidden border-0 shadow-lg my-5">
                    <div class="card-body p-0">
                        <!-- Nested Row within Card Body -->
                        <div class="row">
                            <div class="col-lg-6 d-none d-lg-block bg-login-image"></div>
                            <div class="col-lg-6">
                                <div class="p-5">
                                    <div class="text-center">
                                        <h1 class="h4 text-gray-900 mb-4">Welcome Back!</h1>
                                    </div>
                                    <form class="user" method="post" action="/login">
                                        <div class="form-group">
                                            <input type="text" class="form-control form-control-user"
                                                id="username" name="username" aria-describedby="emailHelp"
                                                placeholder="Enter username...">
                                        </div>
                                        <div class="form-group">
                                            <input type="password" class="form-control form-control-user"
                                                id="password" name="password" placeholder="Password">
                                        </div>
                                        <div class="form-group">
                                            <div class="custom-control custom-checkbox small">
                                                <input type="checkbox" class="custom-control-input" id="customCheck" name="remember-me">
                                                <label class="custom-control-label" for="customCheck">Remember
                                                    Me</label>
                                            </div>
                                        </div>
                                        
                                   		<input type="hidden" name="${_csrf.parameterName}" value="${_csrf.token}"/>
                                        <input type='submit' class="btn btn-primary btn-user btn-block" value="Login">
                                      
                                       <!--  <hr>
                                        <a href="index.html" class="btn btn-google btn-user btn-block">
                                            <i class="fab fa-google fa-fw"></i> Login with Google
                                        </a>
                                        <a href="index.html" class="btn btn-facebook btn-user btn-block">
                                            <i class="fab fa-facebook-f fa-fw"></i> Login with Facebook
                                        </a> -->
                                    </form>
                                    <hr>
                                    <!-- <div class="text-center">
                                        <a class="small" href="forgot-password.html">Forgot Password?</a>
                                    </div>
                                    <div class="text-center">
                                        <a class="small" href="register.html">Create an Account!</a>
                                    </div> -->
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

            </div>

        </div>

    </div>

    <!-- Bootstrap core JavaScript-->
    <script src="/resources/vendor/jquery/jquery.min.js"></script>
    <script src="/resources/vendor/bootstrap/js/bootstrap.bundle.min.js"></script>

    <!-- Core plugin JavaScript-->
    <script src="/resources/vendor/jquery-easing/jquery.easing.min.js"></script>

    <!-- Custom scripts for all pages-->
    <script src="/resources/js/sb-admin-2.min.js"></script>

</body>

</html>
```

## <font color='blue'>게시판 스프링 시큐리티 처리</font>

**<font color='green' style='font-size:large;'>스프링 시큐리티 적용시 한글 처리</font>**
> 필터의 순서에 따라 한글이 깨지는 경우가 발생한다.   
> web.xml 에서 한글인코딩 필터와 시큐리티 필터의 순서에 주의한다. (인코딩 필터 먼저.)

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

	<filter>
		<filter-name>springSecurityFilterChain</filter-name>
		<filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
	</filter>
	
	<filter-mapping>
		<filter-name>springSecurityFilterChain</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
 ```

**<font color='green' style='font-size:large;'>authentication-success-handler-ref 속성 제거</font>**
- 별도로 authentication-success-handler-ref 를 지정하지 않으면, 로그인 성공 시, 사용자가 요청했던 페이지를 보여준다. 디폴트 처리가 `SavedRequestAwareAuthenticationSuccessHandler` 클래스 이용하기 때문이다.

**security-context.xml**

```xml
<security:http>
 <!-- 생략 -->
	<!-- <security:logout logout-url="/customLogout" invalidate-session="true" delete-cookies="remember-me, JSESSIONID" success-handler-ref="customLogoutSuccessHandler"/> -->
	<security:logout logout-url="/customLogout" invalidate-session="true" delete-cookies="remember-me, JSESSIONID" />
	<security:remember-me data-source-ref="dataSource" token-validity-seconds="604800"/>
<!-- 생략 -->
</security:http>
```   

**SecurityConfig**

```java
		http.formLogin()
		.loginPage("/customLogin")
		.loginProcessingUrl("/login");
//		.successHandler(loginSuccessHandler());
```

**<font color='green' style='font-size:large;'>게시물 작성</font>**

BoardController 메서드 일부에 시큐리티 어노테이션 추가한다.   
`isAuthenticated()` 표현식은 어떤 사용자든 로그인이 성공한 사용자만 기능을 사용할 수 있도록 처리한다.   

```java
package org.example.controller;

@Controller
@RequestMapping("/board/*")
@Log4j
@AllArgsConstructor
public class BoardController {

	private BoardService service;

	@PreAuthorize("isAuthenticated()")
	@GetMapping("/register")
	public void register() {
		
	}
	
	@PreAuthorize("isAuthenticated()")
	@PostMapping("/register")
	public String register(BoardVO vo, RedirectAttributes rttr) {
		service.register(vo);
		rttr.addFlashAttribute("result", "success");
		return "redirect:/board/list";
	}
	// 생략
}
```

화면에서 게시물 작성 시, 로그인한 사용자의 아이디가 출력되도록 한다.   
1. 시큐리티 태그를 선언한다 (`<%@ taglib uri="http://www.springframework.org/security/tags" prefix="sec" %>`).
2. `<sec:authentication property="principal.username" />` 태그를 사용한다.
3. CSRF 토큰을 설정한다 ( `<input type="hidden" name="${_csrf.parameterName}" value="${_csrf.token}"/>`)

**register.jsp**

```jsp
<%@ taglib uri="http://www.springframework.org/security/tags" prefix="sec" %>
<!-- 생략 -->
<form role="form" action="/board/register" method="post">
<!-- 생략 -->
    <div class="form-group">
        <label>Writer</label><input class="form-control" name="writer" value='<sec:authentication property="principal.username" />' readonly="readonly"/>
    </div>
    <input type="hidden" name="${_csrf.parameterName}" value="${_csrf.token}"/>
    <button type="submit" class="btn btn-default">Submit Button</button>
    <button type="reset" class="btn btn-default">Reset Button</button>
</form> 
```

**<font color='green' style='font-size:large;'>게시물 조회화면</font>**   

게시물 조회 화면에서 게시글 작성자와 로그인한 사용자가 일치 할 때만, 수정/삭제가 가능하도록 처리한다.  
게시물 댓글 추가버튼은, 로그인한 사용자만 이용할 수 있도록 처리한다.   


**/board/get.jsp**

```jsp
<%@ taglib uri="http://www.springframework.org/security/tags" prefix="sec" %>

<!-- 생략 -->

<sec:authentication property="principal" var="userInfo"/>
<sec:authorize  access="isAuthenticated()">
    <c:if test="${board.writer eq userInfo.username}">
        <button data-oper='modify' class="btn btn-primary">Modify</button>
    </c:if>
</sec:authorize>
<button data-oper='list' class="btn btn-info">List</button>
<!-- 생략 -->
 <!-- 댓글처리 START-->
        <div class="col-lg-12 mt-3">
            <div class="card">
                <div class="card-header">
                    <i class="fa fa-comments fa-fw"></i>Reply
                    <sec:authorize access="isAuthenticated()">
                    <button id='addReplyBtn' class='btn btn-primary btn-sm float-right'>New Reply</button>
                    </sec:authorize>
                </div>
<!-- 생략 -->
```

**<font color='green' style='font-size:large;'>게시물 수정/삭제</font>**   

1. 로그인한 사용자만 접근 가능하며, 작성자와 로그인 사용자가 같은 경우에만 수정/삭제가 가능해야 한다.   
2. Url을 조작해서 수정/삭제가 가능하므로 jsp에 CSRF 토큰을 설정하고 Controller 에도 인증을 체크한다.   

**modify.jsp**

```jsp
<%@ taglib uri="http://www.springframework.org/security/tags" prefix="sec" %>
<!-- 생략 -->
<div class="panel-body">
    <form action='/board/modify' method='post'>
 <!-- 생략 -->
        <sec:authentication property="principal" var="userInfo"/>
        <sec:authorize access="isAuthenticated()">
        <c:if test="${userInfo.username eq board.writer}">
            <button data-oper='modify' class="btn btn-info">Modify</button>
            <button data-oper='remove' class="btn btn-danger">Remove</button>
        </c:if>
        </sec:authorize>
        
        <button data-oper='list' class="btn btn-primary">List</button>
        
        <input type='hidden' name="pageNumber" value="${criteria.pageNumber}"/>
        <input type='hidden' name="amount" value="${criteria.amount}"/>
        <input type='hidden' name="rowindex" value="${criteria.rowindex}"/>
        <input type='hidden' name='type' value='${criteria.type}'/>
        <input type='hidden' name='keyword' value='${criteria.keyword}'/>
        <input type='hidden' name='${_csrf.parameterName}' value='${_csrf.token}'/>
    </form>
</div>
```

**BoardController.java**   
delete()는, writer를 파라미터로 추가하여 @PreAuthorize의 표현식에서 #writer로 사용한다.   
modify()는, 이미 BoardVO를 파라미터로 받고 있으므로 @PreAuthorize의 표현식에서 #vo.writer로 사용한다.   


```java
package org.example.controller;

@Controller
@RequestMapping("/board/*")
@Log4j
@AllArgsConstructor
public class BoardController {

	private BoardService service;
	@PreAuthorize("principal.username == #vo.writer")
	@PostMapping("/modify")
	public String modify(Criteria cri, BoardVO vo, RedirectAttributes rttr) {
		if( service.modify(vo)) {
			rttr.addFlashAttribute("result","success");
		}
		return "redirect:/board/list" + cri.getListLink();
	}

	
	//삭제는 반드시, post 로만 처리합니다.
	@PreAuthorize("principal.username == #writer")
	@PostMapping("/remove")
	public String delete(Criteria cri, BoardVO vo, RedirectAttributes rttr, String writer) {
		
		if(service.remove(vo.getBno())) {
			rttr.addFlashAttribute("result","success");

		}
		return "redirect:/board/list" + cri.getListLink();
	}
}
```

## <font color='blue'>게시판 Ajax 스프링 시큐리티 처리</font>
> Ajax로 Post, put, patch, delete 같은 방식을 데이터 전송하는 경우 반드시 추가적으로 'X-CSRF-TOKEN' 헤더정보를 추가해서 `CSRF` 토큰값을 전달해야 한다.    
> (1) javascript 파일의 상단에 csrf 헤더이름과 값을 선언하고 Ajax의 `beforeSend`를 이용해서 추가적인 헤더를 지정한 후 전송하는 방법(파일첨부시에는 이 방법을 사용한다.)   
> (2) jQuery를 이용해서 CSRF TOKEN을 보내는 것을 기본으로 지정해서 사용하는 방법 ( 보통의 CRUD에는 이 방법이 더 편하다.)   

**예제 (1)**   

```js
var csrfHeaderName = "${_csrf.headerName}";
var csrfTokenValue = "${_csrf.token}";

$.ajax({
    url: '/upload',
    processData: false,
    contentType: false,
    beforeSend : function(xhr) {
        xhr.setRequestHeader(csrfHeaderName, csrfTokenValue);
    },
    data: formData,
    type: 'post',
    dytaType: 'json',
    success: function(result){
        console.log(result);
    }
});
```

**예제 (2)**

```js
$(document).ajaxSend(function(e, xhr, options){
    xhr.setRequestHeader(csrfHeaderName, csrfTokenValue);
});
// 생략, CSRF를 위한 별도의 처리를 하지 않아도 됨. 
```

**<font color='green' style='font-size:large;'>첨부파일 수정/삭제</font>**   

**<font color='green' style='font-size:large;'>댓글등록</font>**    

**get.jsp**   

$(document).ready() 안에 아래와 같이 csrf 설정을 한다.   
시큐리티 태그로 로그인 사용자를 댓글등록 폼에 입력한다.   

```js
var csrfHeaderName = '${_csrf.headerName}';
var csrfTokenValue = '${_csrf.token}';
    
$(document).ajaxSend(function(e, xhr, options){
    xhr.setRequestHeader(csrfHeaderName, csrfTokenValue);
});

 var replyer = null;
	 
	 <sec:authorize access="isAuthenticated()">
	 	replyer = '<sec:authentication property="principal.username"/>';
	 </sec:authorize>

$('#addReplyBtn').on('click', function(){
		 modal.find('input').val('');
		 modal.find('input[name="replyer"]').val(replyer).attr('readonly','readonly');
		 modalInputReplyDate.closest("div").hide();
		 modal.find('button[id != "modalCloseBtn"]').hide();
		 
		 modalRegisterBtn.show();
		 
		 $('#replyModal').modal('show');
	 })
```

**ReplyController.java**    

로그인한 사용자만 댓글을 달 수 있도록, `@PreAuthorize("isAuthenticated()")` 을 추가한다.   

```java
@RestController
@RequestMapping("/reply")
@Log4j
public class ReplyCotroller {

	@Autowired
	private ReplyService service;
	
	
	@PostMapping(value="/new", 
				consumes = "application/json" ,
				produces = {MediaType.TEXT_PLAIN_VALUE})
	@PreAuthorize("isAuthenticated()")
	public ResponseEntity<String> register(@RequestBody ReplyVO vo){
		
		return service.register(vo) ? new ResponseEntity<>("success", HttpStatus.OK) : new ResponseEntity<>(HttpStatus.INTERNAL_SERVER_ERROR);
	}
}
```


**<font color='green' style='font-size:large;'>댓글삭제</font>**    

로그인한 사용자가 자신이 등록한 댓글만 삭제가 가능하도록 해야한다.   
Controller로 댓글번호외에 추가적으로, 작성자 정보도 전송해서 Controller 에서도 작성자 확인 작업을 해야 한다.   

**get.jsp**

```js
$(document).ready(function(){
    // 생략
    var csrfHeaderName = '${_csrf.headerName}';
	var csrfTokenValue = '${_csrf.token}';
	  
	$(document).ajaxSend(function(e, xhr, options){
		xhr.setRequestHeader(csrfHeaderName, csrfTokenValue);
	});
    // 생략
     var replyer = null;
	 
	 <sec:authorize access="isAuthenticated()">
	 	replyer = '<sec:authentication property="principal.username"/>';
	 </sec:authorize>
    //  생략
     modalRemoveBtn.on('click', function(e){
		 
		 //로그인 안한 상태 
		 if(!replyer){
			alert("로그인 후 삭제가 가능합니다.");
			modal.modal('hide');
		 }
		
		 //로그인한 사람과 작성자가 다른 상태 
		 var originalReplyer = modalInputReplyer.val();
		
		 if(replyer != originalReplyer){
			 alert('자신이 작성한 댓글만 삭제가 가능합니다.');
			 modal.modal('hide');
			 return;
		 }
		 
		 replyService.remove(modal.data('rno'), originalReplyer, function(result){
			 if(result==='success')alert('댓글이 삭제되었습니다.');
			 modal.modal('hide');
			 showList(1);
		 })
	 })

}
```

**reply.js**   

```js
//댓글삭제
	function remove(rno, replyer, callback, error){
	
		$.ajax({
			url : '/reply/'+ rno,
	 		type : 'delete',
	 		data : JSON.stringify({rno:rno, replyer:replyer}),
             // contentType에  json으로 보낼것을 꼭 명시해야 함.
	 		contentType : "application/json; charset=utf-8",
			success : function(result,status,xhr){
				if(callback) callback(result);
			},
			error : function(xhr,status,err){
				if(error) error(err);
			}
		});
	}
```

**ReplyController.java**   

로그인한 사람과 작성자가 일치하는지를 체크해야 한다.   


```java
	@PreAuthorize("principal.username == #vo.replyer")
	@DeleteMapping( value ="/{rno}",
					consumes = {MediaType.APPLICATION_JSON_VALUE},
					produces = {MediaType.TEXT_PLAIN_VALUE})
	public ResponseEntity<String> remove(@RequestBody ReplyVO vo, @PathVariable("rno") Long rno){
		
		return  service.remove(rno) ? new ResponseEntity<>("success", HttpStatus.OK) : new ResponseEntity<>(HttpStatus.INTERNAL_SERVER_ERROR); 
	}
```
**<font color='green' style='font-size:large;'>댓글수정</font>**   

로그인한 사용자가 자신이 등록한 댓글만 수정이 가능하도록 해야한다.   
Controller 에서도 작성자와 로그인한 사용자가 같은지 확인 작업을 해야 한다.   

**get.jsp**

```js
$(document).ready(function(){
    // 생략
    var csrfHeaderName = '${_csrf.headerName}';
	var csrfTokenValue = '${_csrf.token}';
	  
	$(document).ajaxSend(function(e, xhr, options){
		xhr.setRequestHeader(csrfHeaderName, csrfTokenValue);
	});
    // 생략
     var replyer = null;
	 
	 <sec:authorize access="isAuthenticated()">
	 	replyer = '<sec:authentication property="principal.username"/>';
	 </sec:authorize>
    //  생략
	 modalModBtn.on('click', function(e){
		 //로그인 안한 상태 
		 if(!replyer){
			alert("로그인 후 삭제가 가능합니다.");
			modal.modal('hide');
			return;
		 }
		
		 //로그인한 사람과 작성자가 다른 상태 
		 var originalReplyer = modalInputReplyer.val();
		
		 if(replyer != originalReplyer){
			 alert('자신이 작성한 댓글만 수정 가능합니다.');
			 modal.modal('hide');
			 return;
		 }
		 
		 if(!modalInputReply.val()){
			 alert('댓글을 입력하세요.');
			 return;
		 }
		 const data = {
				 rno:modal.data('rno'),
				 reply:modalInputReply.val(),
				 replyer:originalReplyer};
		 replyService.modify(data, function(result){
			 alert('댓글이 수정되었습니다.');
			 modal.modal('hide');
			 showList(1);
		 });
	 });
}
```

**ReplyController.java**

```java
	@PreAuthorize("principal.username == #vo.replyer")
	@RequestMapping( method = {RequestMethod.PATCH, RequestMethod.PUT},
					 value = "/{rno}",
					 consumes = {MediaType.APPLICATION_JSON_VALUE},
					 produces = {MediaType.TEXT_PLAIN_VALUE})
	public ResponseEntity<String> modify(@PathVariable("rno") Long rno, @RequestBody ReplyVO vo){
		vo.setRno(rno);
		return service.modify(vo) ? new ResponseEntity<>("success", HttpStatus.OK ) : new ResponseEntity<>(HttpStatus.INTERNAL_SERVER_ERROR);
	}
```