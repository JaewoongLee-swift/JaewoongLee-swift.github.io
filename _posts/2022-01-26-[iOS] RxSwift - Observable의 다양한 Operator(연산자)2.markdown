---
title: "[iOS] RxSwift - Observable의 다양한 Operator(연산자) 2"
excerpt:
categories:
    - iOS
tags:
    - swift
    - iOS
    - RxSwift
    - Observable
    - Operator
toc: true
last_modified_at: 2022-01-26-16:27:00 +0900
---
## 0. Dispose, DisposeBag
이번 포스팅에선 메모리 누수방지를 위해 무조건 필요한 `dispose`를 먼저 알아보고, 이후에 남은 생성 operator들을 알아보도록 하겠다.<br/>
### 1. Dispose
지난 포스팅에서 Observable은 구독(subscribe)하지 않으면 어떠한 이벤트도 내지않는 sequence일 뿐이라고 말했다.<br/>
<br/>
만약 무한한 element를 방출하는 Observable일 경우 구독을 한 뒤 계속해서 이벤트를 방출할 텐데, 어떻게 구독을 멈출 수 있을까?<br/>
<br/>
바로 `dispose()`를 이용해 멈출 수 있다.

{% highlight swift %}
Observable.of(1, 2, 3)      //Observable 생성
    .subscribe(onNext: {    //subscribe를 통해 이벤트 생성
        print($0)
    })
    .dispose()              //dispose 후에는 이벤트 방출 안됨

#=> prints 1
#=> prints 2
#=> prints 3
{% endhighlight %}

다음과 같이 코드상으로 표현되진 않지만 무한한 element가 있을 경우 반드시 `dispose()`를 해야 이벤트가 종료된다.<br/>
<br/>
메모리 누수 방지를 위해선 항상 `dispose`해주어야 한다. (일종의 생명주기라 할 수 있음)

### 2. DisposeBag
구독해제를 `dispose()`로 한다고 알았는데... `DisposeBag`은 무엇일까?<br/>
<br/>
일단 코드를 통해 보도록 하자.
{% highlight swift %}
let disposeBag = DisposeBag()

Observable.of(1, 2, 3)
    .subscribe{
        print($0)
    }
    .disposed(by: disposeBag)

#=> prints 1
#=> prints 2
#=> prints 3    
{% endhighlight %}
`disposed(by:)`는 `dispose()`와 동일하게 구독해제를 위해 작성하였다.<br/>
<br/>
`disposeBag`은 `disposed(by:)`를 통해 `disposable`타입으로 Observable을 받고, `disposeBag`이 할당해제를 할 때마다 담겨져 있는 모든 `disposable`들에게 `dispose()`를 호출한다.<br/>
<br/>
따라서 위 코드의 Observable은 발생하는 이벤트들을 print하고 구독 후 방출된 return 값들을 `disposeBag`에 추가하는 것이다.<br/>
<br/>
`dispose`와 `disposeBag`은 구독할 때 마다 할당하는 것 같아 불편해 보이지만, 끝나지 않는 Observable일 경우 메모리 누수가 일어나기 때문에 매번 Observable을 생성하면 `disposeBag`을 만드는게 무조건 필요하다.<br/>
<br/>
이후 하단의 예시에는 모두 `disposeBag`을 선언했다는 가정 하에 작성하도록 하겠다.

## 1. create
`create`은 observer를 전달받아 `Disposable`을 반환하는 escaping 클로저이다.
{% highlight swift %}
// create 1
Observable.create { observer -> Disposable in
    observer.onNext(1)
    observer.onCompleted()
    observer.onNext(2)
    return Disposables.create()
}
.subscribe {
    print($0)
}

#=> prints 1
{% endhighlight %}

다음과 같이 Observable을 생성하면 `1`만 출력된다. 이유는 무엇일까?<br/>
<br/>
바로 observer가 `1`을 next이벤트로 전달한 다음, completed이벤트를 통해 종료되어 그 다음 전달받은 element는 구독되지 않기 때문이다.<br/>
<br/>
그렇다면 completed이벤트와 유사하게 종료시키는 error이벤트는 어떻게 반응할까?

{% highlight swift %}
// create 2
enum MyError: Error {
    case anError
}

