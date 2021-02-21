---
sort: 8
---

# 페이징 처리

## <font color='blue'>1. 페이지네이션에 필요한 파라미터</font>
1. pageNum : 페이지번호
2. amount : 한 페이지에 보여지는 데이터의 갯수
3. startPage : 제일 처음순서에 있는 페이지 번호
4. endPage : 제일 마지막 순서에 있는 페이지
5. realPage : 실제 데이터가 존재하는 마지막 페이지
6. pre : 이전 페이지 유무
7. next : 다음 페이지 유무
8. total : 총 데이터 갯수

## <font color='blue'>2. Criteria 클래스 생성(페이지를 표시할 기준)</font>

- `org.example.domain.Criteria.java`
- Oracle DB 사용할 경우, rownum 을 사용 할 수 있으므로, 아래와 같이 처리
- MySQL의 경우, rownum 사용이 불가하고, limit을 사용할 것이므로, 불러올 데이터가 몇 번째 데이터 부터인지 알아야 함

```java
package org.example.domain;

import lombok.Getter;
import lombok.Setter;
import lombok.ToString;

@Getter
@Setter
@ToString
public class Criteria {

	private int pageNumber;
	private int amount;
	//mySql 사용시, 속성 추가
    private int rowindex;

	public Criteria() {
		this(1, 10);
	}
	
	public Criteria(int pageNumber, int amount) {
		this.pageNumber = pageNumber;
		this.amount = amount;
        //mySql 사용 시, 속성 추가
        this.rowindex = (this.pageNumber-1) * this.amount;
	}
}
```

## <font color='blue'>3. PageDTO 클래스 생성(화면에 전달해 줄 페이지 속성)</font>

- `org.example.domain.PageDTO.java`

```java
package org.example.domain;

import lombok.Getter;
import lombok.ToString;

@Getter
@ToString
public class PageDTO {

	private int startPage;
	private int endPage;
	private boolean pre, next;
	
	private int total;
	private Criteria cri;
	
	public PageDTO(Criteria cri, int total) {
		this.cri = cri;
		this.total = total;
		
		this.endPage = (int) (Math.ceil(cri.getPageNumber() / (float) this.cri.getAmount())) * this.cri.getAmount();
		this.startPage = this.endPage - (this.cri.getAmount()-1);
		int realEnd = (int) (Math.ceil((total * 1.0) / this.cri.getAmount()));
		if(realEnd < this.endPage) this.endPage = realEnd;
		this.pre = this.startPage > 1;
		this.next = this.endPage < realEnd;
	}
}
```

## <font color='blue'>4. list 페이지 처리</font>

**<font color='green' style='font-size:large;'>1-1. Controller 처리</font>**
- `BoardController.java`

```java
@GetMapping("list")
	public void getList(Criteria cri, Model model) {

		int total = service.getTotal(cri);
		
        // 임의 페이지에 1개의 게시물만 존재하는데, 그 게시물을 삭제 할 경우 현재 pageNumber를 -1 해주는 작업.
		if(total <= (cri.getPageNumber()-1) * cri.getAmount()) {
			cri = new Criteria(cri.getPageNumber()-1, cri.getAmount());
		}
		
		model.addAttribute("list", service.getList(cri));
        // 화면에 표시되는 페이지 정보는 PageDTO 에 모두 들어 있음.
		model.addAttribute("pageMaker",new PageDTO(cri, total));
	}
```

**<font color='green' style='font-size:large;'>1-2. 데이터 총 갯수와 리스트 조회 처리</font>**

- `BoardMapper.xml`

```xml
	<select id='getListWithPage' resultType='org.example.domain.BoardVO'>
	<![CDATA[
	select bno,title,content,writer,regdate,updatedate
	from tbl_board
	where bno > 0
	limit #{rowindex}, #{amount}
	]]>
	</select>

	<select id="getTotal" resultType="int">
	select count(*) from tbl_board where bno > 0
	</select>
```

- `BoardMapper.java` : Criteria 파라미터가 필요가 없지만, 페이지 관련 함수들의 통일성을 위해 넣어줌.

```java
	public List<BoardVO> getListWithPage(Criteria cri);
	public int getTotal(Criteria cri);
```

- `BoardService.java`

```java
	public List<BoardVO> getList(Criteria cri);
	public int getTotal(Criteria cri);
```

- `BoardServiceImpl.java`

