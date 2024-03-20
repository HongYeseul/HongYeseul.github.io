---
title: "Spring 웹 계층 간단 정리"
description: "스프링 부트와 AWS로 혼자 구현하는 웹 서비스 - p.101"
slug: spring-web
date: 2024-03-20T15:55:52+09:00
image: 
math: 
license: 
hidden: false
comments: true
draft: true
categories:
    - 알아두면 좋은 개발 지식
tags:
    - Java
    - Spring
---


![](https://velog.velcdn.com/images/yeseul/post/f6c04a77-1cbe-43fa-a89c-da8fcd95fba7/image.png)

- Web Layer

 컨트롤러와 JSP/Freemaker 등의 뷰 템플릿 영역
 이외에도 필터, 인터셉터, 컨트롤러 어드바이스 등 외부 요청과 응답에 대한 전반적인 영역
 
 
- Service Layer

 @Service, @Transactional
 일반적으로 Controller와 Dao의 중간 영역에서 사용


- Repository Layer

 Database와 같이 데이터 저장소에 접근하는 영역
 Dao(Data Access Object) 영역으로 이해하면 쉬움


- Dtos
 
 Dto(Data Transfer Object)는 계층 간에 데이터 교환을 위한 객체를 얘기하며 Dtos는 이들의 영역을 얘기한다.
 뷰 템플릿 엔진에서 사용될 객체나 Repository Layer에서 결과로 넘겨준 객체 등


- Domain Model
 도메인이라 불리는 개발 대상을 모든 사람이 동일한 관점에서 이해할 수 있고 공유할 수 있도록 단순화 시킨 것
 @Entity, VO
 비즈니스 처리 담당


