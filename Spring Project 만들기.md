#  Spring Project 만들기

## 1. Spring Legacy Project 생성하기 

* File > new > other > Spring Legacy Project 클릭
* Project name 입력 > Templates에서 Spring MVC Project 체크 후 Next
* 프로젝트 패키지 이름 입력 후 Finish 클릭
  * com.mycompany.myapp.controller
  * 예시 : kr.green.springwebproject.controller

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

## 6. Mybatis 설정

1. 의존성 추가
   * pom.xml 클릭 > pom.xml 탭 선택 후 아래 의존성 추가
   * 위치는 </depenedncies> 바로 위
   * mybatis 의존성 추가
   * mybatis-spring 의존성 추가
   * mysql-connector-java 의존성 추가
   * spring-jdbc 의존성 추가

```xml
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.1</version>
</dependency>
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>1.3.0</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>6.0.5</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>${org.springframework-version}</version>
</dependency>
```

2. 네임스페이스 추가
   * /src/main/webapp/WEB_INF/spring/root-contexnt.xml 수정

```xml
<!-- 수정전 -->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	
	<!-- Root Context: defines shared resources visible to all other web components -->
		
</beans>
```

```xml
<!-- 수정후 -->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:jdbc="http://www.springframework.org/schema/jdbc"
	xmlns:mybatis-spring="http://mybatis.org/schema/mybatis-spring"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc-4.3.xsd
		http://mybatis.org/schema/mybatis-spring http://mybatis.org/schema/mybatis-spring-1.2.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd">
	
	<!-- Root Context: defines shared resources visible to all other web components -->
		
</beans>
```

3. mysql 정보를 root-context.xml에 추가
   * 위치는 </beans> 바로 위

```xml
<!-- 기본 형식 -->
<bean id="dataSource"
      class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"></property>
    <property name="url"
              value="jdbc:mysql://localhost:3306/DB이름?useSSL=false&amp;serverTimezone=UTC">
    </property>
    <property name="username" value="mysql계정아이디"></property>
    <property name="password" value="mysql계정비번"></property>
</bean> 
```

```xml
<!-- 예시 -->
<bean id="dataSource"
      class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"></property>
    <property name="url"
              value="jdbc:mysql://localhost:3306/account?useSSL=false&amp;serverTimezone=UTC">
    </property>
    <property name="username" value="stajun"></property>
    <property name="password" value="stajun"></property>
</bean>  
```

4. UserMapper 인터페이스 추가(UserMapper.java)
   * File > New > other > Package 선택
   * 패키지 이름 입력 후 Finish 클릭
     * com.mycompany.myapp.dao
     * 예시 : kr.green.springwebproject.dao
   * 인터페이스 추가 및 필요한 메소드 추가

```java
//예시
package kr.green.springwebproject.dao;
import org.apache.ibatis.annotations.Param;
public interface UserMapper {
	//테스트를 위한 메소드
	public String getPw(@Param("id") String id);
}
```

5. UserMapper.xml 추가
   * 위치 : /src/main/resources/mappers

```xml
<!-- 예시 -->
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="kr.green.springwebproject.dao.UserMapper">
    <select id="getPw" resultType="String">
		select pw from account where id = #{id}
	</select>
</mapper>
```

6. root-context.xml에 Mapper 정보 설정
   * 위치 : </beans> 바로 위

```xml
<!-- 기본 예시 -->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource" />
    <property name="mapperLocations" value="classpath:경로/*Mapper.xml"></property>
</bean>
<bean id="맴퍼이름" class="org.mybatis.spring.mapper.MapperFactoryBean">
    <property name="mapperInterface" value="매퍼인터페이스이름"></property>
    <property name="sqlSessionFactory" ref="sqlSessionFactory"></property>
</bean>
<mybatis-spring:scan base-package="맵핑할패키지이름"/>
```

```xml
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource" />
    <property name="mapperLocations" value="classpath:mappers/**/*Mapper.xml"></property>
</bean>
<bean id="userMapper" class="org.mybatis.spring.mapper.MapperFactoryBean">
    <property name="mapperInterface" value="kr.green.springwebproject.dao.UserMapper"></property>
    <property name="sqlSessionFactory" ref="sqlSessionFactory"></property>
</bean>
<mybatis-spring:scan base-package="kr.green.springwebproject.dao"/>
```







 