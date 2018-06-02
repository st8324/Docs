## Mybatis 설정

## 1. 의존성 추가

* pom.xml 클릭 > pom.xml 탭 선택 후 아래 의존성 추가
* 위치는 `</depenedncies>` 바로 위
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

## 2. 네임스페이스 추가

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

## 3. mysql 정보를 root-context.xml에 추가

* 위치는 `</beans>` 바로 위

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

## 4. UserMapper 인터페이스 추가(UserMapper.java)

* File > New > other > Package 선택
* 패키지 이름 입력 후 Finish 클릭
  * com.mycompany.myapp.dao
  * 예시 : kr.green.spring.dao
* 인터페이스 추가 및 필요한 메소드 추가

```java
//예시
package kr.green.spring.dao;
import org.apache.ibatis.annotations.Param;
public interface UserMapper {
	//테스트를 위한 메소드
	public String getEmail(@Param("id") String id);
}
```

## 5. UserMapper.xml 추가

* 위치 : /src/main/resources/mappers

```xml
<!-- 예시 -->
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="kr.green.spring.dao.UserMapper">
    <select id="getEmail" resultType="String">
		select email from account where id = #{id}
	</select>
</mapper>
```

## 6. root-context.xml에 Mapper 정보 설정

* 위치 : `</beans>` 바로 위

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
    <property name="mapperInterface" value="kr.green.spring.dao.UserMapper"></property>
    <property name="sqlSessionFactory" ref="sqlSessionFactory"></property>
</bean>
<mybatis-spring:scan base-package="kr.green.spring.dao"/>
```

# 테스트

## HomeController에서 테스트 코드 작성

1. HomeController에 UserMapper를 멤버 객체로 추가한 후 @Autowired 어노테이션 추가

```java
@Autowired
UserMapper userMapper;
```

2. 기본 메소드인 home메소드 수정

```java
@RequestMapping(value = "/", method = RequestMethod.GET)
public String home(Model model, HttpServletRequest request) {
    String id = request.getParameter("id");
    String pw = request.getParameter("pw");
    if(userMapper.getEmail(id)!= null)
    	System.out.println("DB access success! ");

    return "home";
}
```

```java
//최종 HomeController
package kr.green.spring;

import javax.servlet.http.HttpServletRequest;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

import kr.green.spring.dao.UserMapper;

@Controller
public class HomeController {
	
	@Autowired
	UserMapper userMapper;
	
	@RequestMapping(value = "/", method = RequestMethod.GET)
	public String home(Model model, HttpServletRequest request) {
		String id = request.getParameter("id");
		String pw = request.getParameter("pw");
		if(userMapper.getEmail(id)!= null)
			System.out.println("DB access success! ");
		
		return "home";
	}
}
```







 