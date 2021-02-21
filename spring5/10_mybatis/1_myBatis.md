---
sort: 1
---

# 마이바티스 동적 SQL

## <font color='blue'>1. &lt;if&gt;</font>

- if 안에 들어간 표현식은 `OGNL` 표현식이라 함. 참고:<https://commons.apache.org/proper/commons-ognl/language-guide.html>
- `test` 속성의 특정 조건이 true가 되었을 때 포함된 SQL을 실행
- 예시

```xml
<if test="type == 'T'.toString()">
(title like concat('%',#{type},'%'))
</if>
```

## <font color='blue'>2. &lt;choose&gt;</font>

- 여러 상황들 중 하나의 상황에서 동작
- java의 'if ~ else' 와 유사함
- 예시

```xml
<choose>
<when test="type == 'T'.toString()">
    (title like concat('%',#{type},'%'))
</when>
<when test="type == 'C'.toString()">
    (content like concat('%',#{type},'%'))
</when>
<otherwise>
    (title like concat('%',#{type},'%') or content like concat('%',#{type},'%') or writer like concat('%',#{type},'%'))
</otherwise>
</choose>
```
## <font color='blue'>3. &lt;trim&gt;,&lt;where&gt;,&lt;set&gt;</font>

- trim, where, set은 단독 사용하지 않음. if,choose등과 같은 태그들을 내포하여 SQL 연결해 주고, AND, OR, WHERE 등 앞.뒤에 필요한 구문들을 추가 및 생략하는 역할

**<font color='green' style='font-size:large;'>where 태그</font>**

- 태그 안쪽에서 SQL이 생성될 때 Where 구문이 붙고 그렇지 않으면 생성되지 않음
- 예시

```xml
select * from tbl_board
<where>
    <if test="bno != null">
    bno = #{bno}
</where>
```

- 결과 1 bno가 없는 경우 : select * from tbl_board where bno = 123
- 결과 2 bno가 있는 경우 : select * from tbl_board

**<font color='green' style='font-size:large;'>trim 태그</font>**

- trim 안쪽에 만들어지는 SQL문을 조사하여, 앞쪽에 추가적인 SQL을 넣는 역할
- 예시

```xml
select * from tbl_board
<where>
    <if test='bno != null'>
        bno = #{bno}
    </if>
    <trim prefixOverrides='and'>
    rownum = 1
    </trim>
</where>
```

- 결과 1 bno 값 존재 시 : select * from tbl_board Where bno = 33 and rownum = 1
- 결과 2 bno 값 null : select * from  tbl_board wehre rownum = 1

- prefix : trim 내부 구문의 제일 앞에 추가할 문자열
- suffix : trim 내부 구문의 제일 마지막에 추가할 문자열
- prefixOverrides : trim 내부 구문의 제일 앞에서, prefixOverrides 에 명시된 문자열 제거
- suffixOverrides : trim 내부 구문의 제일 뒤에서, suffixOverrides 에 명시된 문자열 제거
- with : overrides에 명시된 것을 지우고 with 속성에 명시된 것을 추가

## <font color='blue'>4. &lt;foreach&gt;</font>

- List, 배열, 맵 등을 이용해서 루프 처리. 주로 IN 조건에서 많이 사용.
- 배열이나 list 이용 시, item 속성만 사용
- map 형태로 key 와 value 이용 시, index와 item 속성 사용

```java
Map<String, String> map = new HashMap();
map.put("T","TTT");
map.put("C","CCC");
```

```xml
select * from tbl_board
<trim prefix="where (" suffix=")" prefixOverrides="OR">
    <foreach item="val" index="key" collection="map">

        <trim prefix = "OR">
            <if test="key=='C'.toString()">
            content = #{val}
            </if>
            <if test="key=='T'.toString()">
            title = #{val}
            </if>
            <if test="key=='W'.toString()">
            writer = #{val}
            </if>
        </trim>
    </foreach>
```

## <font color='blue'>5. &lt;sql&gt;&lt;include&gt;</font>

- 재사용 가능한 구문을 &lt;sql id="test"&gt;...&lt;/sql&gt; 에 선언하고 &lt;include refid='test'&gt;&lt;/include&gt; 를 사용하여 재사용 한다.