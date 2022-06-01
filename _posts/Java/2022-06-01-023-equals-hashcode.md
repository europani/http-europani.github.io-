---
layout: post
title: '[JAVA] equals와 hashCode 재정의'
categories: Java
tags: [Java]
---
사용자 정의 클래스를 만들 때 `hashCode()`, `equals()`, `toString()` 를 재정의한다.  
여기서 `hashCode()`, `equals()`는 객체의 동등성과 관련이 있다

- 동일성(identity) : 두 객체가 완전히 같다는 것을 의미한다
  - **객체의 인스턴스 주소를 비교**
  - `==` 를 사용
- 동등성(equality) : 두 객체의 정보가 같다는 것을 의미한다
  - **객체 내부의 값을 비교**
  - `equals()`를 사용

- 단, 기본 타입(Primitive)는 `==`로 값을 비교할 수 있다

### equals()
- 동등성 비교를 위해 사용하는 메서드

```java
class Main {
    static class Person {
        String name;
        int age;

        public Person(String name, int age) {
            this.name = name;
            this.age = age;
        }
    }   
 
    public static void main(String[] args) {
        Person p1 = new Person("aoh", 20);
        Person p2 = new Person("aoh", 20);
      
        System.out.println(p1.hashCode());      // 746292446
        System.out.println(p2.hashCode());      // 1072591677
        System.out.println(p1==p2);             // false
        System.out.println(p1.equals(p2));      // false
    }
}
```
- 두 객체의 정보가 같지만 다른 메모리 주소를 갖는 객체이기 때문에 `p1==p2`에서 false가 나온다
- 하지만 `p1.equals(p2)`에서도 false가 나오게 되는데 이유는 `Object.equals()` 때문이다.
  - 기본적으로 동일성을 비교한다

```java
public class Object{
    ...
    public boolean equals(Object obj) {
        return (this == obj);
    }
    ...
}
```
- 기본적으로 동일성을 비교하기 때문에 오버라이딩을 해서 동등성 비교로 기준을 재정의 해야한다
- `String.equals()`는 동일성 비교를 위해 잘 정의되어 있으니 이를 사용하는 것도 좋다

### hashCode()
- 기본적으로 객체의 메모리 주소를 반환하는 메서드
- 모든 객체는 다른 공간에 할당되기 때문에 다른 메모리 주소를 갖게 된다. 즉, 모든 객체는 기본적으로 다른 해시코드를 갖는다
- 논리적으로 두 인스턴스가 같다면 같은 해시코드를 반환하도록 재정의 해야한다
- 해시 자료구조는 같은 객체인지 판단할 때 해시값을 사용하게 된다. 논리적으로 같은 인스턴스를 같은 객체로 판단하기 위해 재정의가 필요하다

- 재정의시 `Objects.hash(args)`를 사용하면 쉽게 재정의가 가능하다


```java
class Main {
    static class Person {
        String name;
        int age;

        public Person(String name, int age) {
            this.name = name;
            this.age = age;
        }

        public int hashCode() {
            return Objects.hash(name, age);
        }

        public boolean equals(Object o) {
            Person p = (Person) o;
            return (name.equals(p.name)) && age==p.age;
        }
    }   
 
    public static void main(String[] args) {
        Person p1 = new Person("aoh", 20);
        Person p2 = new Person("aoh", 20);
      
        System.out.println(p1.hashCode());    // 3000603
        System.out.println(p2.hashCode());    // 3000603
        System.out.println(p1==p2);           // false
        System.out.println(p1.equals(p2));    // true
    }
}
```
- name과 age의 값으로 해시 코드를 생성하도록 하기 때문에 같은 해시코드를 반환하게 된다
- `p1==p2` : 두 객체는 다른 객체로써 다른 메모리 주소값을 갖기 때문에 `false`를 반환
- `p1.equals(p2)` : name과 age의 값을 비교하도록 재정의 했기 때문에 `true`를 반환


### equals만 오버라이드 하는 경우

```java
class Main {
    static class Person {
        String name;
        int age;

        public Person(String name, int age) {
            this.name = name;
            this.age = age;
        }

        @Override
        public boolean equals(Object o) {
            Person p = (Person) o;
            return (name.equals(p.name)) && age==p.age;
        }

        @Override
        public String toString() {
            return name + ":" + age;
        }
    }   
 
    public static void main(String[] args) {
        HashSet<Person> set = new HashSet();

        set.add(new Person("aoh", 20));
        set.add(new Person("aoh", 20));
        System.out.println(set);    //  [aoh:20, aoh:20]
    }
}
```
- 해시코드가 다르기 때문에 HashSet은 다른 객체로 인식한다

### hashCode만 오버라이드 하는 경우 
- `equals()`를 통해 동등성을 판단하는데 재정의를 하지 않으면 알다시피 `Object.equals()`를 사용하기 때문에 동일성을 비교하게 된다
