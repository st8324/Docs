# Spring 과 Mybatis 연동하기

## 1. Mysql 설치

* **주의사항 : Mysql 5.7버전으로 설치**
* [Mysql 다운로드 링크](https://dev.mysql.com/downloads/installer/)
* 5.7버전 설치를 위해 아래 그림의 빨간 박스를 클릭하여 다운로드

![mysql](../imgs/mybatis/mysqinstall.png)

* mysql 설치 시 root 계정 이외에 다른 계정 하나 생성

## 2. 스키마 및 테이블 생성 



## 3. Spring 프로젝트 의존성 추가(pom.xml)

1. mybatis 추가

~~~ xml
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.1</version>
</dependency>
~~~

2. mybatis-spring 추가

~~~xml
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>1.3.0</version>
</dependency>
~~~

3. mysql-connector-java 추가

~~~xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>6.0.5</version>
</dependency>
~~~

4. spring-jdbc 추가

~~~xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>${org.springframework-version}</version>
</dependency>
~~~

## 4. root-context.xml 수정

1. 네임스페이스 추가 
   * root-context.xml 파일을 열어 namespace 탭 클릭
   * aop, beans, context, jdbc, mybatis-spring 선택 후 저장

![](imgs\mybatis\root-context_namespace.png)

2. mysql 정보 추가

~~~xml
<bean id="dataSource"
      class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"></property>
    <property name="url"
              value="jdbc:mysql://localhost:3306/스키마이름?useSSL=false&amp;serverTimezone=UTC">
    </property>
    <property name="username" value="mysql계정아이디"></property>
    <property name="password" value="mysql계정비번"></property>
</bean> 
~~~

* 예시

~~~xml
<bean id="dataSource"
      class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"></property>
    <property name="url"
              value="jdbc:mysql://localhost:3306/account?useSSL=false&amp;serverTimezone=UTC">
    </property>
    <property name="username" value="stajun"></property>
    <property name="password" value="stajun"></property>
</bean>  
~~~

3. Mapper 설정

~~~xml
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource" />
    <property name="mapperLocations" value="classpath:경로/*Mapper.xml"></property>
</bean>
<bean id="Mapper" class="org.mybatis.spring.mapper.MapperFactoryBean">
    <property name="mapperInterface" value="매퍼인터페이스이름"></property>
    <property name="sqlSessionFactory" ref="sqlSessionFactory"></property>
</bean>
<mybatis-spring:scan base-package="맵핑할패키지이름"/>
~~~

* 예시

~~~xml
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource" />
    <property name="mapperLocations" value="classpath:mappers/**/*Mapper.xml"></property>
</bean>
<bean id="Mapper" class="org.mybatis.spring.mapper.MapperFactoryBean">
    <property name="mapperInterface" value="kr.green.spring.dao.Mapper"></property>
    <property name="sqlSessionFactory" ref="sqlSessionFactory"></property>
</bean>
<mybatis-spring:scan base-package="kr.green.spring.controller.dao"/>
~~~

## 5. Mapper 인터페이스 추가

1. 인터페이스 생성 추가 및 필요한 함수 생성

   * 예시 

   ~~~java
   package kr.green.spring.dao;
   
   import org.apache.ibatis.annotations.Param;
   
   public interface Mapper {
   	public String getId(@Param("id") String id);
   }
   
   ~~~

   

## 6. Mapper.xml 파일 설정

1. Mapper.xml 파일 생성

   * src/main/resources/mappers

2. 코드 작성 예시

   ~~~xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
     PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
     "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="kr.green.spring.dao.Mapper">
     <select id="id" resultType="String">
       select id from account where id = #{id}
     </select>
   </mapper>
   ~~~



## 7.Controller 코그 작성 

1. 작성 예시

~~~java
@RequestMapping(value = "/index", method = RequestMethod.GET)
public String index(Model model) {
    return "index";
}
@RequestMapping(value="/test",method=RequestMethod.POST)
public String test(HttpServletRequest request, Model model) {
    String id = request.getParameter("id");
    System.out.println(id);
    model.addAttribute("id", mapper.getId(id));
    return "list";
}
~~~

## 8. jsp 작성

1. 작성 예시

* index.jsp

~~~jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>
<% String context = request.getContextPath(); %>
<form action="/test" method="post">
	<input type="text" name="id">
	<input type="submit" value="제출">
</form>
</body>
</html>
~~~

   *  list.jsp

~~~jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>
${id}
</body>
</html>
~~~



