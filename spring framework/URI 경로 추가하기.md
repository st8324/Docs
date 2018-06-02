# URI 경로 추가하기

##- 새로운 페이지를 생성하여 새로운 URI를 등록하려면?

1. controller에 메소드 추가
2. jsp나 html등으로 새로운 페이지 생성
3. controller에 추가한 메소드에서 새로운 페이지를 열도록 연결

# 로그인 페이지를 생성하여 등록하는 과정

## - Controller에 메소드 추가

* Controller 생성
  * controller 패키지에 MemberController 생성
  * 클래스 명위에 @Controller 어노테이션 추가
  * @RequestMapping 어노테이션을 이용해 URI 설정
  * 메소스 생성
  * return 값을 이용하여 페이지 파일과 연결

```java
/* 예시 */
package kr.green.spring.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller //contoller로 등록
@RequestMapping(value="/member")//URI가 /member로 시작되면 해당 컨트롤러에서 확인
public class MemberController {
	@RequestMapping(value="signin")
	public String memberSignin() {
		
		return "/member/signin";// /src/main/webapp/WEB-INF/views/memeber/signin.jsp와 연결
	}
}


```



