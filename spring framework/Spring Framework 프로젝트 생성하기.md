#  Spring Project 만들기

## 1. Spring Legacy Project 생성하기 

* File > new > other > Spring Legacy Project 클릭
* Project name 입력 > Templates에서 Spring MVC Project 체크 후 Next
* 프로젝트 패키지 이름 입력 후 Finish 클릭
  * com.mycompany.myapp
  * 예시 : kr.green.spring
* 프로젝트 생성 후 패키지 클릭하여 F2 또는 우클릭 > refactor > rename 클릭하여 기존 패키지 이름에 .controller 추가한다.
  * 예시 :  kr.green.spring.controller

## 2. Server 생성하기

* File > new > other > Server 클릭
* 설치한 Tomcat 버전에 맞게 Tomcat 선택(예 : Tomcat v8.0 Server)
* Server runtime environment에 Add 클릭
* Tomcat installation directory에 Browse... 버튼 클릭
* 설치된 톰캣 폴더 선택 후 Finish 클릭(일반적으로 C:/Program Files/Apache Software Foundation 폴더에 버전별로 있으므로 선택한 버전과 맞는 폴더 선택)
  * 예 : C:\Program Files\Apache Software Foundation\Tomcat 8.0
* 선택사항 : Server name에 원하는 이름을 입력하면 됨
* Finish 클릭하여 Server 생성 완료

## 3. 테스트 하기

* 프로젝트 이름 우클릭 > Run as > Run on Server 클릭
* 생성한 서버 클릭 후 Next 클릭
* 1에서 생성한 프로젝트를 Add해 주고 Finish 클릭
* 서버가 실행되면 웹 브라우저에 URI (localhost:8080/프로젝트이름 입력)를 입력
* Hello world! 및 서버 시간 확인

## 4. home.jsp 인코딩 변경(UTF-8로)

* 수정 전 코드

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ page session="false" %>
<html>
<head>
    <title>Home</title>
</head>
<body>
<h1>
	Hello world!  
</h1>

<P>  The time on the server is ${serverTime}. </P>
</body>
</html>
```

* 수정 후 코드

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<html>
<head>
	<title>Home</title>
</head>
<body>
<h1>
	Hello world!  
</h1>

<P>  The time on the server is ${serverTime}. </P>
</body>
</html>

```

## 5. Spring Framework 버전 수정

* pom.xml 선택 후 아래 탭에 pom.xml 선택
* Spring Framework 버전 수정
* 수정 전 

```xml
<properties>
	<java-version>1.6</java-version>
	<org.springframework-version>3.1.1.RELEASE</org.springframework-version>
	<org.aspectj-version>1.6.10</org.aspectj-version>
    <org.slf4j-version>1.6.6</org.slf4j-version>
</properties>
```

* 수정 후

```xml
<properties>
	<java-version>1.6</java-version>
	<org.springframework-version>4.3.16.RELEASE</org.springframework-version>
	<org.aspectj-version>1.6.10</org.aspectj-version>
    <org.slf4j-version>1.6.6</org.slf4j-version>
</properties>
```

## 6. Java 버전 수정

* pom.xml에 버전 수정
  * pom.xml에 `<java-version>` 태그안에 있는 1.6을 1.8로 수정

```xml
<properties>
    <java-version>1.8</java-version>
    <org.springframework-version>4.3.16.RELEASE</org.springframework-version>
    <org.aspectj-version>1.6.10</org.aspectj-version>
    <org.slf4j-version>1.6.6</org.slf4j-version>
</properties>
```

* preferences 에서 Installed JREs 버전 설정
  * window메뉴에 preference 클릭
  * java > installed JREs 클릭
  * 현재 선택된 버전이 1.8인지 확인하여 맞으면 Apply and close
  * 아니면 Add버튼 클릭
  * Add JRE창에서 Standard VM 클릭
  * JRE home에서 Directory... 클릭
  * 설치된 JDK 폴더 선택( C:\Program Files\Java\jdk 1.8xx)
  * Finish 버튼 클릭
* 프로젝트 버전 자바로 바꾸기
  * 프로젝트 우클릭 > Properties 클릭
  * Project Facets 클릭
  * Java 버전을 1.8 선택 후 Apply 클릭
  * 다시 Properties에  Java Build Path 클릭
  * JRE System Library 클릭
  * 현재 체크된 값의 버전이 jdk 1.8인지 확인
  * 아니면 Alternate JRE 라디오 버튼 클릭
  * jdk 1.8버전 선택 후 Finish 클릭





 