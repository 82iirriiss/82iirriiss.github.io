---
sort : 4
---

# 커스텀 UserDetailsService 활용(최종적으로 사용해야 할 인증방식)

> 앞에서 다룬 과정들이 모두 이번 챕터를 구현하기 위해 알아본 정보였다.   
> 궁극적으로 커스텀 UserDetailsService 를 활용하여 인증/권한 Provider 를 구성하는 것이 좋다.   

```
[ Authentication Manager ]
        ↑
       상속
        ⎮
   [ Provider ]
        ↑
       상속
        ⎮
[ UserDetailsService ] <-- 구현 -- [ CustomUserDetailsService ]
                                         ⎮
                                        리턴
                                         ↓ 
                                   [ UserDetails ]<-- 구현 --[ Users ]
                                                             ↑
                                                ↖︎            상속
                                                상속         ⎮
                                                    ↖︎  [ CustomUsers ]

```

## <font color='blue'>1. 영속계층 개발하기</font>   

**<font color="red">중요한 것은, 스프링 시큐리티에서 username은 사용자의 ID를 의미한다.</font>**

**<font color='green' style='font-size:large;'>테이블스키마</font>**    

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

**<font color='green' style='font-size:large;'>VO 객체 생성</font>**   

. MemberVO   

```java
package org.example.domain;

import java.util.Date;
import java.util.List;

import lombok.Data;

@Data
public class MemberVO {
	
	private String userid;
	private String userpw;
	private String userName;
	private boolean enabled;
	
	private Date regDate;
	private Date updateDate;
	private List<AuthVO> authList;
	
}
```

. AuthVO    

```java
package org.example.domain;

import lombok.Data;

@Data
public class AuthVO {
	
		private String userid;
		private String auth;
}
```

**<font color='green' style='font-size:large;'>MemberMapper 인터페이스 작성</font>**   

```java
package org.example.mapper;

import org.example.domain.MemberVO;

public interface MemberMapper {
	
	 public MemberVO read(String userid);
}
```

**<font color='green' style='font-size:large;'>MemberMapper.xml 작성</font>**   

tbl_member 와 tbl_member_auth 는 1:N 관계이다.   
1:N 관계를 처리할 때는 아래 예제와 같이 **resultMap**을 사용하면 쉽다.   

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
	PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="org.example.mapper.MemberMapper">	
	
	<resultMap type="org.example.domain.MemberVO" id="memberMap">
		<id property="userid" column="userid"/>
		<result property="userid" column="userid"/>
		<result property="userpw" column="userpw"/>
		<result property="userName" column="username"/>
		<result property="regDate" column="regdate"/>
		<result property="updateDate" column="updatedate"/>
		 <collection property="authList" resultMap="authMap" ></collection>
	</resultMap>
	
	<resultMap type="org.example.domain.AuthVO" id="authMap">
		<result property="userid" column="userid"/>
		<result property="auth" column="auth"/>
	</resultMap>
	
	<select id="read" resultMap="memberMap">
	SELECT	MEM.USERID, USERPW, USERNAME, ENABLED, REGDATE, UPDATEDATE, AUTH
	FROM	TBL_MEMBER AS MEM
	LEFT OUTER JOIN	TBL_MEMBER_AUTH AS AUTH 
	ON		MEM.USERID = AUTH.USERID
	WHERE	MEM.USERID = #{userid}
	</select>
</mapper>
```

**<font color='green' style='font-size:large;'>TEST 코드 작성</font>**  

. MemberMapperTests   
. Run Junit 실행 하여, 결과를 확인한다.   

```java
package org.example.mapper;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("file:src/main/webapp/WEB-INF/spring/root-context.xml")
@Log4j
public class MemberMapperTests {

	@Setter(onMethod_= @Autowired )
	private MemberMapper mapper;
	
	@Test
	public void testRead() {
		MemberVO vo = mapper.read("admin90");
	
		log.info(vo);
		vo.getAuthList().forEach(auth -> log.info(auth));
	}
}
```

## <font color='blue'>2. CustomUserDetailsService 구성하기</font>
> MemberMapper 를 CustomUserDetailsService 에 주입한다.   
> CustomUserDetailsService의 유일한 메소드 'loadUserByUsername(String username)' 는 'UserDetails' 객체를 리턴하므로,   
> 우리는 'UserDetails' 인터페이스를  구현한 User 객체를 상속한 CustomUser 에 MemberVO를 담아서 리턴하도록 구현한다.    

**<font color='green' style='font-size:large;'>CustomUser 클래스 작성</font>** 

```java
package org.example.security.domain;

public class CustomUser extends User{
	
	private static final long serialVersionUID = 1L;
	
	// MemberVO 를 주입한다.
    private MemberVO member;

    // 상속을 받기 위해서는 상속자의 생성자를 호출하여야 한다.
	public CustomUser(String username, String password, Collection<? extends GrantedAuthority> authorities) {
		super(username, password, authorities);
	}
	
	public CustomUser(MemberVO vo) {
		super(vo.getUserid(), vo.getUserpw(), vo.getAuthList().stream().
				map(auth -> new SimpleGrantedAuthority(auth.getAuth())).collect(Collectors.toList()));
		
		this.member = vo;
	}
}

```
**<font color='green' style='font-size:large;'>CustomUserDetailsService 클래스 작성</font>**   

```java
package org.example.security;

@Log4j
public class CustomUserDetailsService implements UserDetailsService {

	@Autowired
	private MemberMapper mapper;
	
	@Override
	public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
		// TODO Auto-generated method stub
		
		log.warn("Load User By UserName:" + username );
		
		MemberVO vo = mapper.read(username);
		
		return vo == null? null : new CustomUser(vo);
	}
}

```


**<font color='green' style='font-size:large;'>테스트</font>**

탐캣을 실행하고 http://localhost:8080/sample/admin 으로 접속한다. admin 권한이 있는 사용자가 로그인 하면 admin 화면으로 가고 권한이 없는 사용자가 로그인 하면 홈화면(http://lacalhost:8080/)으로 이동한다.   
