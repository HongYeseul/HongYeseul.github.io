---
title: "[Java 16] 레코드(record)를 알아보자"
description: record를 사용하는 이유와 방법을 알아보자
slug: record
date: 2023-11-05 00:00:00+0000
# image: cover.jpg
categories:
    - 알아두면 좋은 개발 지식
tags:
    - Java
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

## record란?
간단히 설명하자면 "데이터 클래스"이며 순수하게 (불변)데이터를 보유하기 위한 특수한 종류의 클래스다. 이를 사용하면 데이터를 객체 간에 전달하는 작업을 간단하게 만들어준다.
- JDK14에서 preview로 등장하여 JDK16에서 정식 기능으로 포함


## 예시
String 값으로 name, Integer 값으로 age라는 변수를 갖고 있는 ```User```라는 클래스 구현한다고 가정하자.
- 일반 클래스로 구현한 User 클래스
  ``` java
  public class User {
     private final String name;
     private final Integer age;

     public User(String name, Integer age) {
        this.name = name;
        this.age = age;
     }

     public String getName() {
        return name;
     }

     public Integer getAge() {
        return age;
     }
  }
  ```
- record를 사용하여 구현한 User 클래스
  ``` java
  public record User (String name, String address) {}
  ```

## 특징
- record 클래스는 final 클래스라 상속할 수 없다.
- 각 멤버 변수는 private final로 정의된다.
- getter가 자동으로 생성된다.
  - 각 멤버 변수의 getter는 getXXX()가 아닌, 변수명을 딴 getter가 생성된다.(```User.name()```, ```User.age()```로 사용)
- 모든 멤버변수를 인자로 하는 public 생성자를 자동으로 생성하지만,
  기본 생성자는 만들지 않으므로 필요한 경우 직접 구현해야 한다.
- ```equals()```, ```hashcode()```, ```toString()```을 자동으로 생성한다.

때문에 불필요한 코드들을 쉽게 제거가 가능하다!

---
### 참고
- https://coding-start.tistory.com/355
- https://colevelup.tistory.com/28
- https://s7won.tistory.com/2