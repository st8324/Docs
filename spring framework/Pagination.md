# Pagination

* 정의 : 여러 페이지에 일련의 관련 콘텐츠가 있음을 나타내는 페이지 번호 매김을 보여주는 문서 

## 1. Criteria 클래스 생성

* Criteria 클래스는 한 페이지의 기준이 되는 클래스이다.
* Criteria 클래스는 한 페이지에 나타낼 콘텐츠의 갯수와 현재 페이지 번호를 나타낸다.
* 페이지 네이션을 위한 pagination 패키지 생성
  * 예 : kr.green.spring.pagination
* Criteria 클래스 생성 후 아래 코드 추가

```java
package kr.green.spring.pagination;

public class Criteria {
	//현재 페이지
	private int page; 
	//한 페이지 당 컨텐츠 갯수
	private int perPageNum;
	//Criteria 디폴트 생성자 : 현재 페이지를 1페이지로, 한 페이지에 10개의 컨텐츠
	public Criteria() {
		this.page = 1;
		this.perPageNum = 10;
	}
	//getter and setter
	public int getPage() {
		return page;
	}
	public void setPage(int page) {
		//현재 페이지 번호를 음수로 설정하려 할 때
		if(page <= 0) {
			this.page = 1;
		}
		else
			this.page = page;
	}
	public int getPerPageNum() {
		return perPageNum;
	}
	public void setPerPageNum(int perPageNum) {
		/* 한 페이지에 컨텐츠 수를 0이상 100이하로 설정
		개발자 설정에 따라 100부분을 수정할 수 있음 */
		if(perPageNum <=0 || perPageNum > 100) {
			this.perPageNum = 10;
		}
		else
			this.perPageNum = perPageNum;
	}
	@Override
	public String toString() {
		return "Criteria [page=" + page + ", perPageNum=" + perPageNum + "]";
	}
	/* 쿼리문에서 limit에 사용되는 인덱스를 계산하는 getter */
	public int getPageStart() {
		return (this.page -1) * perPageNum;
	}
	
}
```

## 2. BoardMapper 인터페이스 수정

* BoardMapper 인터페이스(BoardMapper.java)에 메소드 추가
* Criteria를 이용하여 검색 할 수 있는 메소드 추가
* 게시글 전체 갯수 가져오는 메소드 추가

```java
public interface BoardMapper {
	//... 이전 메소드 생략
	public List<Board> listPage(Criteria cri); 
    public int countBoard();
}
```

## 3. BoardMapper.xml에 쿼리문 추가

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="kr.green.spring.dao.BoardMapper">
    <select id="listPage" resultType="kr.green.spring.dao.Board">
		select
			*
		from
			board
		order by number desc
		limit #{pageStart}, #{perPageNum}
	</select>
    <select id="countBoard">
		select
			count(number)
		from
			board
	</select>
</mapper>
```

## 4. PageMaker 클래스 생성

* PageMaker 클래스는 현재 활성화된 페이지가 포함된 페이지들을 나타내는 클래스이다.
* 멤버 변수는 다음과 같다.
  * totalCount : 총 컨텐츠의 갯수
  * startPage, endPage : 현재 활성화된 페이지를 포함하는 페이지들의 시작페이지와 마지막페이지
  * prev : 이전으로 가기 버튼의 유무
  * next : 다음으로 가기 버튼의 유무(next)
  * displayPageNum : 한 페이지메이커가 보여줄 페이지의 갯수
  * criteria : 현재 페이지 정보
* PageMaker 클래스를 pagination 패키지에 생성 후 아래 코드 추가 

```java
package kr.green.spring.pagination;

public class PageMaker {
	private int totalCount;
	private int startPage;
	private int endPage;
	private boolean prev;
	private boolean next;
	private int displayPageNum;
	private Criteria criteria;
	
