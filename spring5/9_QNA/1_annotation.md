---
sort : 1
---

# 어노테이션

## @Autowired
- 자신에게 해당 타입의 빈을 주입해주라는 표시(주입: 해당 타입의 빈 인스턴스를 생성해 달라는 의미)

## @AllArgsConstructor
- Lombok 애노테이션
- 모든 인스턴스 변수를 파라미터로 받는 생성자를 자동 생성 (ex. Restaurant(Chef))

## @RequiredArgsConstructor
- @NonNull 이나 final이 붙은 인스턴스 변수에 대한 생성자 생성

## @Configuration
- 자바 설정파일에 선언
- ex: RootConfig.java

## @ComponentScan
- 의존성 주입을 위해 bean을 검색하기 위해 설정하는 RootConfig.java 에 선언 
- ApplicationContext가 @ComponentScan 을 통해 지정된 패키지를 스캔하면서 @Component 가 붙은 클래스들의 인스턴스를 생성함.
- @ComponentScan(basePackages={"org.test.sample"} )

## @Component
- 해당 클래스가 스프링에서 객체로 만들어 관리하는 대상임을 명시, @ComponentScan을 통해 @Component가 있는 객체들을 조사함.

## @Data
- @ToString, @EqualsAndHashCode, @Getter/@Setter, @RequiredArgsConstructor를 모두 생성해주는 Lombok 어노테이션