---
sort: 9
---

# 검색 처리

## <font color='blue'>1. Criteria 객체 수정</font>
- `type` 속성 : 검색종류 추가
- `keyword` 속성 : 검색어 추가
- `getArryType()` 추가 : type 속성을 배열로 반환하는 함수 추가
- `getListLink()` 추가 : UriComponentBuilder 객체를 이용하여, 파라미터들로 get 타입의 uriString 생성하여 반환하는 함수 추가

```java
package org.example.domain;

import org.springframework.web.util.UriComponentsBuilder;

import lombok.Getter;
import lombok.Setter;
import lombok.ToString;

@Getter
@Setter
@ToString
public class Criteria {

	private int pageNumber;
	private int amount;
	private int rowindex;
	
	//  추가
	private String type;
	private String keyword;
	
	public Criteria() {
		this(1, 5);
	}
	
	public Criteria(int pageNumber, int amount) {
		this.pageNumber = pageNumber;
		this.amount = amount;
		// pageNumber가 음수일 때, 데이터 시작 인덱스를 0 부터 처리하도록 수정.
		if(this.pageNumber-1 < 0) {
			this.rowindex = 0;
		}else {
			this.rowindex = (this.pageNumber-1) * this.amount;
		}
	}
	
	// type 이 'TCW'등으로 들어왔을 때 배열 {'T','C','W'} 로 반환하는 함수. myBatis에서 collection 으로 사용함.
	public String[] getTypeArr() {
		return type == null? new String[] {} : type.split("");
	}
	
	// uriString 을 만들어 반환하는 함수
	public String getListLink() {
		UriComponentsBuilder builder = UriComponentsBuilder.fromPath("")
				.queryParam("pageNumber", this.pageNumber)
				.queryParam("amount", this.amount)
				.queryParam("rowindex", this.rowindex)
				.queryParam("type",this.type)
				.queryParam("keyword", this.keyword);
		return builder.toUriString();
	}
}
```

## <font color='blue'>2. BoardMapper.xml의 쿼리 수정</font>
- `getListWithPage` 와 `getTotal` 의 조건을 수정
- myBatis 의 trim, choose, sql, include 등 사용
- type 배열 루프를 위해 foreach 사용함, collection에 콜랙션이나 배열이 올 수 있음.
- choose를 사용하여, case 구문 같은 문장을 만듦.
- trim 의 prifix를 이용하여 내부의 구문이 실행 될 때마다 prifix를 앞에 붙여줌
- trim prefixOverrides 를 이용하여, 내부 구문의 가장앞에 오는 prefix를 삭제해 버림
- sql 로 지정된 쿼리는 include 를 통해서 재사용 가능함.

```xml
<sql id="criteria">
	<trim prefix="AND (" suffix=")" prefixOverrides="OR">
		<foreach item="type" collection="typeArr">
			<trim prefix="OR">
				<choose>
				<when test="type == 'T'.toString()">
				title like concat('%',#{keyword},'%')
				</when>
				<when test="type == 'C'.toString()">
				content like concat('%',#{keyword},'%')
				</when>
				<when test="type == 'W'.toString()">
				writer like concat('%',#{keyword},'%')
				</when>
				</choose>
			</trim>
		</foreach>	
	</trim>
</sql>
<!-- 생략 -->
<select id='getListWithPage' resultType='org.example.domain.BoardVO'>
select bno,title,content,writer,regdate,updatedate
from tbl_board 
where bno > 0
<include refid="criteria"></include>
limit #{rowindex}, #{amount}
</select>

<select id="getTotal" resultType="int">
select count(*) from tbl_board where bno > 0
<include refid="criteria"></include>
</select>
```

## <font color='blue'>3. list.jsp 화면에 검색필드 처리</font>

