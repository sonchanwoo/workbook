
- 3번 문제는 간단한 crud 흐름만을 위한 예제로써 @SelectKey, 모달, data-oper사용, 제이쿼리...등이 포함되어 있지 않습니다.

- 위 사항들을 조금씩 붙여 나갈 예정입니다.

- 세부적인 것들 차이가 있을 수 있으며 신경쓰지 않으셔도 됩니다. (이번 문제는 일단 돌아가기만 하면 됩니다)

---

> DAO tests & production

- 테스트 코드

```java
package org.zerock.dao;

import static org.junit.Assert.assertNotNull;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.zerock.domain.BoardVO;

import lombok.Setter;
import lombok.extern.log4j.Log4j;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("file:src/main/webapp/WEB-INF/spring/root-context.xml")
@Log4j
public class BoardDAOTest {
    @Setter(onMethod_=@Autowired)
    private BoardDAO dao;
    
    @Test
    public void testDAO() {
        assertNotNull(dao);
    }
    
    @Test
    public void testGetList() {
        log.info(dao.getList());
    }
    
    @Test
    public void testGetOne() {
        log.info(dao.read(4L));
    }
    
    @Test
    public void testInsert() {
        BoardVO vo = new BoardVO();
        vo.setContent("helo");
        vo.setTitle("title");
        vo.setWriter("me");
        
        log.info(dao.insert(vo));
    }
    
    @Test
    public void testUpdate() {
        BoardVO vo = new BoardVO();
        vo.setBno(19L);
        vo.setContent("helo2");
        vo.setTitle("title2");
        vo.setWriter("me2");
        
        log.info(dao.update(vo));
    }
    
    @Test
    public void testRemove() {
        log.info(dao.delete(19L));
    }    
}

```

<br/>

- 프로덕션 코드

1. root-context.xml의 mybatis-scan / mybatis-config의 typeAlias

```xml
	<mybatis-spring:scan base-package="org.zerock.dao" />
```

```xml
<configuration>
  <typeAliases>

    <typeAlias type="org.zerock.domain.BoardVO" alias="BoardVO" />

  </typeAliases>
</configuration> 
```

2. DAO 인터페이스

```java
package org.zerock.dao;

import java.util.List;

import org.zerock.domain.BoardVO;

public interface BoardDAO {
    public List<BoardVO> getList();

    public BoardVO read(Long bno);

    public int insert(BoardVO vo);

    public int update(BoardVO vo);

    public int delete(Long bno);
}
```

