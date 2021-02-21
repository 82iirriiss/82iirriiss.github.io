---
sort: 4
---

# 영속계층(DB, Mapper) 구현

1. 테이블 반영하여 VO 생성
2. Mapper 인터페이스 작성
3. XML 처리
4. Mapper 인터페이스 테스트

## <font color='blue'> 1. 게시판 VO 클래스 작성 </font>

- `org.example.domain` 패키지 하위에 작성
- `BoardVO.java`

```java
@Data
public class BoardVO {
	
	private long bno;
	private String title;
	private String content;
	private String writer;
	private Date regdate;
	private Date updatedate;
}
```

## <font color='blue'> 2. 게시판 Mapper 인터페이스 작성</font>

- `org.example.mapper` 패키지 하위에 작성
- `BoardMapper.java`

```java
public interface BoardMapper {

	@Select("select * from tbl_board where bno > 0")
	public List<BoardVO> getList();
}
```

## <font color='blue'> 3. Mapper 인터페이스 TEST</font>

- src/test/java 폴더 하위에 `org.example.mapper` 패키지 생성
- `BoardMapperTest.java`
- 테스트를 통과하면, MapperXML 을 작성한다.

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("file:src/main/webapp/WEB-INF/spring/root-context.xml")
//@ContextConfiguration(classes={RootConfig.class})
@Log4j
public class BoardMapperTest {
	
	@Autowired
	private BoardMapper mapper;

	@Test
	public void testGetList() {
		
		mapper.getList().forEach(board -> log.info(board));
	}
}
```

## <font color='blue'> 4. Mapper XML 작성</font>

1. BoardMapper.java 인터페이스에서 @Select 어노테이션 부분을 삭제한 후 진행한다.
2. `src/main/resource` 폴더 하위에 `org > example > mapper` 폴더를 만든다. (!한 단계씩 만들어야 함.)
3. org/example/mapper 폴더 하위에 `BoardMapper.xml` 파일을 작성한다.
- `namespace`: mapper 인터페이스의 위치와 같게 한다.
- CDATE : xml에서 부등호를 사용하기 위해서 처리하였다.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="org.example.mapper.BoardMapper">
        <select id='getList' resultType='org.example.domain.BoardVO'>
        <![CDATA[
        select * from tbl_board where bno > 0
        ]]>
        </select>
    </mapper>
    ```
4. 이전에 작성해 둔 BoardMapperTest.java 를 테스트한다. (결과는 이전과 같아야 함)

## <font color='blue'> 5. CRUD 구현</font>

**<font color='green' style='font-size:large;'>1-0. BoardVO.java</font>**

```java
@Data
public class BoardVO {
	
	private Long bno;
	private String title;
	private String content;
	private String writer;
	private Date regdate;
	private Date updateDate;
	
	@Builder
	public BoardVO(Long bno, String title, String content, String writer) {
		this.bno = bno;
		this.title = title;
		this.content = content;
		this.writer = writer;
	}
}
```

--------------
**<font color='green' style='font-size:large;'>1-2. BoardMapper.java</font>**

```java
public interface BoardMapper {

	//@Select("select * from tbl_board where bno > 0")
	public List<BoardVO> getList();
	
	public void insert(BoardVO vo);
	
    //mySql에서는 xml의  useGeneratedKeys 속성을 사용하여, insert 하거나 update 한 키값을 리턴 받을 수 있는데   
    //안되고 있음. 이유를 잘 모르겠다.
	public void insertSelectKey(BoardVO vo);
	
	public  BoardVO read(Long bno);
	
	public int update(BoardVO vo);
	
	public int delete(Long bno);
	
}
```

-----------------
**<font color='green' style='font-size:large;'>1-3. BoardMapper.xml</font>**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
	PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="org.example.mapper.BoardMapper">	
	<select id='getList' resultType='org.example.domain.BoardVO'>
	<![CDATA[
	select * from tbl_board where bno > 0
	]]>
	</select>
	
	<insert id="insert">
		insert into tbl_board (title, content, writer)
		values (#{title}, #{content}, #{writer})
	</insert>
	
	<insert id="insertSelectKey" parameterType="org.example.domain.BoardVO"  useGeneratedKeys="true" keyProperty="org.example.domain.BoardVO.bno"  keyColumn="bno" >
			insert into tbl_board (title, content, writer)
			values (#{title}, #{content}, #{writer})
	</insert>
	
	<select id="read" resultType="org.example.domain.BoardVO">
		select * from tbl_board where bno = #{bno}
	</select>
	
	<update id="update">
		update tbl_board
		set title = #{title},
		content = #{content},
		writer = #{writer},
		updatedate = CURRENT_TIMESTAMP
		where bno = #{bno}
	</update>
	
	<delete id="delete">
		delete from tbl_board
		where bno = #{bno}
	</delete>
</mapper>
```

---------------------
**<font color='green' style='font-size:large;'>1-4. BoardMapperTest.java</font>**

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = {RootConfig.class})
@Log4j
public class BoardMapperTest {
	
	@Autowired
	private BoardMapper mapper;

	//@Test
	public void testGetList() {
		
		mapper.getList().forEach(board -> log.info(board));
	}
	
	//@Test
	public void testInsert() {
		
		BoardVO vo = BoardVO.builder()
				.title("타이틀-insertTest")
				.content("컨텐츠-insertTest")
				.writer("user01")
				.build();
				
		mapper.insert(vo);
		log.info("insert Test::");
		log.info(vo);
	}
	
	@Test
	public void testInsertSelectKey() {
		
		BoardVO vo = BoardVO.builder()
				.title("타이틀-insertTest")
				.content("컨텐츠-insertTest")
				.writer("user01")
				.build();

		mapper.insert(vo);
		log.info("insertSelectKey Test::");
		log.info(vo);
	}
	
//	@Test
	public void testUpdate() {
		
		BoardVO vo = BoardVO.builder()
				.bno(1L)
				.title("타이틀-updateTest")
				.content("컨텐츠-updateTest")
				.writer("user01")
				.build();
		
		log.info("update Test::");
		log.info("result:" + (mapper.update(vo) == 1));
	}
	
//	@Test
	public void testDelete() {
		
		log.info("delete Test::");
		log.info("result:" + (mapper.delete(2L) == 1));
	}
}
```