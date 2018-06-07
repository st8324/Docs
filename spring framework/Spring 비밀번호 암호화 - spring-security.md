# Spring 비밀번호 암호화 - spring-security

* 비밀번호 암호화를 위하여 Spring-security를 이용한다.
* 회원 가입 시 비밀번호를 인코딩하여 저장한다.
* 로그인 시 입력한 비밀 번호와 인코딩 된 비밀번호를 비교하여 (디코딩을 하지 않고서) 같으면 로그인 성공, 실패하면 로그인 실패

## 1.  pom.xml에 의존성 추가

* 다음 3개의 의존성을 추가한다.
  * spring-security-web
  * spring-security-core
  * spring-security-config

```xml
<!--스프링시큐리티 web 라이브러리-->
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-web</artifactId>
    <version>4.1.0.RELEASE</version>
</dependency>
  <!--스프링시큐리티 core 라이브러리-->
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-core</artifactId>
    <version>4.1.0.RELEASE</version>
</dependency>
  <!--스프링시큐리티 config 라이브러리-->
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-config</artifactId>
    <version>4.1.0.RELEASE</version>
</dependency>
```

## 2. spring-security.xml 생성

* /WEB_INF/spring 폴더에 spring-sercurity.xml 생성

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans:beans xmlns="http://www.springframework.org/schema/security"
    xmlns:beans="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/security http://www.springframework.org/schema/security/spring-security.xsd">

    <beans:bean id="bcryptPasswordEncoder" class="org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder" />  
</beans:beans>
```

## 3. web.xml에 spring-security.xml를 등록

```xml
<!-- 수정전 web.xml-->
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>
        /WEB-INF/spring/root-context.xml
    </param-value>
</context-param>
```

```xml
<!-- 수정후 web.xml-->
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>
        /WEB-INF/spring/root-context.xml
        /WEB-INF/spring/spring-security.xml
    </param-value>
</context-param>
```

## 4. 회원 가입 시 비밀번호를 암호화

* 회원 가입 시 암호화가 진행되는 Controller에 BCryptPasswordEncoder 멤버 객체 추가

```java
@Autowired
BCryptPasswordEncoder passwordEncoder;
```

* 암호화를 위해 encode 메소드 사용 - 테스트를 위해 급히 만듬

```java
@RequestMapping(value ="/member")
public String test() {
    String encPw = passwordEncoder.encode("1234");
    String id = "1234";
    String email = "stajun@naver.com";
    User user = new User();
    user.setEmail(email);
    user.setId(id);
    user.setPw(encPw);
    userMapper.signup(user);
    return "redirect:/";
}
```

## 5. 로그인 시 비밀 번호를 비교

* maches 메소드를 이용하여 암호화 되지 않은 비밀번호와 암호화 된 비밀번호를 비교한다.

```java
@RequestMapping(value = "/", method = RequestMethod.GET)
public String home(Model model, HttpServletRequest request) {
    String id = request.getParameter("id");
    String pw = request.getParameter("pw");

    User user = userMapper.loginById(id);

    if(user != null && passwordEncoder.matches(pw, user.getPw())) {
        model.addAttribute("user", user);
        return "redirect:/board/list";
    }

    return "home";
}
```





## Reference

블로그 : https://shj7242.github.io/2017/12/10/Spring31/