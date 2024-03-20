---
title: "[Java] JVM의 Garbage Collector"
description: "Garbage Collector에 대해 알아보자"
slug: garbage-collector
date: 2024-03-20T15:53:55+09:00
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
---


## JVM(Java Virtual Machine)이란?

- 운영체제의 메모리 영역에 접근하여 메모리를 관리하는 프로그램
- 메모리 관리, Garbage Collector 등을 수행하게 된다.

## Garbage Collector란?

- 동적으로 할당한 메모리 영역 중 사용하지 않는 영역을 탐지하여 해제하는 기능을 맡고 있다.
  - C, C++의 경우 사용자가 직접 메모리를 할당 받고, 해제해야 한다.(```malloc(), free()```와 같은 함수를 사용)
  - 하지만 Java에서는 GC의 기능이 Heap 메모리에서 unreachable한 객체를 자동으로 삭제 해 준다.

---

### (참고) Heap과 Stack은 또 뭐지?

- Stack
**정적**으로 할당한 메모리 영역
원시 타입의 데이터가 **값**과 함께 할당, Heap 영역에 생성된 Object 타입의 데이터의 **참조** 값을 할당한다.

- Heap
**동적**으로 할당한 메모리 영역
모든 **Object 타입**의 **데이터**가 할당된다.

코드와 이미지로 이해를 해보자면 다음과 같다.

![](https://velog.velcdn.com/images/yeseul/post/3cec2b0a-f734-40ee-9c67-dbe2ededbfb1/image.png)


---

다시 돌아와서...

## Garbage Collector가 수행되는 과정

1. Garbage collector가 stack의 모든 변수를 스캔하면서 각각 어떤 객체를 참조하고 있는지 **<span style="color: #6495ED">찾아서 마킹</span>**한다.
2. Reachable Object가 참조하고 있는 객체도 **<span style="color: #6495ED">찾아서 마킹</span>**한다.
3. 마킹되지 않는 객체를 heap에서 **<span style="color: #6495ED">제거</span>**한다.

1,2번 과정을 ```Mark```, 3번 과정을 ```Sweep```이라고 부르며 이 과정 전체를 ```Mark and Sweep```이라고 한다.

## Garbage Collection이 일어나는 과정

이는 Heap의 구조를 알아야 이해를 할 수 있다.
Heap의 구조는 다음과 같다.
![](https://velog.velcdn.com/images/yeseul/post/73f32293-3fee-4357-848c-ee6e1c0aca23/image.png)

New Generation 영역과 Old Generation으로 나누어져있고,
New Generation이 Eden/Survival0/Survival1로 나누어 진다.

1. 새로운 객체는 ```Eden```에 할당 된다.
2. ```Eden``` 영역이 전부 사용되면 이때 GC가 발생되는데, 이를 **<span style="color: #6495ED">```Minor GC```</span>**라고 한다.
3. Mark and Sweep이 진행되며 reachable이라 판단된 객체는 ```Survival 0``` 영역으로 옮겨진다.
   - 이때, 옮겨지면서 Age-bit가 1 증가한다.
4. ```Eden```이 또 꽉차게 되면 Minor GC가 다시 실행된다.
   - reachable 객체들이 ```Survival 1```로 이동하며 Age-bit가 1 증가한다.
   - 이때, ```Survival 0```에 있던 객체들도 Survival 1로 이동하며 Age-bit가 1 증가한다.
   - 그러므로 항상 Survial 0, 1중 한 곳은 비어 있다.
5. Age-bit가 특정 숫자만큼 높아지면, 오래 쓰이는 객체라고 인식하여 ```Old Generation```으로 이동하게 되는데, 이를 **<span style="color: #6495ED">```Promotion```</span>**이라고 한다.
6. 해당 순서가 반복되며 Old Generation이 전부 차버려 Old Generation 안에서 GC가 일어난다면 이를 **<span style="color: #6495ED">```Major GC```</span>**라고 부른다.
   - Mark and Sweep를 이용하는 것은 동일하다.

---

### 그림으로 이해하기

해당 ```Minor GC``` 순서를 그림으로 이해해보자.
![](https://velog.velcdn.com/images/yeseul/post/f39940f1-9880-4956-80b2-75c985c10b96/image.png)

Eden이 가득차면 Survival 0과 Survival 1로 이동하며 Age값을 높인다.
Age값이 특정 숫자만큼 높아지면, Old Generation으로 이동한다.(Promotion)

그리고 Old Generation이 가득차면 ```Major GC```가 일어나는 것이다.


---

## Garbage Collector의 장단점

- 장점
  - Memory Leak이 발생되지 않음
  - 휴먼 에러 발생 가능성을 낮춤
    - 예시: 해제된 메모리에 접근을 시도하거나 메모리 이중 해제를 시도하는 등이 있다.


- 단점
  - 성능 저하
    - 어떤 메모리를 해제해야 할지 검사하고 삭제하는 과정이 필요하기 때문
    - 대규모일 경우 비용은 더 증가
  - 개발자는 언제 메모리가 해제되는지 모름
    - jvm은 GC를 실행시키기 위해 **잠시 실행을 멈추는데**, 실시간성이 매우 강조된다면 이러한 특징이 적합하지 않을 수 있음
    
---

## JVM은 어떻게 GC와 Application을 같이 실행할까?

- 완전히 같이 실행할 수는 없다.
- 때문에 JVM은 GC를 실행하기 위해 Application 실행을 멈춘다.
  - 이 과정을 ```Stop the world```라고 표현한다.
- ```Stop the world``` 과정이 짧을 수록 최적화 된 것이다.

여러 방법이 있지만 자주 쓰이는 것만 소개를 한다면 다음과 같다.


1. parallel GC
   - JAVA 8에서 기본적으로 쓰이는 GC 방식
   - 멀티 코어 환경에서 사용
   - 여러개의 thread로 GC를 실행하기 때문에 stop the world 시간이 짧음


2. G1 GC
   - JAVA 9부터 기본적으로 쓰이는 GC 방식
   - heap을 일정 크기의 region으로 잘게 나눠 young generation, old generation으로 나눠 활용
   - 이때 region은 그때그때 개수를 알아서 튜닝해줌
   - 이에 따라 stop the world 최소화가 가능
  
---

### 레퍼런스

- [우아한테크 - [10분 테코톡] 주디의 Garbage Collector](https://youtu.be/M49_H5FjJ3U?si=hBekl2_5zEzjJazy)
- [우아한테크 - [10분 테코톡] 던의 JVM의 Garbage Collector](https://youtu.be/vZRmCbl871I?si=7uqEXOqzIl2_Y4mP)