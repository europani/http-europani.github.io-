---
layout: post
title: Xml과 Java Config
categories: Spring
tags: [Spring]
---

#### Xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    
    <bean id="studentDao" class="ems.member.dao.StudentDao" />
    
    <bean id="registerService" class="ems.member.service.StudentRegisterService">
        <constructor-arg ref="studentDao" />
    </bean>
    
</beans>
```

```java
public class MainMethod {
    public static void main(String[] args) {
        ApplicationContext ac = new GenericXmlApplicationContext("classpath:applicationContext.xml")
        StudentDao dao = ac.getBean(StudentDao.Class);
    }
}
```

#### Java Config

```java
@Configuration
public class MemberConfig {

   @bean
   public StudentDao studentDao() {
       return new StudentDato();
    }
    
   @Bean
   public StudentRegisterService registerService() {
   	return new StudentRegisterService(studentDao());
   }
   
}
```

```java
public class MainMethod {
    public static void main(String[] args) {
        ApplicationContext ac = new AnnotationConfigApplicationContext(MemberConfig.class);
        StudentDao dao = ac.getBean(StudentDao.Class);
    }
}
```

### Xml과 Java Config 혼합

#### 1) Java  **in Xml**

 - \<bean class="자바config파일" />

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    
    <context:annotation-config />
    <bean class="ch01.service.Board" />			<!-- 혼합시킬 자바 Config -->
    
    <bean id="studentDao" class="ems.member.dao.StudentDao" />
    
    <bean id="registerService" class="ems.member.service.StudentRegisterService">
        <constructor-arg ref="studentDao" />
    </bean>
    
</beans>
```

#### 2) Xml **in Java**

 - @ImportResource("xml파일")

```java
@ImportResource("classpath:/ch01/service/configBoard.xml")	// 혼합시킬 xml
@Configuration
public class MemberConfig {

   @bean
   public StudentDao studentDao() {
       return new StudentDato();
    }
    
   @Bean
   public StudentRegisterService registerService() {
   	return new StudentRegisterService(studentDao());
   }
   
}
```

---

### Configuration

#### 1) Xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans:beans xmlns="http://www.springframework.org/schema/mvc"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:beans="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd
		http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

	<!-- Enables the Spring MVC @Controller programming model -->
	<annotation-driven />
	<!-- scan package range -->
	<context:component-scan base-package="com.demo" />
    
	<mvc:defalut-servlet-handler />
	<mvc:view-controller path="/" view-name="index />
    
	<!-- Handles HTTP GET requests for /resources/** by efficiently serving up static resources in the ${webappRoot}/resources directory -->
	<resources mapping="/resources/**" location="/resources/" />

	<!-- Resolves views selected for rendering by @Controllers to .jsp resources in the /WEB-INF/views directory -->
	<beans:bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	    <beans:property name="prefix" value="/WEB-INF/views/" />
	    <beans:property name="suffix" value=".jsp" />
	</beans:bean>
    
</beans:beans>

```

#### 2) Java Config

```java
@EnableWebMvc
@ComponentScan(basePackages = {"com.demo"})
@Configuration
public class ServletConfig extends WebMvcConfigurerAdapter {
	
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable();
    }
    @Override
    public void addViewControllers(final ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("index");
    }
	
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/resources/**").addResourceLocations("/resources/");
    }
	
    @Override
    public void configureViewResolvers(ViewResolverRegistry registry) {
        InternalResourceViewResolver resolver = new InternalResourceViewResolver();
        resolver.setPrefix("/WEB-INF/views/");
        resolver.setSuffix(".jsp");
        registry.viewResolver(resolver);
    }
}
```