```java
	@Override
	public List<BoardVO> getList(Criteria cri) {
		return mapper.getListWithPage(cri);
	}

	@Override
	public int getTotal(Criteria cri) {
		// TODO Auto-generated method stub
		return mapper.getTotal(cri);
	}
```

**<font color='green' style='font-size:large;'>1-3. 화면 처리 하기</font>**

- `/board/list.jsp`

```jsp
 <table class="table table-bordered"  width="100%" cellspacing="0">
    <thead>
        <tr>
            <th>#번호</th>
            <th>제목</th>
            <th>작성자</th>
            <th>작성일</th>
            <th>수정일</th>
        </tr>
    </thead>
    <tbody>
        <c:forEach items="${list}" var="board">
        <tr>
            <!-- 기존에 a 태그로 조회링크를 처리 했으나, form 방식으로 변경함. -->
            <td><a href="${board.bno}" value="${board.bno}">${board.bno}</a></td>
            <td>${board.title}</td>
            <td>${board.writer}</td>
            <td><fmt:formatDate value="${board.regdate}" pattern="yyyy/mm/dd" /></td>
            <td><fmt:formatDate value="${board.updateDate}" pattern="yyyy/mm/dd" /></td>
        </tr>
        </c:forEach>
    </tbody>
</table>

<!-- Start pagination -->
<!-- justify-content-end : 부트스트랩4의 정렬방식으로, 우측 정렬 -->
<ul class="pagination justify-content-end" style="margin:20px 0">
    <c:if test="${pageMaker.pre}">
            <li class="page-item"><a class="page-link" href="${pageMaker.startPage - 1}">Previous</a></li>
    </c:if>
    
    <c:forEach var="num" begin="${pageMaker.startPage}" end="${pageMaker.endPage}">
        <li class="page-item ${pageMaker.cri.pageNumber==num ?'active':'' }"><a class="page-link" href="${num}">${num}</a></li>
        
    </c:forEach>
    
    <c:if test="${pageMaker.next}">
            <li class="page-item"><a class="page-link" href="${pageMaker.endPage + 1}">Next</a></li>
    </c:if>
</ul>
<form action='/board/list' method='get'>
    <input type='hidden' name="pageNumber" id="pageNumber" value="${pageMaker.cri.pageNumber}"/>
    <input type='hidden' name="amount" id="amount" value="${pageMaker.cri.amount}"/>
    <!-- mySql DB를 사용하는 경우, limit 에 넣어줄 데이터 순서 -->
    <input type='hidden' name="rowindex" id="rowindex" value="${pageMaker.cri.rowindex}"/>
</form>
<!-- 생략 -->
<script type="text/javascript">
 $(document).ready(function(){
	$(".page-link").on("click", function(e){
		e.preventDefault();
		
		$('#pageNumber').val($(this).attr('href'));
		const amount = $('#amount').val();
		const pageNumber = $('#pageNumber').val();
		$('#rowindex').val((pageNumber-1) * amount);
		$('form').submit();
		
	});
	
	$('.table a').on('click', function(e){
		e.preventDefault();
		const form = $('form');
		const bno = $('<input>').attr('name','bno').val($(this).attr('href'));
		form.append(bno);
		form.attr('action','/board/get').attr('method','get');
		form.submit();
	})
});
 </script>
```

## <font color='blue'>5. 조회/수정/삭제 후, 보던 목록의 페이지번호로 돌아오기 처리</font>
- 기본적으로, 각 기능의 화면에 Criteria의 속성을 모두 input 으로 지정해 줌
- 각 기능의 Controller 에서는, 파라미터로 Criteria 를 지정하고, 화면으로 전달 되도록 처리함

**<font color='green' style='font-size:large;'>1-1. 조회 처리 후 목록 이동</font>**
- `get.jsp`
- 수정 페이지로 이동 처리
- 목록 페이지로 이동 처리

```jsp
<!-- 생략 -->
<form action='/board/modify' method='get'>
    <input type='hidden' name='bno' value='${board.bno}'/>
    <input type='hidden' name="pageNumber" value="${criteria.pageNumber}"/>
    <input type='hidden' name="amount" value="${criteria.amount}"/>
    <input type='hidden' name="rowindex" value="${criteria.rowindex}"/>
</form>
<!-- 생략 -->
 <script type='text/javascript'>
 	$(document).ready(function(){
 		
 		$('button').on('click', function(){
             <!-- $.data()는, element의 'data-xxx' 속성의 값을 가져옴 -->
 			const oper = $(this).data('oper');
 	 		const form = $('form');
 	 		if(oper == 'list'){
 	 			form.attr('action', '/board/list');
 	 			form.find('input[name="bno"]').remove();
 	 		}
 	 		form.submit();
 		})
 	})
 </script>
```

