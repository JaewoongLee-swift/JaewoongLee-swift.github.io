---
title: "[iOS] RxSwift - Observable 알아보기"
excerpt:
categories:
    - iOS
tags:
    - swift
    - iOS
    - RxSwift
    - Observable
toc: true
last_modified_at: 2022-01-06-22:53:00 +0900
---
## 1. Observable 이란

지난 포스팅에서, RxSwift를 알아보았다.<br/>
<br/>
이번 포스팅에선 RxSwift의 한 구성요소인 Observable에 대해 알아보도록 하자.<br/>
<br/>

- Observable이란?
> 제너럴타입의 객체로, `T`형태의 데이터 snapshot을 전달 할 수 있는 일련의 이벤트를 비동기적으로 생성하는 기능을 가짐.

<br/>
지난 포스팅 내용과 같이, `Observable`은 제너럴 타입의 객체이며 실시간으로 세 가지 유형의 이벤트를 방출한다.<br/>
<br/>

`Observable`은 일정 기간 동안 계속해서 이벤트를 생성하며 이러한 행동을 emit이라고 한다.<br/>
<br/>
세 가지 이벤트는 next, error, completed 세가지로 구성된다.<br/>
<br/>
중요한 점은 이 모든 이벤트들은 비동기적으로 발생한다는 것이다.

## 2. Observable의 생명주기
생명주기를 보여줌에 앞서, Rx의 흐름에 따라 값을 표기하는 방식인 'marble diagram' 으로 나타내도록 하겠다.

<img width="328" src="https://user-images.githubusercontent.com/83946704/148389744-9a058c20-0e2e-49e3-936a-7bd1d3405137.png"><br/>

위의 marble diagram은 세 개의 Observable을 나타내었다.<br/>
<br/>
`Observable`은 각각의 next 이벤트를 통해서 요소(element)들을 방출하게 되는데, 첫번째와 두번째 `Observable`은 3개의 이벤트를 방출한 뒤 종료하게 된다.<br/>
<br/>
두번째 `Observable`의 diagram에서 맨 끝의 수직선은 completed 이벤트를 표현한 것이다.<br/>
<br/>
세번째 `Observable`은 다른것들과 다르게 error가 발생하였다(X로 표현됨).<br/>
<br/>
이벤트가 종료된 것은 앞서 두 `Observable`과 동일하지만 error를 통해 종료되었다는 차이점이 있다.


## 3. next, error, completed
`Observable`은 next, error, completed 이 세가지 이벤트만 가지는데 다음과 같은 특징을 가진다.

1. Observable은 어떤 구성요소를 가지는 next 이벤트를 계속해서 방출할 수 있다.
2. Observable은 error 이벤트를 방출하여 완전히 종료될 수 있다.
3. Observable은 completed 이벤트를 방출하여 완전히 종료될 수 있다.

이러한 이벤트들의 특징을 확인하기 위해 RxSwift의 문서를 좀 더 살펴보자.

<br/>
<img width="600" src="https://user-images.githubusercontent.com/83946704/148392677-9c394cf6-0b1a-49e4-b48f-42e6023859e7.png"><br/>

next 이벤트는 어떠한 element 인스턴스를 가지고 있고, error 이벤트는 Swift.Error 인스턴스를 가지고 있다.<br/>
<br/>
completed는 인스턴스 없이 이벤트를 종료한다.


## 4. 마무리
이번 포스팅에선 RxSwift의 심장이라 불릴 수 있는 `Observable`을 알아보았다.<br/>
<br/>
다음 포스팅에선 `Observable`에서 방출한 값들을 순서와 기능에 맞게 조합하여 사용할 수 있게 만들어주는 Operator에 대해 알아보도록 하자.



<br/><br/><br/><br/>
출처 : 30개 프로젝트로 배우는 iOS 앱 개발 with Swift 초격차 패키지 Online - part 4. Github 앱 만들기
