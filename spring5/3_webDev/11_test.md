---
sort: 11
---

# 테스트

## 1. JDBC TEST
- src/test/java 폴더 하위 `org.example.persistance` 패키지 생성
- JDBCTest.java 생성 후 테스트코드 작성

```java
@Log4j
public class JDBCTest {

	static {
		try {
			Class.forName("com.mysql.cj.jdbc.Driver");
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
	
	@Test
	public void testConnection() {
		Connection conn = null;
		try{
			conn = 
				DriverManager.getConnection("jdbc:mysql://localhost/testDb?characterEncoding=UTF-8&serverTimezone=UTC",
						"book_ex",
						"book_ex");
			log.info(conn);
			conn.close();
		} catch (SQLException e) {
			
			fail(e.getMessage());
		} 
	}
}
```   
- `run as` > `JUnit Test` 실행 후, conn 객체가 로그에 찍히는지 확인. 

## 2. DataSource & ConnectionPool TEST
- src/test/java 폴더 하위 `org.example.persistance` 패키지
- DataSourceTest.java 생성 후 테스트코드 작성
```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("file:src/main/webapp/WEB-INF/spring/root-context.xml")
//java 설정의 경우 위의 라인 대신, @ContextConfiguration(classes= {RootConfig.class}) 
@Log4j
public class DataSourceTest {

	@Setter(onMethod = @__({@Autowired}))
	private DataSource ds; 
	
	@Test
	public void testConnection() {
		Connection conn = null;
		try{
			conn = ds.getConnection();
			log.info(conn);
		} catch (Exception e) {
			fail(e.getMessage());
		}
	}
}
```
- `run as` > `JUnit Test` 실행 후, conn 객체가 로그에 찍히는지 확인. 

## 3. 서비스, 영속 계층 테스트


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

## 4. Web Url 테스트

```java
@RunWith(SpringJUnit4ClassRunner.class)
// test for Controller 
@WebAppConfiguration
@ContextConfiguration({
		"file:src/main/webapp/WEB-INF/spring/root-context.xml",
		"file:src/main/webapp/WEB-INF/spring/appServlet/servlet-context.xml"})
@Log4j
public class BoardControllerTests {

	@Setter(onMethod = @__({@Autowired}))
	private WebApplicationContext ctx;
	private MockMvc mockMvc;
	
	@Before
	public void setup() {
		this.mockMvc = MockMvcBuilders.webAppContextSetup(ctx).build();
	}
	
	//@Test
	public void testList() throws Exception {
		log.info("----test List--------");
		log.info(mockMvc.perform(MockMvcRequestBuilders.get("/board/list/"))
				.andReturn()
				.getModelAndView()
				.getModelMap()
				);
	}
	
	//@Test
	public void testRegister() throws Exception {
		
		String resultPage = mockMvc.perform(MockMvcRequestBuilders.post("/board/register")
				.param("title", "컨트롤러 테스트 - 타이틀")
				.param("content","컨트롤러 테스트 - 컨텐츠")
				.param("writer", "user001")
				).andReturn().getModelAndView().getViewName();
		
		log.info("resultPage:"+resultPage);
	}
	
	//@Test
	public void testGet() throws Exception {
		log.info(mockMvc.perform(MockMvcRequestBuilders.get("/board/get")
		.param("bno", "3"))
		.andReturn()
		.getModelAndView()
		.getModelMap());
	}
	
	//@Test
	public void testModify() throws Exception {
		String resultPage = mockMvc.perform(MockMvcRequestBuilders.post("/board/modify")
				.param("bno", "3")
				.param("title","타이틀 수정 테스트")
				.param("content", "컨텐츠 수정 테스트")
				.param("writer", "user003"))
				.andReturn()
				.getModelAndView()
				.getViewName();
		
		log.info(resultPage);
	}
	
	@Test
	public void testRemove() throws Exception {
		String resultPage = mockMvc.perform(MockMvcRequestBuilders.post("/board/remove")
				.param("bno", "3"))
				.andReturn()
				.getModelAndView()
				.getViewName();
		log.info("resultPage::" + resultPage );
	}
}
```