```jsp
<!-- 생략 -->
 </table>
<!-- Start Search -->
		<form class="form-inline" id='searchForm' action='/board/list' method='get'>
			<select class="form-control mr-3" name='type'>
				<option value=''>--</option>
				<option value='T' ${pageMaker.cri.type=="T"?"selected":""}>제목</option>
				<option value='C' ${pageMaker.cri.type=="C"?"selected":""}>내용</option>
				<option value='W' ${pageMaker.cri.type=="W"?"selected":""}>작성자</option>
				<option value='TC' ${pageMaker.cri.type=="TC"?"selected":""}>제목 or 내용</option>
				<option value='TW' ${pageMaker.cri.type=="TW"?"selected":""}>제목 or 작성자</option>
				<option value='TCW' ${pageMaker.cri.type=="TCW"?"selected":""}>제목 or 내용 or 작성자</option>
			</select>
			<input class="form-control mr-3" type='text' name='keyword' value='${pageMaker.cri.keyword}'/>
			<input type='hidden' name="pageNumber" id="pageNumber" value="${pageMaker.cri.pageNumber}"/>
			<input type='hidden' name="amount" id="amount" value="${pageMaker.cri.amount}"/>
			<input type='hidden' name="rowindex" id="rowindex" value="${pageMaker.cri.rowindex}"/>
			<button class='btn btn-primary '>Search</button>
		</form>
<!-- End Search -->
<!-- 생략 -->
<script type="text/javascript">
 $(document).ready(function(){
	$(".page-link").on("click", function(e){
			e.preventDefault();
			$('#pageNumber').val($(this).attr('href'));
			const amount = $('#amount').val();
			const pageNumber = $('#pageNumber').val();
			$('#rowindex').val((pageNumber-1) * amount);
			$('#searchForm').submit();
		});
		
		$("#searchForm button").on('click', function(){
			
			
			if(!$('#searchForm').find('select[name="type"]').val()){
				alert('검색종류를 선택하세요.');
				return false;
			}
		
			if(!$('#searchForm').find('input[name="keyword"]').val()){
				alert('검색를 선택하세요.');
				return false;
			}
			e.preventDefault();
			$('#pageNumber').val("1");
			const amount = $('#amount').val();
			const pageNumber = $('#pageNumber').val();
			$('#rowindex').val((pageNumber-1) * amount);
			$('#searchForm').submit();
		});
});
 </script>
```

## <font color='blue'>4. 수정/삭제 처리 후 리스트 복귀 시, 검색어 가지고 다니기</font>
- `modify.jsp` 의 수정, 삭제, 목록의 처리

```jsp
<!-- 생략 -->
<button data-oper='modify' class="btn btn-info">Modify</button>
<button data-oper='remove' class="btn btn-danger">Remove</button>
<button data-oper='list' class="btn btn-primary">List</button>

<input type='hidden' name="pageNumber" value="${criteria.pageNumber}"/>
<input type='hidden' name="amount" value="${criteria.amount}"/>
<input type='hidden' name="rowindex" value="${criteria.rowindex}"/>
<input type='hidden' name='type' value='${criteria.type}'/>
<input type='hidden' name='keyword' value='${criteria.keyword}'/>
</form>
<!-- 생략 -->

<script type="text/javascript">
 $(document).ready(function(){
	 $('button').on('click',function(e){
		 e.preventDefault();
		 
		 const form = $('form');
		 const oper = $(this).data('oper');
		 const pageNumberInput = form.find('input[name="pageNumber"]');
		 const amountInput = form.find('input[name="amount"]');
		 const rowindexInput = form.find('input[name="rowindex"]');
		 const bnoInput = form.find('input[name="bno"]');
		 const typeInput = form.find('input[name="type"]');
		 const keywordInput = form.find('input[name="keyword"]');
		 
		 switch(oper){
		 case 'list':
			 form.attr('action', '/board/list').attr('method','get');
			 form.empty();
			 form.append(pageNumberInput, amountInput, rowindexInput, typeInput, keywordInput);
			 break;
		 case 'remove':
			 form.attr('action', '/board/remove').attr('method','post');
			 form.empty();
			 form.append(pageNumberInput, amountInput, rowindexInput, typeInput, keywordInput, bnoInput);
			 break;
		 }
		 
		 form.submit();
	 })
 });
 </script>
```

- `BoardController.java`의 modify, remove 함수에서 검색어 처리
- 리다이렉트는 GET 방식을 이용함, RedirectAttributes 객체를 이용해도 되지만, 쿼리스트링을 만들어주어도 됨.

```java
	@PostMapping("/modify")
	public String modify(Criteria cri, BoardVO vo, RedirectAttributes rttr) {
		if( service.modify(vo)) {
			rttr.addFlashAttribute("result","success");
//			rttr.addAttribute("pageNumber", cri.getPageNumber());
//			rttr.addAttribute("amount", cri.getAmount());
//			rttr.addAttribute("rowindex", cri.getRowindex());
//			rttr.addAttribute("type", cri.getType());
//			rttr.addAttribute("keyword", cri.getKeyword());
		}
		return "redirect:/board/list" + cri.getListLink();
	}

	
	//삭제는 반드시, post 로만 처리합니다.
	@PostMapping("/remove")
	public String delete(Criteria cri, BoardVO vo, RedirectAttributes rttr) {
		
		if(service.remove(vo.getBno())) {
			rttr.addFlashAttribute("result","success");
//			rttr.addAttribute("pageNumber", cri.getPageNumber());
//			rttr.addAttribute("amount", cri.getAmount());
//			rttr.addAttribute("rowindex", cri.getRowindex());
//			rttr.addAttribute("type", cri.getType());
//			rttr.addAttribute("keyword", cri.getKeyword());
		}
		return "redirect:/board/list" + cri.getListLink();
	}
```