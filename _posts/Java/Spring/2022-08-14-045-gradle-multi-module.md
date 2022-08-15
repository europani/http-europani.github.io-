---
layout: post
title: 'Gradle 멀티 모듈'
categories: Spring
tags: [Spring]
---
하나의 스프링 프로젝트에서 하나의 모듈만 사용하지 않고 여러 모듈을 사용해서 분리 할 수 있다  
예를 들어, 하나의 프로젝트에 사용자(web)와 관리자(admin) 모듈을 분리해 관리할 수 있다  
또, 여러 모듈에서 사용하는 공통된 모듈을 만들어 재사용할 수 있다

- 모듈을 분리해 관리하면 빌드도 쉽게 할 수 있다
    - `./gradlew:{moduleName}:build`

- 프로젝트 구조
   - Root Project : myapp
   - Sub Module : app-web, app-admin, common 

```
myapp
├─ app-web
│  ├─ src
│  └─ build.gradle
├─ app-admin
│  ├─ src
│  └─ build.gradle
├─ common
│  ├─ src
│  └─ build.gradle
│
├─ .gitignore
├─ gradlew
├─ gradlew.bat
├─ build.gradle
└─ settings.gradle [Root Project 설정파일]
```

### Root Project
- settings.gradle

```gradle
rootProject.name = 'myapp'

// 하위 모듈 include
include 'app-web'
include 'app-admin'
include 'common'
```

- build.gradle

```gradle
buildscript {
    ext {      // 변수 선언
        h2Version = '1.4.199'
    }
}

plugins {
    id 'org.springframework.boot' version '2.4.5'
    id 'io.spring.dependency-management' version '1.0.11.RELEASE'
    id 'java'
}

subprojects {
    apply plugin: 'org.springframework.boot'
    apply plugin: 'io.spring.dependency-management'
    apply plugin: 'java'

    group = 'com.sample'
    version = '1.0.0'
    sourceCompatibility = '11'

    configurations {
        compileOnly {
            extendsFrom annotationProcessor
        }
    }

    repositories {
        mavenCentral()        
    }    

    dependencies {
        compileOnly "org.projectlombok:lombok"
        annotationProcessor "org.projectlombok:lombok"

        testCompileOnly "org.projectlombok:lombok"
        testAnnotationProcessor "org.projectlombok:lombok"

        runtimeOnly 'com.h2database:h2:${h2Version}'
    }

    test {
        useJUnitPlatform()
    }
}
```

### common
- 공통 모듈
- common 모듈에는 main 메서드가 없기 때문에 `bootJar`, `jar` 설정을 다음과 같이 해야 한다

- build.gradle

```gradle
description = "common module"

bootJar { 
   enabled = false 
}
jar { 
   enabled = true 
}

dependencies {
   
   // spring-boot
   implementation 'org.springframework.boot:spring-boot-starter'
   implementation 'org.springframework.boot:spring-boot-starter-web'
   implementation 'org.springframework.boot:spring-boot-starter-actuator'
   implementation 'org.springframework.boot:spring-boot-starter-validation'
}
```

### app-web
- 사용자용 모듈

- build.gradle

```gradle
description = "app-web module"

dependencies {
    compile project(":common")

    implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
}
```

### app-admin
- 관리자용 모듈

- build.gradle

```gradle
description = "app-admin module"

dependencies {
    compile project(":common")

    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
}
```

### 모듈 설정파일 include
모듈을 분리해서 공통 모듈을 사용할 때 설정파일 포함시켜야 할 때가 있다

```yml
spring:
  profiles:
    include:
      - common
...
```