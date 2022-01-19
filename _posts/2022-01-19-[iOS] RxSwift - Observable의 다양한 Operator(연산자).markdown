---
title: "[iOS] RxSwift - Observable의 다양한 Operator(연산자) 1"
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
last_modified_at: 2022-01-19-16:04:00 +0900
---

지난 포스팅을 통해 `Observable`을 알아보았으니 `Observable`의 이벤트를 처리할 수 있는 다양한 연산자 중, 생성 Operator(연산자)를 알아보도록 하자.<br/>
<br/>

## 0. Subscribe
일단 operator들을 알아보기 전 subscribe(구독)에 대해 알 필요가 있다.<br/>
<br/>
Rx를 사용할 때, Observable이 어떠한 이벤트를 내보낸다는 것은 어떻게 알까?<br/>
<br/>
Observable는 실제로는 sequence 정의일 뿐이며 subscribe 되기 전에는 어떠한 이벤트도 내보내지 않는다.<br/>
<br/>
따라서 실제로 잘 작동하는지 확인하기 위해선 반드시 subscribe 해야하는 것을 명심하도록 하자.<br/>
<br/>


## 1. Just
`just`는 단 하나의 element만 방출시키는 연산자이다.<br/>

{% highlight swift %}
Observable<Int>.just(1)
    .subscribe(onNext: {
            print($0)
    })

#=>	prints 1    
{% endhighlight %}

## 2. Of
`of`는 여러개의 element를 방출시키는 연산자이다.<br/>

{% highlight swift %}
Observable<Int>.of(1, 2, 3, 4, 5)
    .subscribe(onNext: {
            print($0)
    })

#=>	prints 1
#=>	prints 2
#=>	prints 3
#=>	prints 4
#=>	prints 5    
{% endhighlight %}
쉼표를 기준으로 element를 하나씩 방출한다.<br/>
<br/>
여러개의 element를 방출하지만 array타입의 element는 array형태 그대로 방출한다.

{% highlight swift %}
Observable<Int>.of([1, 2, 3, 4, 5])
    .subscribe(onNext: {
            print($0)
    })

#=>	prints [1, 2, 3, 4, 5]    
{% endhighlight %}


## 3. From
`from`은 바로 위에서 다룬 `of`연산자와 유사하게 여러개의 element를 방출하지만 array형태의 element만 받으며 해당 array의 요소들을 하나씩 방출한다.

{% highlight swift %}
Observable<Int>.from([1, 2, 3, 4, 5])
    .subscribe(onNext: {
            print($0)
    })

#=>	prints 1
#=>	prints 2
#=>	prints 3
#=>	prints 4
#=>	prints 5    
{% endhighlight %}

## 4. empty
`empty`는 아무런 element를 방출하지 않는다.

{% highlight swift %}
//empty 1
Observable.empty()
    .subscribe{
      print($0)
    }

#=>	no prints
{% endhighlight %}
`empty`는 element도 없고 이벤트도 없는 `Observable`을 만들때 사용된다.

{% highlight swift %}
//empty 2
Observable<Void>.empty()
    .subscribe{
      print($0)
    }

#=>	prints completed
{% endhighlight %}
하지만 `Void`타입으로 선언한 뒤에 구독을 하면 completed가 출력된다. 왜그럴까?<br/>
<br/>
첫번째 예문은 타입선언이 없었기 때문에 `empty`를 통해 타입추론이 불가능해 어떠한 이벤트도 발생하지 않는다.<br/>
<br/>
하지만 `Void`타입을 선언한 뒤 구독을 하면 기존에 타입선언이 없었을 때와 같이 어떠한 element도 없지만 completed 이벤트는 발생하기 때문에 다음과 같이 completed가 출력된다.<br/>
<br/>
다음 예문(empty 3)은 위(empty 2)와 동일하다고 볼 수 있다.

{% highlight swift %}
//empty 3
Observable<Void>.empty()
    .subscribe(onNext: {

    },
    onCompleted: {
      print("completed")
    })

#=>	prints completed
{% endhighlight %}
`Void`타입을 선언하고 `empty` operator를 사용했으므로 element는 없고 전달되는 이벤트는 없지만 `Observable`을 종료시키는 completed 이벤트는 마지막에 발생한다.<br/>
<br/>
그렇다면 이러한 빈 `Observable`은 어디에 사용할까?<br/>
<br/>
즉시 종료할 수 있는 `Observable`을 리턴하고 싶을 때, 또는 의도적으로 0개의 값을 가지는 `Observable`을 리턴하고 싶을 때 사용할 수 있다.

## 5. never
{% highlight swift %}
Observable<Void>.never()
    .subscribe(onNext: {
        print($0)
    }, onCompleted: {
        print("completed")
    })

#=>	no prints
{% endhighlight %}
`never`는 `empty`와 유사하지만 completed 조차 출력되지 않는다.<br/>
<br/>
`Void`타입까지 선언했지만 아무런 이벤트도 출력되지 않았다.<br/>
<br/>
그렇다면 정상적으로 작동했는지 어떻게 확인할까?<br/>
<br/>
바로 `.debug`를 이용해서 확인할 수 있다.

{% highlight swift %}
Observable<Void>.never()
    .debug("never")
    .subscribe(onNext: {
        print($0)
    }, onCompleted: {
        print("completed")
    })

#=>	prints 2022-01-19 15:23:49.348: never -> subscribed
{% endhighlight %}
`.debug`에 원하는 텍스트를 입력하고 실행하니 실행시각과 함께 구독되었다고 출력되었다.<br/>
<br/>
다음과 같이 `never`는 아무런 이벤트를 내보내지 않는 operator이다.

## 6.range
{% highlight swift %}
Observable.range(start: 1, count: 9)
    .subscribe(onNext: {
        print("2*\($0)=\(2*$0)")
    })

#=>	prints 2*1=2
#=>	prints 2*2=4
#=> ...
#=>	prints 2*8=16
#=>	prints 2*9=18
{% endhighlight %}
`range`는 `start`값부터 `count`값까지 1씩 늘려가면서 element를 내보낸다.

## 7. 마무리
`Observable`을 생성하는 다양한 생성 operator를 알아보았다.<br/>
<br/>
하지만 아직 더 남은 operator도 있고, 메모리누수를 방지하기 위해 중요한 내용도 남아있어 다음 포스팅에서 더 다루기로 하겠다.

<br/><br/><br/><br/>
출처 : 30개 프로젝트로 배우는 iOS 앱 개발 with Swift 초격차 패키지 Online - part 4. Github 앱 만들기
