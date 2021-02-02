---
sort: 2
---

# 의존성 주입 / Lombok

## <font color='blue'>1. 의존성 주입</font>

- 스프링의 `ApplicationContext` 가 필요한 객체를 생성해서 주입해주는 방식
- 객체에서 new 클래스명() 식으로 코드로 생성하지 않고 스프링에서 생성된 객체를 제공해주는 방식
- ApplicationContext 가 관리하는 개체들을 `빈` 이라 부름

## <font color='blue'>2. AOP(Aspect Oriented Programming)</font>

- 대부분 시스템이 공통으로 관심을 갖는 보안, 로그, 트랜젝션등을 `횡단 관심사`라 부름
- `AOP`는 횡단 관심사를 모듈로 분리하여 개발하는 프로그램 페러다임

## <font color='blue'>3. 의존성 주입과 테스트를 위한 라이브러리 설정</font>

- 설정 방식은 주로 `XML`이나 `어노테이션` 을 이용

- *pom.xml 에 `spring-test` , `lombok` `추가`하기*

```xml
<!-- spring-test -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>${org.springframework-version}</version>
    <scope>provided</scope>
</dependency>     

<!-- Lombok -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.0</version>
    <scope>provided</scope>
</dependency>     
		
```

- *pom.xml 에 `log4j` , `junit` 버전 `변경` 하기*

```xml
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
    <!-- 생략 -->
</dependency>
<!-- Test -->
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <!-- <version>4.7</version> -->
    <version>4.12</version>
    <scope>test</scope>
</dependency>   
```



## <font color='blue'>4. 의존성 주입을 위한 ConponentScan 설정</font>

**(1) XML 이용한 의존성 주입**
- root-context.xml 클릭 -> 아랫쪽 NameSpaces 탭에서 'context'  체크 -> 소스 추가

```xml
<!-- root-context.xml -->
<context:component-scan base-package="org.test.sample">
</context:component-scan>
```

**(2) JavaConfig 이용한 의존성 주입**

 - `RootConfig.java` 와 `@ComponentScan` 어노테이션 이용
 - ApplicationContext가 @ComponentScan 을 통해 지정된 패키지를 스캔하면서 @Component 가 붙은 클래스들의 인스턴스를 생성함.

 ```java
package org.example.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan(basePackages = {"org.test.sample"})
public class RootConfig {

}
 ```

## <font color='blue'>5. 의존성 주입 테스트</font>

- org.example.sample.Chef.java

```java
package org.example.sample;

import org.springframework.stereotype.Component;

import lombok.Data;

@Component
@Data
public class Chef {

}

```
- @Data : Chef(), canEqual(Object), equals(Objectl), hashCode(), toString() 함수를 자동 생성함

------------------------------------------

- org.example.sample.Restaurant.java

```java
package org.example.sample;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import lombok.Data;
import lombok.Setter;

@Component
@Data
public class Restaurant {

	@Setter(onMethod = @__({@Autowired}) )
	private Chef chef;
}

```
- @Component : Bean으로 등록하고, 스프링이 인스턴스를 자동 생성한다.
- @Data : Restaurant(), chef, setChef(Chef), getChef(), toString() 등의 메소드를 자동 생성한다.

-------------------------------------------

- org.example.sample.SampleTests.java

```java
package org.example.sample;

import static org.junit.Assert.assertNotNull;

import org.example.config.RootConfig;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import lombok.Setter;
import lombok.extern.log4j.Log4j;

@RunWith (SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = {RootConfig.class} )
@Log4j
public class SampleTests {

	@Setter(onMethod = @__({@Autowired}) )
	private Restaurant restaurant;
	
	@Test
	public void testExist() {
		
		assertNotNull(restaurant);
		
		log.info(restaurant);
		log.info("-------------------------------------------");
		log.info(restaurant.getChef());
		
	}
}

```
- @RunWith(SpringJUnit4ClassRunner.class) : 현재 코드가 `스프링을 실행하는 역할` 이라는 것을 표시
- @ContextConfiguration : `ComponentScan` 이 지정되어 있는 설정파일을 셋팅함으로써, 필요한 객체를 Bean으로 등록
    - xml 설정일 경우 : @ContextConfiguration("file:src/main/webapp/WEB-INF/spring/root-context.xml")
    - java 설정일 경우: @ContextConfiguration(classes = {RootConfig.class})
- @Log4j : Lombok 을 이용해서 로그를 기록. 별도의 Logger 객체 선언 없이 사용하면 됨
- @Setter(onMethod = @__({@Autowired}) ) : setRestaurant(Restaurant) 메소드를 생성할 때, 해당 인스턴스(Restaurant)를 자동으로 생성해서 주입해 달라는 표시

## <font color='blue'>6. 코드에 사용된 어노테이션</font>

**(1) Lombok 관련 어노테이션**

- @Setter : setter 메서드를 만들어주는 역할
    - @Setter 의 세가지 속성
        - value: 접근 제한 속성, 기본값은 lombok.AccessLevel.PUBLIC 
        - onMethod: setter 생성 시, setter 메서드에 붙일 어노테이션을 지정하는 역할
        - onParam: setter 메서드 생성 시, 메서드의 파라미터에 어노테이션을 사용할 경우 적용
- @Data
    - @ToString, @EqualsAndHashCode, @Getter/@Setter, @RequiredArgsConstructor를 모두 생성해주는 Lombok 어노테이션
- @Log4j
- @AllArgsConstructor : 모든 인스턴스 변수를 파라미터로 받는 생성자를 자동 생성 (ex. Restaurant(Chef))
- @RequiredArgsConstructor : @NonNull 이나 final이 붙은 인스턴스 변수에 대한 생성자 생성

**(2) Sprint 관련 어노테이션**

- @Autowired : 자신에게 해당 타입의 빈을 주입해주라는 표시(주입: 해당 타입의 빈 인스턴스를 생성해 달라는 의미)
- @Component : 해당 클래스가 스프링에서 객체로 만들어 관리하는 대상임을 명시, @ComponentScan을 통해 @Component가 있는 객체들을 조사함.

**(3) Test 관련 어노테이션**

- @RunWith : 테스트시 필요한 클래스 지정
- @ContextConfiguration : 어떤 설정정보를 읽어들여야 할지 명시
- @Test : 단위테스트 대상에 붙여줌
