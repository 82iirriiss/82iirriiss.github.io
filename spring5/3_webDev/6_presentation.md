---
sort: 6 
---

# 프레젠테이션 계층(Controller) 구현

## <font color='blue'>1. Controller 분석</font>

|Task |URL |MEthod |Parameter |From (입력화면)|URL 이동 |
|=====|====|=======|==========|=====|=========|
|전체목록 |/board/list |GET | | |
|등록 |/board/register |Post |모든 항목 ||list 이동|
|조회 |/board/get |GET |bno | | |
|삭제 |/board/remove |Post |bno |입력화면 |list 이동 |
|수정 |/board/modify |Post |모든 항목 |입력화면 |list 이동 |

## <font color='blue'>2. BoardController 작성</font>

1. `org.example.controller` 패키지 생성 - `BoardController.java` 작성

```java
@Controller
@RequestMapping("/board/*")
@Log4j
@AllArgsConstructor
public class BoardController {

    // @AllArgsConstructor 로 생성자에 BoardService를 주입해 줌.
    // @AllArgsConstructor 대신 @Setter(onmethod=@__({@Autowired})) 로 주입 할 수도 있음.
	private BoardService service;

    //게시글 목록
	@GetMapping("/list")
	public void list(Model model) {
		model.addAttribute("list", service.getList());
	}
	
	@PostMapping("/register")
	public String register(BoardVO vo, RedirectAttributes rttr) {
		service.register(vo);
		rttr.addFlashAttribute("result", "success");
		return "redirect:/board/list";
	}
	
	@GetMapping("/get")
	public void get(@RequestParam("bno") Long bno, Model model) {
		model.addAttribute("board", service.get(bno));
		
	}
	
	@PostMapping("/modify")
	public String modify(BoardVO vo, RedirectAttributes rttr) {
		if( service.modify(vo)) {
			rttr.addAttribute("result", "succcess");			
		}
		return "redirect:/board/list";
	}

	//삭제는 반드시, post 로만 처리합니다.
	@PostMapping("/remove")
	public String delete(@RequestParam("bno") Long bno, RedirectAttributes rttr) {
		
		if(service.remove(bno)) {
			rttr.addFlashAttribute("result","success");
		}
		return "redirect:/board/list";
	}
}
```

## <font color='blue'>3. BoardController 테스트 작성</font>

1. `src/test/java` 디렉토리 하위에 `org.example.controller` 패키지 생성 - `BoardControllerTests.java` 클래스 선언
2. WAS 실행하지 않고 웹 URL 을 테스트 할 수 있다.
3. @WebAppConfiguration 은, servlet-context.xml 설정(스프링의 WebApplicationContext 이용하기 위한 설정) 을 이용하기 위해서 선언

```java
@RunWith(SpringJUnit4ClassRunner.class)
// test for Controller 
@WebAppConfiguration
@ContextConfiguration({
		"file:src/main/webapp/WEB-INF/spring/root-context.xml",
		"file:src/main/webapp/WEB-INF/spring/appServlet/servlet-context.xml"})
//자바 설정 시, @ContextConfiguration(classes = {RootConfig.class, ServletConfig.class})
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