- `BoardController.java`
- 파라미터로 객체를 지정하면, 자동으로 화면으로 전달 됨.

```java
	@GetMapping("/get")
	public void get(Criteria cri, @RequestParam("bno") Long bno, Model model) {
		model.addAttribute("board", service.get(bno));
		
	}
	
	@GetMapping("/modify")
	public void modify(Criteria cri, @RequestParam("bno") long bno, Model model) {
		model.addAttribute("board", service.get(bno));
	}
```

**<font color='green' style='font-size:large;'>1-2. 수정화면에서 수정 처리 후 목록 이동</font>**

- `modify.jsp`
- form 에 criteria 속성 추가
- Controller 에서 자동으로 넘어온 객체는 첫 글자만 소문자로 바꾸어 사용한다.

```jsp
<!-- 생략 -->
    <input type='hidden' name="pageNumber" value="${criteria.pageNumber}"/>
    <input type='hidden' name="amount" value="${criteria.amount}"/>
    <input type='hidden' name="rowindex" value="${criteria.rowindex}"/>
<!-- 생략 -->
 <script type="text/javascript">
 $(document).ready(function(){
	 $('button').on('click',function(e){
		 e.preventDefault();
		 <!-- form의 기본 처리는 수정처리 됨. -->
		 const form = $('form');
		 const oper = $(this).data('oper');
		 const pageNumberInput = form.find('input[name="pageNumber"]');
		 const amountInput = form.find('input[name="amount"]');
		 const rowindexInput = form.find('input[name="rowindex"]');
		 const bnoInput = form.find('input[name="bno"]');
		 
		 switch(oper){
             <!-- 목록으로 이동하는 버튼 클릭 시! -->
		 case 'list':
			 form.attr('action', '/board/list').attr('method','get');
			 form.empty();
			 form.append(pageNumberInput, amountInput, rowindexInput);
			 break;
             <!-- 삭제 처리 버튼 클릭 시! -->
		 case 'remove':
			 form.attr('action', '/board/remove').attr('method','post');
			 form.empty();
			 form.append(pageNumberInput, amountInput, rowindexInput, bnoInput);
			 break;
		 }
		 form.submit();
	 })
 });
 </script>
```

- `BoardController.java`
- 리다이렉트 시, `addAttribute` 에 Criteria 의 각 속성을 넣어준다.

```java
	@PostMapping("/modify")
	public String modify(Criteria cri, BoardVO vo, RedirectAttributes rttr) {
		if( service.modify(vo)) {
				
			rttr.addFlashAttribute("result","success");
			rttr.addAttribute("pageNumber", cri.getPageNumber());
			rttr.addAttribute("amount", cri.getAmount());
			rttr.addAttribute("rowindex", cri.getRowindex());
		}
		return "redirect:/board/list";
	}
```

**<font color='green' style='font-size:large;'>1-3. 수정화면에서 삭제 처리 후 목록 이동</font>**

- modify.jsp 처리는 1-2 와 같음.
- `BoardController.java`
- 리다이렉트 시, `addAttribute` 에 Criteria 의 각 속성을 넣어준다.

```java
	//삭제는 반드시, post 로만 처리합니다.
	@PostMapping("/remove")
	public String delete(Criteria cri, BoardVO vo, RedirectAttributes rttr) {
		
		if(service.remove(vo.getBno())) {
			rttr.addFlashAttribute("result","success");
			rttr.addAttribute("pageNumber", cri.getPageNumber());
			rttr.addAttribute("amount", cri.getAmount());
			rttr.addAttribute("rowindex", cri.getRowindex());
		}
		return "redirect:/board/list";
	}
```

**<font color='green' style='font-size:large;'>1-4. 수정화면에서 목록 이동</font>**
- modify.jsp 처리는 1-2 와 같음.
- Controller 처리는, '4. 리스트 처리' 와 같음
- 리다이렉트 시, `addAttribute` 에 Criteria 의 각 속성을 넣어준다.
