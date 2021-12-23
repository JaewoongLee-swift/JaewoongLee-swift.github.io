---
title: "[iOS] RxSwift - RxSwift란?"
excerpt:
categories:
    - iOS
tags:
    - swift
    - iOS
    - RxSwift
toc: true
last_modified_at: 2021-12-23-22:50:00 +0900
---
## 1. Reactive X

RxSwift를 배우기 위해 Rx, ReactiveX가 무엇인지 알고 지나가자.<br/>
<br/>
- Reactive X란?
> 프로그래밍에서 데이터가 동기식이든, 비동기식이든 상관없이 명령형 프로그래밍 언어가 데이터 요소들에서 작동할 수 있도록 하는 도구.<br/>
(위키백과 - ReactiveX)

<br/>
흠... 어렵다. 이해를 쉽게 하기위해 다른 블로그에서의 정리글을 옮겨보자면,
> ReactiveX는 비동기에 대한 스트림(결과)를 관찰하고 그것에 반응해서 무언가를 할 수 있는 프로그래밍 패러다임

(출처 : <https://magi82.github.io/ios-rxswift-01/>)

좀 더 이해를 쉽게 해보자면....<br/>
<br/>
비동기의 예로 서버통신에 ReactiveX를 사용한다면, 통신결과가 언제 나올지 관찰하고 통신결과에 반응해서 어떠한 행동을 할 수 있게 만들어주는 것이라 생각하면 되겠다.

## 2. RxSwift
ReactiveX를 알았으니, RxSwift는 Swift언어에 ReactiveX를 입혔다는 것을 알 수 있다.<br/>
<br/>
조금 더 정확이 알아보자면,<br/>
RxSwift는 기본적으로 비동기적으로 움직이는 애플의 api들과 수시로 상태가 변하는 환경에서 보다 직관적이고 효율적인 코드를 작성할 수 있게 도와준다.<br/>
<br/>
RxSwift를 사용하기 위해선 구성 요소를 알아야하는데 아래엔 다음 3가지를 설명하겠다.
- Observable
- Operator
- Scheduler

### 2-1. Observable
`Observable<T>`란?<br/>

1. 제너럴타입의 객체로, `T`형태의 데이터 snapshot을 전달 할 수 있는 일련의 이벤트를 비동기적으로 생성하는 기능을 가진다.
2. 하나 이상의 `observers`가 실시간으로 어떤 이벤트에 반응한다.
3. 세 가지 유형의 이벤트만 방출한다.

{% highlight swift %}
enum Event<Element> {
  case next(Element)      // Observable의 어떠한 다음 요소
  case error(Swift.error) // 에러 이벤트
  case completed          // 최종완료 이벤트
}
{% endhighlight %}
<br/>
`observer`와 `Observable`만 있을 뿐, 다른 `delegate protocal`을 이용하거나 `class`간 통신을 위해 클로저를 사용할 필요가 없다.<br/>
<br/>
어느곳에서든 `Observable`을 선언하고 `Observable`을 수신할 수 있는 `observer`만 있다면 이벤트를 서로 수신할 수 있다.<br/>

### 2-2. Operator
`Operator`란?<br/>
수식과 같이 일정한 기능을 수행하면서 `Observable`에서 방출한 값을 기능에 맞춰 순서를 맞추고 조합해서 사용하는 것.<br/>
<br/>
{% highlight swift %}
//예시: 가로, 세로 화면전환의 경우
UIDevice.rx.orientation
  .filter { value in
      return value != .landscape
  }
  .map { _ in
      return "세로로만 보겠습니다!"
  }
  .subscribe(onNext: { string in
      showAlert(text: String)
  })
{% endhighlight %}
<br/>
코드를 통해 예시를 보자면,<br/>
`UIDeive.rx.orientation`이 `Observable<Orientation>`타입을 가지는 하나의 `Observable`이다.<br/>
<br/>
하나의 `Observable`을 `filter`, `map`, `subscribe`와 같은 연산자들이 각각의 결과값을 조합하고 계산한 다음 결과를 내뱉는 것.<br/>

### 2-3 Scheduler
`Scheduler`란?<br/>
RxSwift에서 dispatchQueue와 동일한 것으로 더욱 강력하고 사용하기 쉽다.<br/>
<br/>
여태 GCD를 이용해 `dispatchQueue`를 이용했다면, `Scheduler`는 `Main Scheduler`, `Background Scheduler`로 나뉘어서 사용됨.


## 3. 마무리
다음과 같이 RxSwift의 개념과 RxSwift를 이루는 요소들에 대해 알아보았다.<br/>
<br/>
RxSwift는 거의 필수적으로 알아야 하는 만큼 이번에 간단하게 알아본것을 넘어서 더욱 자세하게 구성요소들에 대해 알아보도록 포스팅을 이어가겠다.



<br/><br/><br/><br/>
출처 : 30개 프로젝트로 배우는 iOS 앱 개발 with Swift 초격차 패키지 Online - part 4. Github 앱 만들기
