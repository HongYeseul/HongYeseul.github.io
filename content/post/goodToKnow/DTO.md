---
title: "DTO를 사용하는 이유와 설계 방법"
description: "DTO를 사용하는 이유와 방법을 알아보자"
date: 2024-03-20T15:50:30+09:00
image: 
math: 
license: 
hidden: false
comments: true
draft: true
slug: DTO
categories:
    - 알아두면 좋은 개발 지식
tags:
    - Java
    - 설계
---


## DTO란?
- ```DTO(Data Transfer Object)```란 계층간 데이터 교환을 하기 위해 사용되는 객체로 Getter/Setter만 가진 클래스
- 주로 View와 Controller 사이에서 활용
- 데이터 전달만을 위한 객체이므로 java-record로 구현하면 좋음


## 도메인 대신 DTO를 사용하면 좋은 이유
DTO 대신 도메인 모델을 계층간 전달에 사용하면 도메인 모델을 캡슐화 하여 보호할 수 있다.
view마다 필요한 정보가 다른데, 도메인 모델의 경우 필요하지 않은 정보까지 갖고 있기 때문이다.

![](https://velog.velcdn.com/images/yeseul/post/c3935f79-fc15-4962-afdd-c06b0af20308/image.png)

- 해당 사진은 MVC 패턴[(출처-위키백과)](https://ko.wikipedia.org/wiki/%EB%AA%A8%EB%8D%B8-%EB%B7%B0-%EC%BB%A8%ED%8A%B8%EB%A1%A4%EB%9F%AC)을 간략하게 나타낸 사진이다.

MVC 패턴에서 Controller는 Model과 View 사이에서 데이터를 주고 받는다.
이 과정에서 Model을 바로 Controller에게 넘겨준다면 View가 Model의 메서드를 호출하거나 상태를 변경시킬 위험이 존재한다. 또한 Model과 View가 강하게 결합되어 View의 요구사항 변화가 Model에 영향을 끼치기 쉽다.

- Model의 속성이 변경되면, View가 전달받을 프론트엔드 코드에도 변경을 유발하기 때문에 상호간 강하게 결합되는 것이다.

- 엔티티와 DTO가 항상 동일한 상황이라면 DTO대신 엔티티를 사용해도 되지만 그럴 일은 거의 없다.
- 물론 요청 & 응답시마다 DTO를 생성하는 것은 엔티티만 사용했을 경우보다 더 많은 코드를 관리해야 한다. 하지만 엔티티만 썼을 때 발생하는 코드 버그들과 유지보수의 난이도를 생각하면 훨씬 값싼 노동이다.

---

## 코드로 알아보자.

1. 아래와 같은 엔티티(Model)가 있을 때
``` java
public class User {
    private Long id;

    private String name;
	private String address;

}
```

2. DTO
``` java
static class ResponseDto {
        private String name;
        private String result = "결과는 = ";

        public ResponseDto(User user) {
            name = user.getName();
        }
}
```

3. Controller
``` java
    public ResponseDto useDTOController(String name, Long id) {
    	User findedUser = userService.findOne(id);
        return new ResponseDto(findedUser);
    }
```


### 결과

useDTOController는 엔티티의 결과를 DTO로 변환하여 원하는 결과인 name과 result만 클라이언트에게 반환한다. 하지만 DTO를 사용하지 않게되면 User 객체 전체를 반환하게되어 불필요한 데이터까지 전달하게 되는 것이다.

---

### 공부한 걸 토대로 이해하고 그려본 유스케이스 설계도

![](https://velog.velcdn.com/images/yeseul/post/172b0cca-3b22-492a-9bb1-46b8718941af/image.png)
!! 위 그림은 제가 이해한 걸 그린 거라 정확하지 않을 수 있어요. 양해 부탁드립니다 !!
혹시라도 틀린 점이 있다면 편하게 말씀 해주시면 감사하겠습니다. 🙇‍♀️

## 참고
- https://tecoble.techcourse.co.kr/post/2021-04-25-dto-layer-scope/
- https://tristy.tistory.com/54
- https://dkswnkk.tistory.com/500
- https://ccomccomhan.tistory.com/35