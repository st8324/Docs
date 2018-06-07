# Spring 인코딩 설정 UTF-8

## 1. web.xml에 아래 코드를 추가

```xml
<filter>
	<filter-name>encoding</filter-name>
	<filter-class>
		org.springframework.web.filter.CharacterEncodingFilter
	</filter-class>
	<init-param>
		<param-name>encoding</param-name>
		<param-value>UTF-8</param-value>
    </init-param>
</filter>
    
<filter-mapping>
	<filter-name>encoding</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>
```

## 2. 이클립스 UTF-8 설정

1. 프로젝트 인코딩 설정
   * 메뉴 Window > Preferences 클릭
   * General > Workspace 클릭
   * Text file encoding> Other에서 UTF-8 설정 후 Apply
2. JSP 인코딩 설정
   * 메뉴 Window > Preferences 클릭
   * Web > JSP Files 클릭
   * Encoding에서 ISO 10646/Unicode(UTF-8) 선택 후 Apply

