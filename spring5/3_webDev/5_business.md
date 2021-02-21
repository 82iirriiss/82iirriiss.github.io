---
sort: 5
---

# 비지니스 계층 (Service) 구현
- 고객의 요구사항 반영
- 서비스 객체 하나에서는, 여러개의 persistence 객체를 조합하여 사용하기도 한다.
- 계층간의 연결은 인터페이스를 사용하여 느슨한 연결을 한다.
- <font color='red'>Persistence 계층은, 개발자 중심의 용어를 사용하지만 Business 계층은 고객 친화적인 용어를 사용한다. </font>

1. `org.example.service`  패키지 생성 - 하위에 `BoardService.java` 인터페이스 생성
2. `org.example.service.impl` 패키지 생성 - 하위에 `BoardServiceImpl.java` 구현 클래스 생성

## BoardService 인터페이스 생성

```java
public interface BoardService {

	public void register(BoardVO vo);
	
	public BoardVO get(Long bno);
	
	public boolean modify(BoardVO vo);
	
	public boolean remove(Long bno);
	
	public List<BoardVO> getList();
}
```

## BoardSerciceImpl 구현 클래스 생성

- <font color='red'>@Service 어노테이션 중요!</font>
- BoardServiceImpl(BoardMapper) 생성자를 만들어 주기 위해, @AllArgsConstructor 추가 ( @Setter(onMethod=@__({@Autowired})) 를 해 주어도 됨. )

```java
@Log4j
@Service
@AllArgsConstructor
public class BoardServiceImpl implements BoardService{

    // mapper 주입
	@Autowired
	private BoardMapper mapper;
	
	@Override
	public void register(BoardVO vo) {
		// TODO Auto-generated method stub
		log.info("register........." + vo);
		mapper.insert(vo);
		
	}

	@Override
	public BoardVO get(Long bno) {
		// TODO Auto-generated method stub
		log.info("get..........." + bno);
		return mapper.read(bno);
	}

	@Override
	public boolean modify(BoardVO vo) {
		// TODO Auto-generated method stub
		log.info("modify..........." + vo);
		return mapper.update(vo)==1;
	}

	@Override
	public boolean remove(Long bno) {
		// TODO Auto-generated method stub
		log.info("remove..........." + bno);
		return mapper.delete(bno)==1;
	}

	@Override
	public List<BoardVO> getList() {
		// TODO Auto-generated method stub
		log.info("getList..................");
		return mapper.getList();
	}


}
```

## 서비스 객체를 Bean 으로 등록 (ComponentScan)

- root-context.xml

```xml
<!-- context 네임스페이스 추가 -->
<beans xmlns:context="http://www.springframework.org/schema/context">

    <!-- 컴포넌트 스캔 추가 -->
	<context:component-scan base-package="org.example.service" />
```

- RootConfig.java

```java
@Configuration
// ComponentScan 추가
@ComponentScan(basePackages = {"org.example.service"})
@MapperScan(basePackages = {"org.example.mapper"})
public class RootConfig {
	// 생략
}

```

## 테스트

- `src/test/java` 폴더 하위 `org.example.service` 패키지 생성 - BoardServiceTests.java 생성

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("file:src/main/webapp/WEB-INF/spring/root-context.xml")
//@ContextConfiguration(classes={RootConfig.class})
@Log4j
public class BoardServiceTests {

	@Autowired
	private BoardService service;
	
	@Test
	public void testExist() {
		log.info(service);
		assertNotNull(service);
	}
	
	@Test
	public void testRegister() {
		BoardVO vo = BoardVO.builder()
				.title("타이틀-서비스 테스트")
				.content("컨텐츠 - 서비스 테스트")
				.writer("user02")
				.build();
		
		service.register(vo);
	}
	
	@Test
	public void testGet() {
		log.info(service.get(10L));
	}
	
	@Test
	public void testModify() {
		BoardVO vo = BoardVO.builder()
				.bno(10L)
				.title("타이틀-서비스 테스트 업데이트 ")
				.content("컨텐츠 - 서비스 테스트 업데이트 ")
				.writer("user03")
				.build();
		log.info("modify test Result:" + service.modify(vo));
	}
	
	@Test
	public void testRemove() {
		log.info("remove test Result::" + service.remove(10L));
	}
	
	@Test
	public void testGetList() {
		service.getList().forEach(board -> log.info(board));
	}
```

