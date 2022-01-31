> dml 반환 값 이용하기

- DAO 수정

```java
    public int insert(BoardVO vo);
    public int update(BoardVO vo);

    public int delete(Long bno);

    public int insertSelectKey(BoardVO vo);
```
1. 반환 값을 int로 한다.

2. 처리 성공한 행의 갯수나, 성공여부(0또는1)를 반환한다.

<br/>

- ServiceImpl 수정 (당연히 Service도)

```java
    @Override
    public boolean register(BoardVO vo) {
        return dao.insertSelectKey(vo) == 1;
    }
    @Override
    public boolean modify(BoardVO vo) {
        return dao.update(vo)==1;
    }
    @Override
    public boolean remove(Long bno) {
        return dao.delete(bno)==1;
    }
```

<br/>

- Controller 수정

```java
    @PostMapping("/register")
    public String register(BoardVO vo, RedirectAttributes rttr) {
        if(service.register(vo))
            rttr.addFlashAttribute("result", vo.getBno());
        return "redirect:/board/list";
    }
    
    @PostMapping("/modify")
    public String modify(BoardVO vo, RedirectAttributes rttr){
        if(service.modify(vo))
            rttr.addFlashAttribute("result", "success");
        return "redirect:/board/list";
    }
    
    @PostMapping("/remove")
    public String remove(Long bno, RedirectAttributes rttr){
        if(service.remove(bno))
            rttr.addFlashAttribute("result", "success");
        return "redirect:/board/list";
    }
```

<br/>

> include 적용

- header.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>


<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.2/css/bootstrap.min.css" />
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.2/css/bootstrap-theme.min.css" />
<script	src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.2/js/bootstrap.min.js"></script>


</head>

<body> 
```

- footer.jsp

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>

</body>
</html> 
```

- include 사용

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<!-- <%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%> -->
<!-- <%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt"%>-->

<%@ include file="../includes/header.jsp"%>

...

<%@ include file="../includes/footer.jsp"%>
```

<br/>

> 모달창 구현하기

- 모달 코드 list.jsp에 포함(부트스트랩 필요)

```jsp
<div class="modal" id="myModal" tabindex="-1" role="dialog"
	aria-labelledby="myModalLabel" aria-hidden="true">
	<div class="modal-dialog">
		<div class="modal-content">
			<div class="modal-header">
				<button type="button" class="close" data-dismiss="modal"
					aria-hidden="true">x</button>
				<h4 class="modal-title" id="myModalLabel">Modal Title</h4>
			</div>
			<div class="modal-body">처리가 완료되었습니다.</div>
			<div class="modal-footer">
				<button type="button" class="btn btn-default" data-dismiss="modal">
					Close</button>
			</div>
		</div>
	</div>
</div>
```

<br/>

- list.jsp의 script

```html
<script type="text/javascript">
	$(document).ready(
			function() {

				//modal
				const result = '<c:out value="${result}" />';

				checkModal(result);

				function checkModal(result) {

					if (result === '')
						return;

					if (parseInt(result) > 0) {
						$(".modal-body").html(
								"게시글" + parseInt(result) + " 번이 등록되었습니다.");
					}

					$("#myModal").modal("show");
				}

				//register
				const regButton = document.querySelector("#regButton");

				function register() {
					location.href = "/board/register";
				}

				regButton.addEventListener("click", register);
			});
</script>
```
<br/>

> 뒤로가기 문제 해결

- 위 스크립트 수정

```html
<script type="text/javascript">
	$(document).ready(
			function() {

				//modal
				const result = '<c:out value="${result}" />';

				checkModal(result);

                history.replaceState({},null,null);

				function checkModal(result) {

					if (result === '' || history.state)
						return;

					if (parseInt(result) > 0) {
						$(".modal-body").html(
								"게시글" + parseInt(result) + " 번이 등록되었습니다.");
					}

					$("#myModal").modal("show");
				}

				//register
                ...
			});
</script>
```
