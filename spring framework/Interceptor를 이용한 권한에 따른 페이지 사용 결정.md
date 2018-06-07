# Interceptor를 이용한 권한에 따른 페이지 사용 결정

## 1. AuthInterceptor 생성

1. HandlerInterceptorAdapter를 상속받은 AuthInterceptor 생성
2. preHandle 메소드 오버라이딩
   * 세션에 user 정보를 가져와서 null값이면 로그인 화면으로 보냄
   * 정보가 있으면 그냥 넘어감

```java
package kr.green.springwebproject.interceptor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import org.springframework.web.servlet.handler.HandlerInterceptorAdapter;

public class AuthInterceptor extends HandlerInterceptorAdapter{
	
	@Override
	public boolean preHandle(HttpServletRequest request, 
			HttpServletResponse response, 
			Object handler)
			throws Exception {
		HttpSession session = request.getSession();
		Object user = session.getAttribute("user");
		if(user == null) {
			response.sendRedirect("/");
		}
		return true;
	}
}
```

## 2. servlet-context.xml에 AuthInterceptor 추가

1. kr.green.spring.interceptor.AuthInterceptor를 authInterceptor로 bean에 추가

```xml
<beans:bean id="authInterceptor" 
	class="kr.green.spring.interceptor.AuthInterceptor"></beans:bean>
```

2. 로그인 후 접근해야 하는 경로(URI) 가 주어졌을 때 authInterceptor가 호출하도록 interceptor태그에 등록

```xml
<interceptors>
	<interceptor>
        <mapping path="/board/*"/>
        <beans:ref bean="authInterceptor"/>
	</interceptor>
</interceptors>
```

3. 최종적으로 servlet.context.xml에 추가한 코드 - 기존에 추가한 loginInterceptor 포함

```xml
<beans:bean id="loginInterceptor" 
	class="kr.green.springwebproject.interceptor.LoginInterceptor"></beans:bean>
<beans:bean id="authInterceptor" 
	class="kr.green.springwebproject.interceptor.AuthInterceptor"></beans:bean>
<interceptors>
    <interceptor>
        <mapping path="/"/>
        <beans:ref bean="loginInterceptor"/>
    </interceptor>
    <interceptor>
        <mapping path="/board/*"/>
        <beans:ref bean="authInterceptor"/>
    </interceptor>
</interceptors>
```