	/* endPage, startPage, prev, next 값 계산 */
	public void calcData() {
		/* starPage와 endPage는 현재 페이지 정보인 criteria와 displayPageNum을 이용하여 계산
		 * displayPageNum이 10이고 현재 페이지가 3페이지면 startPage = 1, endPage = 10이 되도록 계산 */
		endPage = (int) (Math.ceil(criteria.getPage()/(double) displayPageNum)*displayPageNum);
		
		startPage = (endPage - displayPageNum)+1;
		/* 총 콘텐츠 갯수를 이용하여 마지막 페이지 번호를 계산 */
		int tempEndPage = (int)(Math.ceil(totalCount/(double)criteria.getPerPageNum()));
		
		/* 현재 페이지에 계산된 현재 페이지메이커의 마지막 페이지 번호와 실제 마지막 페이지 번호를 비교하여
		 * 작은 값이 마지막 페이지 번호가 됨 */
		if(endPage > tempEndPage) {
			endPage = tempEndPage;
		}
		/* 현재 페이지메이커에 시작페이지가 1페이지면 prev가 없어야 함 */
		prev = startPage == 1 ? false : true;
		/* 현재 페이지메이커에 마지막 페이지에 컨텐츠의 마지막이 포함되어 있으면 next가 없어야 함 */
		next = endPage * criteria.getPerPageNum() >= totalCount ? false:true;
	}
	/* getter and setter */
	public int getTotalCount() {
		return totalCount;
	}
	public void setTotalCount(int totalCount) {
		this.totalCount = totalCount;
		calcData();
	}
	public int getStartPage() {
		return startPage;
	}
	public void setStartPage(int startPage) {
		this.startPage = startPage;
	}
	public int getEndPage() {
		return endPage;
	}
	public void setEndPage(int endPage) {
		this.endPage = endPage;
	}
	public boolean isPrev() {
		return prev;
	}
	public void setPrev(boolean prev) {
		this.prev = prev;
	}
	public boolean isNext() {
		return next;
	}
	public void setNext(boolean next) {
		this.next = next;
	}
	public int getDisplayPageNum() {
		return displayPageNum;
	}
	public void setDisplayPageNum(int displayPageNum) {
		this.displayPageNum = displayPageNum;
	}
	public Criteria getCriteria() {
		return criteria;
	}
	public void setCriteria(Criteria criteria) {
		this.criteria = criteria;
	}
	@Override
	public String toString() {
		return "PageMaker [totalCount=" + totalCount + ", startPage=" + startPage + ", endPage=" + endPage + ", prev="
				+ prev + ", next=" + next + ", displayPageNum=" + displayPageNum + ", criteria=" + criteria + "]";
	}
	
}
```

## 5. 게시판 목록 페이지에 페이지네이션 추가

* 게시판 목록 페이지 /views/board/list.jsp에 페이지네이션 추가
* 부트스트랩4 참고

```jsp
<ul class="pagination" style="justify-content: center;">
    <c:if test="${pageMaker.prev}">
        <li class="page-item">
            <a class="page-link" href="<%=request.getContextPath()%>/board/list?page=${pageMaker.startPage-1}">Previous</a>
        </li>
    </c:if>
    <c:forEach begin="${pageMaker.startPage }" end="${pageMaker.endPage}" var="index">
        <li class="page-item">
            <a class="page-link" href="<%=request.getContextPath()%>/board/list?page=${index}">${index}</a>
        </li>
    </c:forEach>
    <c:if test="${pageMaker.next}">
        <li class="page-item">
            <a class="page-link" href="<%=request.getContextPath()%>/board/list?page=${pageMaker.endPage+1}">Next</a>
        </li>
    </c:if>
</ul>
```

## 6. BoardController에 코드 수정

* PageMaker를 이용하여 페이지네이션 적용

```java
@RequestMapping(value="list")
public String boardList(Model model, Criteria cri) {
    int totalCount = boardMapper.countBoard();
    PageMaker pageMaker = new PageMaker();
    pageMaker.setCriteria(cri);
    pageMaker.setTotalCount(totalCount);
    ArrayList<Board> list 
        = (ArrayList)boardMapper.listPage(pageMaker.getCriteria());

    model.addAttribute("list",list);
    model.addAttribute("pageMaker", pageMaker);
    return "/board/list";
}
```



# 참고 문헌

#### http://bootstrap4.kr/docs/4.0/components/pagination/

#### https://www.w3schools.com/bootstrap4/bootstrap_pagination.asp

#### 코드로 배우는 스프링 웹 프로젝트

#### 





#### 