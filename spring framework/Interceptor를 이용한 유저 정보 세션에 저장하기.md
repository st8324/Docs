# Interceptor를 이용한 유저 정보 세션에 저장하기

## 1. 패키지 생성

* 국가.회사명.프로젝트명.interceptor 패키지 생성
* 예 : kr.green.spring.interceptor

## 2. 컨트롤러에서 유저 정보를 Model에 넘기기

1. 로그인 창에서 입력 받은 id와 pw를 이용하여 유저 정보 검색
2. 유저 정보가 있으면 유저 정보를 Model에 추가 후 게시판 리스트 페이지로 이동
3. 없으면 로그인 페이지 유지

```java
@Autowired
private UserMapper userMapper;

@RequestMapping(value = "/", method = RequestMethod.GET)
public String home(Model model, HttpServletRequest request) {
    String id = request.getParameter("id");
    String pw = request.getParameter("pw");

    User user = userMapper.login(id,pw);

    if(user != null) {
        model.addAttribute("user", user);
        return "redirect:/board/list";
    }

    return "home";
}
```

4. 로그인 페이지 소스

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>

<html>
<head>
<title>Home</title>
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.1.0/css/bootstrap.min.css">
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.0/umd/popper.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.1.0/js/bootstrap.min.js"></script>
</head>
<body>

<div class="container">
<h1>
	로그인  
</h1>
  <form>
    <div class="form-group">
      <label for="usr">Id:</label>
      <input type="text" class="form-control" id="usr" name="id">
    </div>
    <div class="form-group">
      <label for="pwd">Password:</label>
      <input type="password" class="form-control" id="pwd" name="pw">
    </div>
    <button type="submit" class="btn btn-primary">로그인</button>
  </form>
</div>
</body>
</html>
```

## 2. 로그인 시도 시 실행할 interceptor 클래스 생성

1. LoginInterceptor 클래스 생성 및 메소드 오버라이딩
   * HandlerInterceptorAdapter 클래스를 상속 받은 LoginInterceptor 클래스 생성
   * postHandle 메소드를 오버라이딩 함
   * Controller에서 Model에 유저 정보를 저장하지 않았으면 modelMap.get("user")를 했을 때 null값이 들어가고 있으면 저장한 정보를 가져온다.
   * 가져온 정보가 null이 아니면 해당 유저 정보를 세션에 저장

```java
@Override
public void postHandle(
    HttpServletRequest request, 
    HttpServletResponse response, 
    Object handler, 
    ModelAndView modelAndView)
    throws Exception {
    ModelMap modelMap = modelAndView.getModelMap();
    User user = (User)modelMap.get("user");

    if(user != null) {
        HttpSession session = request.getSession();
        session.setAttribute("user", user);
    }
}
```

## 3. LoginInterceptor를 servlet-context.xml에 interceptor로 등록

1. kr.green.spring.interceptor.LoginInterceptor 클래스를 bean에 등록

```xml
<beans:bean id="loginInterceptor" 
	class="kr.green.spring.interceptor.LoginInterceptor"></beans:bean>
```

2. 로그인 path가 주어졌을 때 loginInterceptor가 호출하도록 interceptor 태그에 등록

```xml
<interceptors>
    <interceptor>
        <mapping path="/"/>
        <beans:ref bean="loginInterceptor"/>
    </interceptor>
</interceptors>
```

3. 최종적으로 servlet.context.xml에 추가한 코드

```xml
<beans:bean id="loginInterceptor" 
	class="kr.green.spring.interceptor.LoginInterceptor"></beans:bean>
<interceptors>
	<interceptor>
		<mapping path="/"/>
		<beans:ref bean="loginInterceptor"/>
	</interceptor>
</interceptors>
```