Observable<Int>.create { observer -> Disposable in
    observer.onNext(1)
    observer.onError(MyError.anError)
    observer.onCompleted()
    observer.onNext(2)
    return Disposables.create()
}
.subscribe(
    onNext: {
        print($0)
    },
    onError: {
        print("에러발생 : \($0.localizedDescription)")
    },
    onCompleted: {
        print("completed!")
    },
    onDisposed: {
        print("disposed!")
    }
)
.disposed(by: disposeBag)

#=> prints 1
#=> prints 에러발생 : The operation couldn’t be completed. (__lldb_expr_25.MyError error 0.)
#=> prints disposed!
{% endhighlight %}

임의의 에러 `anError`를 생성한 후 `create`로 Observable을 생성하였다.<br/>
<br/>
처음 nex이벤트로 전달된 `1`은 무사히 출력되었지만 이후 에러가 발생하고 completed이벤트까지 가지않고, 2 또한 출력되지 않았다.<br/>
하지만 Observable이 종료되고 `disposed`가 실행된 것은 확인할 수 있다.<br/>
<br/>
자 그럼 error, completed, disposed 모두 생성하지 않고 Observable을 만들면 어떻게 될까?

{% highlight swift %}
Observable.create { observer -> Disposable in
    observer.onNext(1)
    observer.onNext(2)
    return Disposables.create()
}
.subscribe(
    onNext: {
        print($0)
    },
    onError: {
        print("에러발생 : \($0.localizedDescription)")
    },
    onCompleted: {
        print("completed!")
    },
    onDisposed: {
        print("disposed!")
    }
)

#=> prints 1
#=> prints 2
{% endhighlight %}
`disposed`, error, completed 이벤트 모두 없으므로 결과적으로 Observable은 종료되지 않고 메모리 누수가 발생할 것이다.<br/>
<br/>
따라서 반드시 Observable을 생성할 땐 `disposed`를 작성해주도록 하자.

## 2. deferred
deferred는 Observable을 만드는 대신에, 구독이 되기 전까지 Observable 생성을 지연하고 구독이 시작되면 새롭게 Observable을 제공하는 Observable Factory 형식이다.<br/>
<br/>
굉장히 이해하기 어려운 내용인데, 쉽게 풀어보면
1. 구독하기 전엔 '대기상태의 Observable'
2. 구독을 시작하면 '진짜로 생성된 Observable'<br/>
<br/>
예시를 통해 확인하자.

{% highlight swift %}
var 뒤집기: Bool = false

let factory: Observable<String> = Observable.deferred {
    뒤집기 = !뒤집기

    if 뒤집기 {
        return Observable.of("👍")
    } else {
        return Observable.of("👎")
    }
}

for _ in 0...3 {
    factory.subscribe(onNext: {
        print($0)
    })
        .disposed(by: disposeBag)
}

#=> prints 👍
#=> prints 👎
#=> prints 👍
#=> prints 👎
{% endhighlight %}
`factory`는 처음 선언될 때 `deferred`된 Observable로 선언되었기 때문에 대기상태이다.<br/>
<br/>
그리고 Bool 타입인 `뒤집기` 변수는 처음엔 `false`이다.<br/>
<br/>
이후 구독됨에 따라 `factory`는 대기상태가 풀리면서 `deferred`내의 `뒤집기 = !뒤집기`가 실행되었기 때문에 `뒤집기 = true`가 되고, `👍`이 출력된다.<br/>
<br/>
그 다음 반복에서도 위와 같은 과정으로 `뒤집기`의 Bool값이 변하면서 출력된다.<br/>
<br/>
다시한번 정리하자면, Observable deferred는 Observable factory를 통해서 sequence를 생성할 수 있는 연산자이다.

## 3. 마무리
지금까지 두개의 포스팅을 통해 Observable의 기본이 되는 생성 Operator들과 dispose에 대해 알아보았다.<br/>
<br/>
다음은 Observable보다 조금 작은 개념인 Single, Maybe, Completable에 대해 알아보도록 하자.

<br/><br/><br/><br/>
출처 : 30개 프로젝트로 배우는 iOS 앱 개발 with Swift 초격차 패키지 Online - part 4. Github 앱 만들기
