---
layout: post
title: '[스프링] Java Configuration'
categories: Spring
tags: [Spring]
---

버전이 업그레이드 되면서 xml파일 보단 Java Configuration(어노테이션)을 사용함   
사용할때는 `AnnotationConfigApplicationContext(클래스명);`  
반환형에 class, 메소드 이름에 id를 입력함.

1\. **@Configuration**
- 해당 Class가 Spring 설정클래스임을 명시
- 이 클래스의 메서드에는 `@Bean` 팩토리 메서드가 들어있음

2\. **@Bean**
- 스프링 컨테이너에 빈(=클래스)을 등록시키기 위해 사용
- Bean 팩토리 **메서드**에 사용

3\. **@ComponentScan(패키지명)**
- 명시한 패키지에서 `@Controller`, `@Service`, `@Repository`, `@Component`, `@Configuration`가 붙은 **클래스**를 자동으로 스프링 컨테이너에 빈으로 등록
- 패키지를 명시하지 않으면 이 어노테이션이 붙은 패키지 레벨부터 등록
-  = `<context:component-scan base\-package="ems.member"\>`

```java
@Configuration
@ComponentScan("ems.member")	// member 패키지의 Component는 자동으로 등록 (아래 빈은 수동으로 등록)
public calss MemberConfig {

   /* in xml 
   <bean id="studentDao" class="ems.member.dao.StudentDao" />
   */
   @bean
   public StudentDao studentDao() {
    	return new StudentDato();
    }
    
   /* in xml 
   <bean id="registerService" class="ems.member.service.StudentRegisterService">
   	<constructor-arg ref="studentDao" />
   </bean>
   */
   @Bean
   public StudentRegisterService registerService() {
   	return new StudentRegisterService(studentDao());
   }
   
}

public class MainMethod {
    public static void main(String[] args) {
        /* applicationContext.xml 사용시
        ApplicationContext ac = new GenericXmlApplicationContext("classpath:applicationContext.xml")
        */
    	ApplicationContext ac = new AnnotationConfigApplicationContext(MemberConfig.class);
        StudentDao dao = ac.getBean(StudentDao.Class); 	// = (StudentDao) ac.getBean("studentDao");
    }
}
```

4\. **@Component**
- 해당 클래스를 빈으로 등록
- @ComponentScan의 대상이 되는 어노테이션 중 하나로써 주로 유틸, 기타 지원 **클래스**에 붙이는 어노테이션

5\. **@Controller, @Service, @Repository**
- 해당 클래스를 빈으로 등록
- `@Component` 하위에 속하는 어노테이션으로 각각 컨트롤러, 서비스, 레퍼지토리(DAO) 클래스 명시
- 특정 역할을 수행하는 클래스에 `@Component`대신 부여 
    -> but, 컨테이너에 등록시키기 위해서는 @ComponentScan을 꼭 사용해야 함.


6\. **@Autowired, @Resource**
- 주입 대상이되는 bean을 컨테이너에 찾아 주입하는 어노테이션

**7\. @RequestMapping, @GetMapping, @PostMapping, @DeleteMapping, @PutMapping, @PatchMapping**
- Http요청과 이를 다루기 위한 Controller의 메서드를 연결하는 어노테이션