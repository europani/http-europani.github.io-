---
layout: post
title: 생성 (4) - 추상 팩토리(Abstract Factory) 패턴
categories: [Design Pattern]
tags: [Design Pattern]
---
- 서로 관련있는 여러 객체를 캡슐화해 한번에 만들어주는 인터페이스를 제공

### 용어
![image](https://user-images.githubusercontent.com/48157259/200110046-67077225-68ad-4e4e-b316-f96d3e7b3d8c.png)

- Abstract Product : 제품의 공통 인터페이스
- Concrete Product : 팩토리에 의해 생성될 구체적인 객체
- Abstract Factory : 팩토리 클래스의 공통 인터페이스
- Concrete Factory : 구체적인 팩토리 클래스

### 구현
- Product

```java
public interface Keyboard {
}

public class SamsungKeyboard implements Keyboard {
    public SamsungKeyboard(){
        System.out.println("Samsung 키보드 생성");
    }
}

public class LGKeyboard implements Keyboard {
    public LGKeyboard(){
        System.out.println("LG 키보드 생성");
    }
}
```

```java
public interface Mouse {
}

public class SamsungMouse implements Mouse {
    public SamsungMouse(){
        System.out.println("Samsung 마우스 생성");
    }
}

public class LGMouse implements Mouse {
    public LGMouse(){
        System.out.println("LG 마우스 생성");
    }
}
```

- Concrete Factory

```java
public interface ComputerPartsFactory {
    public Keyboard createKeyboard();
    public Mouse createMouse();
}

public class SamsungComputerPartsFactory implements ComputerPartsFactory {
    @Override
    public SamsungKeyboard createKeyboard() {
        return new SamsungKeyboard();
    }

    @Override
    public SamsungMouse createMouse() {
        return new SamsungMouse();
    }
}

public class LGComputerPartsFactory implements ComputerPartsFactory {
    @Override
    public LGKeyboard createKeyboard() {
        return new LGKeyboard();
    }

    @Override
    public LGMouse createMouse() {
        return new LGMouse();
    }
}
```

- Abstract Factory
    - 여러 객체를 조합해서 생성하는 Factory 클래스
    - 다른 파츠 팩토리가 추가되어도 변경 될 일이 없음

```java
public class ComputerFactory {

    private ComputerPartsFactory computerPartsFactory;

    public ComputerFactory(ComputerPartsFactory computerPartsFactory) {
        this.computerPartsFactory = computerPartsFactory;
    }

    public void createComputer(){
        computerPartsFactory.createKeyboard();
        computerPartsFactory.createMouse();
    }
}
```

- Client

```java
public class Main {

    public static void main(String[] args) {
        ComputerFactory computerFactory = new ComputerFactory(new LGComputerPartsFactory());
        computerFactory.createComputer();
    }
}
```

### 팩토리 메서드 vs 추상 팩토리
- 팩토리 매서드
    - 한 종류의 객체를 만든다
    - 팩토리를 구현하는 방법(inheritance)에 포커스
    - 구체적인 객체 생성 과정을 하위로 옮기는 것이 목적
- 추상 팩토리
    - 연관되거나 의존하는 객체로 이루어진 여러 종류의 객체를 만든다
    - 팩토리를 사용하는 방법(composition)에 포커스
    - 관련있는 여러 객체를 구체적인 클래스에 의존하지 않고 만드는 것이 목적