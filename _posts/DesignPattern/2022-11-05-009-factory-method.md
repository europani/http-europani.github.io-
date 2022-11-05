---
layout: post
title: 생성 (3) - 팩토리 메서드(Factory Method) 패턴
categories: [Design Pattern]
tags: [Design Pattern]
---
- 상위 클래스는 인터페이스이며 구체적인 객체의 생성은 하위 클래스에게 위임
- 템플릿 메서드와 매우 비슷하며 템플릿 메서드 패턴의 생성버전이라고 생각할 수 있다
- 객체 생성 클래스를 분리하여 기존 코드를 수정하지 않고 변화에 확장하기 위한 패턴

### 용어
![image](https://user-images.githubusercontent.com/48157259/200107973-91d30ad9-1201-4041-8ec1-3eb53c8cc9bf.png)

- Product : 팩토리 메서드로 생성될 객체
- Creator : 팩토리 메서드 클래스
- Concrete Creator : 실제 객체를 생성하는 하위 클래스

### 구현
- Product

```java
public interface User {
    void signup();
}

public class GoogleUser implements User {
    @Override
    public void signup() {
        System.out.println("Google Signup!!");
    }
}
```

- Creator (추상 클래스)
    - 팩토리 메서드로써 객체 생성을 위한 호출을 한다. 다만, 구체적인 클래스의 생성은 하위 클래스에서 한다

```java
public abstract class UserFactory {

    public User newInstance() {
        User user = createUser();
        user.signup();
        return user;
    }
    protected abstract User createUser();
}
```

- Concrete Creator (하위 클래스)

```java
public class GoogleUserFactory extends UserFactory {
    @Override
    protected User createUser() {
        return new GoogleUser();
    }
}
```

- Client

```java
public class Main {

    public static void main(String[] args) {
        UserFactory userFactory = new GoogleUserFactory();
        User user = userFactory.newInstance();
    }
}
```

### 장점
1. 기존 코드를 변경하지 않고 새로운 객체의 생성을 추가할 수 있다 [OCP]


### 단점
1. 클래스의 갯수가 늘어난다