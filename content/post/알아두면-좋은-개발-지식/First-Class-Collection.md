---
title: 일급 컬렉션(First Class Collection)을 사용하는 이유
description: 일급 컬렉션을 사용하는 이유와 방법을 알아보자
slug: first-class-collection
date: "2023-11-04"
# image: cover.jpg
categories:
    - 알아두면 좋은 개발 지식
tags:
    - Java
    - 설계
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

## 일급 컬렉션이 뭔데?
- 컬렉션과 관련된 로직을 캡슐화하는 디자인 패턴
- **Collection**을 **Wrapping** 하면서 그 외의 다른 멤버 변수가 없는 상태

그렇기 때문에 일급 컬렉션은 다음과 같은 요소들로 구성 된다.
- 컬렉션 자료형의 인스턴스 변수
- 컬렉션 자료형의 인스턴스 변수에 잘못된 값이 할당되지 않게 막고, 정상적으로 조작하는 메서드

예를 들면 다음과 같다.
``` java
List<User> member = new ArrayList<>();

member.add(new User("길동","주소1"));
member.add(new User("예슬","주소2"));
member.add(new User("지수","주소3"));
```
해당 코드를 wrapping 하여 일급 컬렉션을 만들어 보자.
``` java
private List<User> member;

public Member(List<User> user){
    this.member = user;
}
```

## 일급 컬렉션을 사용함으로써 얻는 이점
1. 비지니스에 종속적인 자료구조
   - 만약 사용하는 프로젝트 여러군데서 User라는 객체를 생성한다고 가정했을 때, User 객체를 생성하는 모든 로직에서 해당 리스트에 대한 검증 코드가 들어가야한다.
   하지만 일급 컬렉션 내 생성자에 검증 로직을 추가 해주면 객체를 쉽게 관리할 수 있다.
2. Collection의 불변성을 보장
   - 컬렉션의 값을 변경할 수 있는 메소드가 없는 컬렉션을 만들면 된다.
   컬렉션 클래스 내 외부에서 값을 변경할 수 있는 메서드들을 제외하고 구현을 하면 된다.
3. 상태와 행위를 한 곳에서 관리
   - 일급 컬렉션에는 값과 로직이 함께 존재한다. 
4. 이름 있는 컬렉션의 사용
   - 사용하는 컬렉션에 이름을 붙일 수 있다.
   다른 개발자들과 협업할 때 서로 다른 클래스에서 다른 변수명으로 선언하게 된다면 그냥 변수를 쓰는 것과 다름 없기 때문이다.

## 주의! 외부로 전달시 컬렉션의 변경 막기
인스턴스 변수를 그대로 외부에 전달하면, Member 클래스 외부에서 마음대로 멤버들 추가하고 제거해 버릴 수 있다. 그렇게 되면 일급 컬렉션을 사용하는 의미가 사라진다.`
``` java
class System {
	...
    List<Member> members() {
    	return members;
    }
}
```

``` java
members = system.member();
```
이렇게 사용한다면 ```add()```, ```clear()``` 과 같은 메서드를 사용하여 조작도 가능하다.
이러한 부작용을 방지하기 위하여 외부로 전달할 때는 컬렉션의 요소를 변경하지 못하게 ```unmodifiableList``` 메서드를 사용하면 된다.
``` java
class System {
	...
    List<Member> members(){
    	return members.unmodifiableList();
    }
}
```

---
## 참고 레퍼런스
- [일급 컬렉션 (First Class Collection)의 소개와 써야할 이유 - 티스토리, 향로님](https://jojoldu.tistory.com/412)
- [내 코드가 그렇게 이상한가요? - 센바 다이야](https://product.kyobobook.co.kr/detail/S000202521361)