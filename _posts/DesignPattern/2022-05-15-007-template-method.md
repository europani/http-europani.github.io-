---
layout: post
title: 행동 (2) - 템플릿 메소드(Template Method) 패턴
categories: [Design Pattern]
tags: [Design Pattern]
---
- 추상 클래스로 템플릿을 제공하고 서브 클래스에서 구체적으로 구현하는 패턴
- 즉, 특정 작업을 처리하는 부분을 서브 클래스로 캡슐화하여 수행하도록 설정
- 전체적인 흐름은 동일하면서 부분적으로 코드가 다른 경우 사용하면 코드 중복을 최소화 할 수 있다

![image](https://user-images.githubusercontent.com/48157259/168474647-0f3bf11b-e80a-469d-ae99-79b7faadcf0e.png)

- Abstract Class : 템플릿을 제공하는 추상클래스
- Concrete Class : 특정 작업을 처리하는 서브클래스

### 구현
- Abstract Class

```java
public abstract class Teacher {
    public void work() {
        inside();
        attendance();
        teach();
        outside();
    }

    // 서브 클래스에서 확장이 필요한 부분
    public abstract void teach();
	
    // 공통 메서드
    private void inside() {
        System.out.println("선생님이 강의실로 들어옵니다.");
    }
    private void attendance() {
        System.out.println("선생님이 출석을 부릅니다.");
    }
    private void outside() {
        System.out.println("선생님이 강의실을 나갑니다.");
    }
    
}
```

- Concrete Class

```java
class KoreanTeacher extends Teacher{
    @Override
    public void teach() {
        System.out.println("선생님이 국어를 수업합니다.");
    }
}
 
class MathTeacher extends Teacher {
    @Override
    public void teach() {
        System.out.println("선생님이 수학을 수업합니다.");
    }
}

class EnglishTeacher extends Teacher {
    @Override
    public void teach() {
        System.out.println("선생님이 영어를 수업합니다.");
    }
}
```

- Main

```java
public class Main {
    public static void main(String args[]){
        Teacher kr = new KoreanTeacher();
        Teacher mt = new MathTeacher();
        Teacher en = new EnglishTeacher();

        kr.work();
        mt.work();
        en.work();
    }
}
```

#### 익명 내부클래스 사용
- 클래스가 계속 늘어나는 것을 막기 위해 익명 내부클래스를 사용할 수도 있다

```java
public class Main {
    public static void main(String args[]){
        Teacher teacher = new Teacher() {
            @Override
            public void teach() {
                System.out.println("선생님이 수업합니다.");
            }
        };
        teacher.work();
    }
}
```

### 장점
1. 중복 코드를 줄일 수 있다
2. 템플릿 코드를 변경하지 않고 상속받아 특정부분만 변경할 수 있다


### 단점
1. LSP를 위배할 수도 있다
   - 자식클래스는 부모클래스의 기능을 전혀 사용하고 있지 않다 
2. 상속을 통해 부모클래스를 의존하고 있다 
   - 부모클래스에 변경이 일어나면 자식클래스에도 영향을 미친다
3. 알고리즘 구조가 복잡할 수록 템플릿을 유지하기 어려워진다

- 템플릿 메소드보다 전략패턴을 선택하면 1, 2번을 해결할 수 있다