# Pagination+Search

* 페이지네이션이 적용된 게시판 목록 페이지에 검색 기능 추가

## 1. PageMaker 클래스에 검색어, 타입 추가

* 멤버 변수로 검색어를 저장하는 search, 검색에 타입을 저장하는 type을 추가 후 getter와 setter 추가

```java
private String search;
private String type;

public String getSearch() {
    return search;
}
public void setSearch(String search) {
    this.search = search;
}
public String getType() {
    return type;
}
public void setType(String type) {
    this.type = type;
}
```



## 2. 게시판 페이지수정 

* board/list.jsp에 검색창을 위한 input태그 추가 및 검색 타입을 위한 select 추가

```jsp
<nav class="navbar navbar-expand-sm bg-dark navbar-dark">
    <div class="container">
        <div class="col-12">
            <form class="form-inline"  style="display:inline-block">
                <select class="form-control" id="type" name="type">
                    <option value="1" <c:out value="${pageMaker.type==1?'selected':'' }"/>>선택
                    </option>
                	<option value="2" <c:out value="${pageMaker.type==2?'selected':'' }"/>>제목
                	</option>
				   <option value="3" <c:out value="${pageMaker.type==3?'selected':'' }"/>>저자
            		</option>
				   <option value="4" <c:out value="${pageMaker.type==4?'selected':'' }"/>>내용
        			</option>
  				</select>
				<input class="form-control mr-sm-2" type="text" placeholder="Search"
                       name="search" value="${pageMaker.search}">
				<button class="btn btn-success" type="submit">Search</button>
			</form>
			<div class="float-right" style="margin-top:7px;">
				<a href="<%=request.getContextPath()%>/modify">회원 정보 수정</a>
				<span style="color:white;"> | </span> 
				<a href="<%=request.getContextPath()%>/logout">로그아웃</a>
			</div>
		</div>
	</div>
</nav>
```

* 페이지네이션 수정

```jsp
<ul class="pagination" style="justify-content: center;">
    <c:if test="${pageMaker.prev}">
        <li class="page-item"><a class="page-link" 
         	href="<%=request.getContextPath()%>/board/list?page=${pageMaker.startPage-1 }&search=${pageMaker.search}&type=${pageMaker.type}">Previous</a></li>
    </c:if>
    <c:forEach begin="${pageMaker.startPage }" end="${pageMaker.endPage}" var="index">
        <li class="page-item"><a class="page-link" 
            href="<%=request.getContextPath()%>/board/list?page=${index}&search=${pageMaker.search}&type=${pageMaker.type}">${index}</a></li>
    </c:forEach>
    <c:if test="${pageMaker.next}">
        <li class="page-item"><a class="page-link" 
            href="<%=request.getContextPath()%>/board/list?page=${pageMaker.endPage+1 }&search=${pageMaker.search}&type=${pageMaker.type}">Next</a></li>
    </c:if>
</ul>
```



## 3. BoardMapper.java에 메소드 추가

* 타입별 검색에 필요한 메소드 추가

```java
public List<Board> listPageByTitle(@Param("cri")Criteria cri,@Param("title")String title);
public int countBoardByTitle(@Param("title")String title);
public List<Board> listPageByContents(@Param("cri")Criteria cri,@Param("contents")String contents);
public int countBoardByContents(@Param("contents")String contents);
public List<Board> listPageByAuthor(@Param("cri")Criteria cri,@Param("author")String author);
public int countBoardByAuthor(@Param("author")String author);
```



## 4. BoardMapper.xml에 쿼리문 추가

* BoardMapper.java에 추가한 메소드가 실행할 쿼리문 작성

```xml
<select id="listPageByTitle" resultType="kr.green.springwebproject.dao.Board">
    select
    *
    from
    board
    where title like #{title}
    order by number desc
    limit #{cri.pageStart}, #{cri.perPageNum}
</select>
<select id="countBoardByTitle" resultType="java.lang.Integer">
    select
    count(number)
    from
    board
    where title like #{title}
</select>
<select id="listPageByContents" resultType="kr.green.springwebproject.dao.Board">
    select
    *
    from
    board
    where contents like #{contents}
    order by number desc
    limit #{cri.pageStart}, #{cri.perPageNum}
</select>
<select id="countBoardByContents" resultType="java.lang.Integer">
    select
    count(number)
    from
    board
    where contents like #{contents}
</select>
<select id="listPageByAuthor" resultType="kr.green.springwebproject.dao.Board">
    select
    *
    from
    board
    where author like #{author}
    order by number desc
    limit #{cri.pageStart}, #{cri.perPageNum}
</select>
<select id="countBoardByAuthor" resultType="java.lang.Integer">
    select
    count(number)
    from
    board
    where author like #{author}
</select>
```

## 5. BoardController 수정

```java
@RequestMapping(value="list")
public String boardList(Model model, Criteria cri, String type, String search) {
    int totalCount = 0;
    PageMaker pageMaker = new PageMaker();
    ArrayList<Board> list = null;
    pageMaker.setCriteria(cri);
    System.out.println("search : " + search);
    System.out.println("type : " + type);
    if(search==null)
        search="";
    if(type==null)
        type="1";
    pageMaker.setSearch(search);
    pageMaker.setType(type);
    if(type.compareTo("1") == 0) {
        totalCount = boardMapper.countBoard();
        pageMaker.setTotalCount(totalCount);
        list = (ArrayList)boardMapper.listPage(pageMaker.getCriteria());
    }
    else if(type.compareTo("2") == 0) {
        totalCount = boardMapper.countBoardByTitle("%"+search+"%");
        pageMaker.setTotalCount(totalCount);
        list = (ArrayList)boardMapper.listPageByTitle(pageMaker.getCriteria(),"%"+search+"%");
    }
    else if(type.compareTo("3") == 0) {
        totalCount = boardMapper.countBoardByAuthor("%"+search+"%");
        pageMaker.setTotalCount(totalCount);
        list = (ArrayList)boardMapper.listPageByAuthor(pageMaker.getCriteria(),"%"+search+"%");
    }
    else if(type.compareTo("4") == 0) {
        totalCount = boardMapper.countBoardByContents("%"+search+"%");
        pageMaker.setTotalCount(totalCount);
        list = (ArrayList)boardMapper.listPageByContents(pageMaker.getCriteria(),"%"+search+"%");
    }
    System.out.println(pageMaker);
    model.addAttribute("list",list);
    model.addAttribute("pageMaker", pageMaker);
    return "/board/list";
}
```