3. DAO xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="org.zerock.dao.BoardDAO">

	<select id="getList" resultType="BoardVO">
		<![CDATA[
			select bno,title,content,writer,regDate,updateDate from tbl_board where bno > 0
		]]>
        <!-- index를 이용하기 위해 where 조건절에서 pk로 건든다. -->
	</select>

	<select id="read" resultType="BoardVO">
		select
		bno,title,content,writer,regDate,updateDate from tbl_board where
		bno=#{bno}
	</select>

	<insert id="insert">
		insert into tbl_board (title,content,writer)
		values
		(#{title},#{content}, #{writer})
	</insert>
	
	<update id="update">
  			update tbl_board
  			set title=#{title},
  			content = #{content},
  			writer = #{writer},
  			updateDate = now()
  			where bno = #{bno}
	</update>
	
	<delete id="delete">
		delete from tbl_board where bno=#{bno}
	</delete>

</mapper>
```

<br/>

> Service tests & production

- 테스트 코드

```java
package org.zerock.service;

import static org.junit.Assert.assertNotNull;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.zerock.domain.BoardVO;

import lombok.Setter;
import lombok.extern.log4j.Log4j;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("file:src/main/webapp/WEB-INF/spring/root-context.xml")
@Log4j
public class BoardServiceTest {

    @Setter(onMethod_=@Autowired)
    private BoardService service;

    @Test
    public void test() {
        assertNotNull(service);
    }

    @Test
    public void testGetList() {
        log.info(service.getList());
    }

    @Test
    public void testGet() {
        log.info(service.get(4L));
    }

    @Test
    public void testRegister() {
        BoardVO vo = new BoardVO();
        vo.setContent("helo");
        vo.setTitle("title");
        vo.setWriter("me");

        service.register(vo);
    }

    @Test
    public void testModify() {
        BoardVO vo = new BoardVO();
        vo.setBno(20L);
        vo.setContent("helo2");
        vo.setTitle("title2");
        vo.setWriter("me3");

        service.modify(vo);        
    }

    @Test
    public void testRemove() {
        service.remove(20L);
    }
}
```

<br/>

- 프로덕션 코드

1. root-context.xml의 context-scan

```xml
	<context:component-scan	base-package="org.zerock.service"></context:component-scan>
```

2. Service 인터페이스

```java
package org.zerock.service;

import java.util.List;

import org.zerock.domain.BoardVO;

public interface BoardService {
    public List<BoardVO> getList();

    public BoardVO get(Long bno);

    public void register(BoardVO vo);

    public boolean modify(BoardVO vo);

    public boolean remove(Long bno);
}
```

3. Service 구현체

```java
package org.zerock.service;

import java.util.List;

import org.springframework.stereotype.Component;
import org.springframework.stereotype.Service;
import org.zerock.dao.BoardDAO;
import org.zerock.domain.BoardVO;

import lombok.AllArgsConstructor;

@Service
@AllArgsConstructor
public class BoardServiceImpl implements BoardService {

    private BoardDAO dao;

    @Override
    public List<BoardVO> getList() {
        return dao.getList();
    }

    @Override
    public BoardVO get(Long bno) {
        return dao.read(bno);
    }

    @Override
    public void register(BoardVO vo) {
        dao.insert(vo);
    }

    @Override
    public boolean modify(BoardVO vo) {
        return dao.update(vo)==1;
    }

    @Override
    public boolean remove(Long bno) {
        return dao.delete(bno)==1;
    }
}

```

<br/>

> Controller tests & production

- 테스트 코드

```java
package org.zerock.controller;

import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.springframework.test.context.web.WebAppConfiguration;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.springframework.ui.ModelMap;
import org.springframework.web.context.WebApplicationContext;
import org.springframework.web.servlet.ModelAndView;

import lombok.Setter;
import lombok.extern.log4j.Log4j;

@RunWith(SpringJUnit4ClassRunner.class)
@WebAppConfiguration//WebApplicationContext를 사용하가 위함
@ContextConfiguration({
    "file:src/main/webapp/WEB-INF/spring/root-context.xml",
"file:src/main/webapp/WEB-INF/spring/appServlet/servlet-context.xml"})
@Log4j
public class BoardControllerTest {
    @Setter(onMethod_=@Autowired)
    private WebApplicationContext ctx;

    private MockMvc mockMvc;

    @Before
    public void setUp() {
        this.mockMvc = MockMvcBuilders.webAppContextSetup(ctx).build();
    }

    @Test
    public void testList() throws Exception{
        ModelAndView modelAndView = mockMvc.perform(MockMvcRequestBuilders.get("/board/list"))//원하는 방식과, 경로
                .andReturn().getModelAndView();

        ModelMap modelMap = modelAndView.getModelMap();

        String viewName = modelAndView.getViewName();


        log.info("\n...................\n modelMap : "
                +modelMap+"\n viewName : "
                +viewName+"\n...................");
    }

    @Test
    public void testRegister() throws Exception{
        ModelAndView modelAndView = mockMvc.perform(MockMvcRequestBuilders.get("/board/register"))//원하는 방식과, 경로
                .andReturn().getModelAndView();

        ModelMap modelMap = modelAndView.getModelMap();

        String viewName = modelAndView.getViewName();


        log.info("\n...................\n modelMap : "
                +modelMap+"\n viewName : "
                +viewName+"\n...................");
    }

    @Test
    public void testRegisterPost() throws Exception{
        ModelAndView modelAndView = mockMvc.perform(MockMvcRequestBuilders.post("/board/register")
                .param("title", "제에목")
                .param("content", "내에용")
                .param("writer", "작성자")
                )
                .andReturn().getModelAndView();

        ModelMap modelMap = modelAndView.getModelMap();

        String viewName = modelAndView.getViewName();


        log.info("\n...................\n modelMap : "
                +modelMap+"\n viewName : "
                +viewName+"\n...................");
    }

    @Test
    public void testGetAndModify() throws Exception{
        ModelAndView modelAndView = mockMvc.perform(MockMvcRequestBuilders.get("/board/get")
                .param("bno", "4"))//원하는 방식과, 경로
                .andReturn().getModelAndView();

        ModelMap modelMap = modelAndView.getModelMap();

        String viewName = modelAndView.getViewName();


        log.info("\n...................\n modelMap : "
                +modelMap+"\n viewName : "
                +viewName+"\n...................");
    }

    @Test
    public void testModify() throws Exception{
        ModelAndView modelAndView = mockMvc.perform(MockMvcRequestBuilders.post("/board/modify")
                .param("bno", "4")
                .param("title", "제에목")
                .param("content", "내에용")
                .param("writer", "작성자")
                )
                .andReturn().getModelAndView();

        ModelMap modelMap = modelAndView.getModelMap();

        String viewName = modelAndView.getViewName();


        log.info("\n...................\n modelMap : "
                +modelMap+"\n viewName : "
                +viewName+"\n...................");
    }

    @Test
    public void testRemove() throws Exception{
        ModelAndView modelAndView = mockMvc.perform(MockMvcRequestBuilders.post("/board/remove")
                .param("bno", "4")
                )
                .andReturn().getModelAndView();

        ModelMap modelMap = modelAndView.getModelMap();

        String viewName = modelAndView.getViewName();


        log.info("\n...................\n modelMap : "
                +modelMap+"\n viewName : "
                +viewName+"\n...................");
    }
}
```

<br/>

- 프로덕션 코드

```java
package org.zerock.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.zerock.domain.BoardVO;
import org.zerock.service.BoardService;

import lombok.AllArgsConstructor;

@Controller
@AllArgsConstructor
@RequestMapping("/board/*")
public class BoardController {
    private BoardService service;

    @GetMapping("/list")
    public void list(Model model) {
        model.addAttribute("list",service.getList());
    }

    @GetMapping("/register")
    public void register() {        
    }

    @PostMapping("/register")
    public String register(BoardVO vo) {
        service.register(vo);
        return "redirect:/board/list";
    }

    @GetMapping({"/get","/modify"})
    public void get(Long bno, Model model) {
        model.addAttribute("vo",service.get(bno));
    }

    @PostMapping("/modify")
    public String modify(BoardVO vo){
        service.modify(vo);
        return "redirect:/board/list";
    }

    @PostMapping("/remove")
    public String remove(Long bno){
        service.remove(bno);
        return "redirect:/board/list";
    }
}
```

<br/>

> View(jsp)

- list.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt"%>
<!DOCTYPE html>
<html>
<head>
<title>BoardList</title>
</head>
<body>

	<table>
		<thead>
			<tr>
				<th>bno</th>
				<th>title</th>
				<th>writer</th>
				<th>registerDate</th>
				<th>updateDate</th>
			</tr>
		</thead>
		<tbody>
			<c:forEach items="${list }" var="vo">
				<tr>
					<td> <c:out value="${vo.bno }"></c:out> </td>
					<td> <a href="/board/get?bno=${vo.bno }"> <c:out value="${vo.title }"></c:out> </a> </td>
					<td> <c:out value="${vo.writer }"></c:out> </td>
					<td> <fmt:formatDate pattern="yyyy/MM/dd" value="${vo.regDate }"/> </td>
					<td> <fmt:formatDate pattern="yyyy/MM/dd" value="${vo.updateDate }"/> </td>		
				</tr>
			</c:forEach>
		</tbody>
	</table>

	<button id="regButton">register</button>

	<script type="text/javascript">
	
		const regButton = document.querySelector("#regButton");
		
		function register(){
			location.href="/board/register";
		}
		
		regButton.addEventListener("click",register);
	
	</script>
</body>
</html>
```

<br/>

- register.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<form action="/board/register" method="POST">

		<div>
			<label>title</label><br />
			<input name="title" type="text" required placeholder="title" />
		</div>

		<div>
			<label>writer</label><br />
			<input name="writer"  type="text"  required placeholder="writer" />
		</div>

		<div>
			<label>content</label><br />
			<textarea name="content"  rows="3" cols="" required placeholder="content" ></textarea>
		</div>

		<button type="submit">register</button>
		<button id="listButton" type="button">list</button>
	</form>

	<script type="text/javascript">
		function list() {
			location.href = "/board/list";
		}
		listButton.addEventListener("click", list);
	</script>
</body>
</html>
```

<br/>

- get.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>

	<div>
		<label>bno</label><br/>
		<input type="text" readonly	value="${vo.bno }" />
	</div>

	<div>
		<label>title</label><br/>
		<input type="text" readonly	value="${vo.title }" />
	</div>

	<div>
		<label>writer</label><br/>
		<input type="text" readonly	value="${vo.writer }" />
	</div>

	<div>
		<label>content</label><br/>
		<textarea readOnly rows="3" cols=""><c:out value="${vo.content }"></c:out></textarea>
	</div>

	<div>
		<label>registerDate</label><br/>
		<input type="text" readonly	value='<fmt:formatDate pattern="yyyy/MM/dd" value="${vo.regDate }" />'  />
	</div>

	<div>
		<label>updateDate</label><br/>
		<input type="text" readonly	value='<fmt:formatDate pattern="yyyy/MM/dd" value="${vo.updateDate }" />'  />
	</div>

	<div>
		<button id="modifyButton">modify</button>
		<button id="listButton">list</button>
	</div>	

	<form id="form" hidden="true" method="GET">
	</form>

	<script type="text/javascript">
	
		const modifyButton = document.querySelector("#modifyButton");
		const listButton = document.querySelector("#listButton");
		
		const form = document.querySelector("#form");
		/*
		function list(){
			form.action="/board/list";
			form.submit();
		}*/
		
		function list() {
			location.href = "/board/list";
		}
		
		function modify(){
			form.innerHTML = '<input id="bno" name="bno" value="${vo.bno}" />';
			form.action="/board/modify";
			form.submit();
		}
		
		modifyButton.addEventListener("click",modify);
		listButton.addEventListener("click",list);
	
	</script>

</body>
</html>
```

<br/>

- modify.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>

	<form id="form" method="POST">

<div>
		<label>bno</label><br/>
		<input name="bno" type="text" readOnly	value="${vo.bno }" />
	</div>

	<div>
		<label>title</label><br/>
		<input name="title"  type="text" required name="title" value="${vo.title }" />
	</div>

	<div>
		<label>writer</label><br/>
		<input  name="writer" readonly  type="text" required name="writer"	value="${vo.writer }" />
	</div>

	<div>
		<label>content</label><br/>
		<textarea name="content" rows="3" required><c:out value="${vo.content }" />
		</textarea>
	</div>

	<div>
		<label>registerDate</label><br/>
		<input type="text" readonly	value='<fmt:formatDate pattern="yyyy/MM/dd" value="${vo.regDate }" />'  />
	</div>

	<div>
		<label>updateDate</label><br/>
		<input type="text" readonly	value='<fmt:formatDate pattern="yyyy/MM/dd" value="${vo.updateDate }" />'  />
	</div>

	<div>
		<button id="modifyButton">modify</button>
		<button id="removeButton">remove</button>
		<button id="listButton" type="button">list</button>
	</div>	

	</form>

	<script type="text/javascript">
		const removeButton = document.querySelector("#removeButton");
		
		function list() {
			location.href = "/board/list";
		}
		
		function modify(e){
			e.preventDefault();
			
			form.action="/board/modify";
			form.submit();
		}
		
		function remove(e){
			e.preventDefault();
			
			form.action="/board/remove";
			form.submit();
		}
		
		modifyButton.addEventListener("click",modify);
		removeButton.addEventListener("click",remove);
		listButton.addEventListener("click",list);
	
	</script>

</body>
</html>
```

<br/>