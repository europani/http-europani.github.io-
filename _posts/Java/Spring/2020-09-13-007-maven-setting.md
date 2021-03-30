---
layout: post
title: Spring 메이븐 설정
categories: Spring
tags: [Spring]
---

```xml
<?xml version="1.0"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://xmlns.jcp.org/xml/ns/javaee" xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd" id="WebApp_ID" version="4.0">
    <properties>
        <spring.version>4.3.10.RELEASE</spring.version>
    </properties>
    
    <dependencies>
    <!-- JUnit 테스트 프레임워크 -->
	<dependency>
	    <groupId>junit</groupId>
	    <artifactId>junit</artifactId>
	    <version>3.8.1</version>
	    <scope>test</scope>
	</dependency>
	<dependency>
	    <groupId>org.springframework</groupId>
	    <artifactId>spring-context</artifactId>
	    <version>${spring.version}</version>
	</dependency>
    <!-- JDBC -->
	<dependency>
	    <groupId>org.springframework</groupId>
	    <artifactId>spring-jdbc</artifactId>
	    <version>${spring.version}</version>
	</dependency>
    <!-- 트랜잭션 -->
	<dependency>
	    <groupId>org.springframework</groupId>
	    <artifactId>spring-tx</artifactId>
	    <version>${spring.version}</version>
	</dependency>
    <!-- MVC모델 -->
	<dependency>
	    <groupId>org.springframework</groupId>
	    <artifactId>spring-webmvc</artifactId>
	    <version>${spring.version}</version>
	</dependency>
    </dependencies>
</web-app>
```