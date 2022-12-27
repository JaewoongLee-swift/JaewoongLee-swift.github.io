---
title: "[Swift] GCD - 3 (DispatchQueue 사용하기)"
excerpt:
categories:
    - Swift
tags:
    - GCD
    - DispatchQueue

toc: true
last_modified_at: 2022-12-27-17:19:00 +0900
---
## 0. 서론
지난 [GCD - 2] 글에서 DispatchQueue에 대해 알아보았다.<br/><br/>
그렇다면 이번엔 DispatchQueue에서 제공하는 main, global queue를 어떻게 사용할 수 있을지 알아보도록 하자!<br/><br/><br/>
(sync/async, serial/concurrent가 구분이 안된다면 [소들이님의 포스트] 참고!)

## 1. Main Queue (Serial Queue)
- Main Queue : Serial Queue로 Queue에 들어있는 하나의 task가 끝나야지 다음 task를 할 수 있다.

{% highlight swift %}
print("시작")

DispatchQueue.main.async {
    for _ in 0...5 {
        print("main async 1")
    }
}

DispatchQueue.main.async {
    for _ in 0...5 {
        print("main async 2")
    }
}

print("끝")

#=> prints 시작
#=> prints 끝
#=> prints main async 1
#=> prints main async 1
#=> prints main async 1
#=> prints main async 1
#=> prints main async 1
#=> prints main async 2
#=> prints main async 2
#=> prints main async 2
#=> prints main async 2
#=> prints main async 2
{% endhighlight %}

위와 같이 `DispatchQueue.main.async`와 같이 비동기로 선언했기 때문에 '시작', '끝'이라는 print문이 먼저 실행되었지만 main queue에선 첫번째 async 클로저의 코드가 모두 실행되고 두번째 async 클로저의 코드가 실행되었다.<br/><br/>

그리고 Main Queue는 UI와 관련된 task를 넣어야 하고 반드시 sync로 작업을 하면 안된다고 하는데<br/>
<img width="752" alt="스크린샷 2022-12-27 오후 3 53 51" src="https://user-images.githubusercontent.com/83946704/209624734-99376615-9425-4e49-986d-0f4f4a1888b8.png"><br/>
(다음과 같은 스레드 관련 오류가 발생한다.)<br/><br/>
자세한 내용은 [제드님의 포스트]에 잘 정리되어 있으므로 참고하면 좋을듯 하다.

## 2. Global Queue (concurrent Queue)
- Global Queue : Queue에 추가된 순서대 한번에 하나의 task를 실행한다.

{% highlight swift %}
DispatchQueue.global().sync {
    print("시작!")
}

DispatchQueue.global().async {
    print("1 시작했습니다")

    for _ in 0...5 {
        print("global async 1")
    }

    print("1 끝났습니다")
}

DispatchQueue.global().async {
    print("2 시작했습니다")

    for _ in 0...5 {
        print("global async 2")
    }

    print("2 끝났습니다")
}

DispatchQueue.global().sync {
    print("끝나감")

    for _ in 0...5 {
        print("global sync 1")
    }

    print("끝!")
}

#=> prints 시작!
#=> prints 끝나감
#=> prints 2 시작했습니다
#=> prints 1 시작했습니다
#=> prints global sync 1
#=> prints global async 1
#=> prints global async 1
#=> prints global async 1
#=> prints global async 1
#=> prints global async 1
#=> prints global async 1
#=> prints global sync 1
#=> prints global async 2
#=> prints global async 2
#=> prints global async 2
#=> prints 1 끝났습니다
#=> prints global async 2
#=> prints global async 2
#=> prints global async 2
#=> prints global sync 1
#=> prints global sync 1
#=> prints global sync 1
#=> prints global sync 1
#=> prints 2 끝났습니다
#=> prints 끝!

{% endhighlight %}

다음과 같이 Concurrent Queue인 Global Queue에서는 sync로 선언된 작업에서의 순서는 보장되지만 (매 실행때마다 같은 결과)) async로 선언된 작업에서의 순서는 보장되지 않고 동시에 실행된다! (매 실행마다 다른 결과)<br/><br/>

그렇다면 지난번에 배운 QoS 설정을 통해서 쓰레드의 우선순위를 부여해보자.

{% highlight swift %}
DispatchQueue.global(qos: .utility).async {
    print("1 시작했습니다")

    for _ in 0...5 {
        print("global async 1")
    }

    print("1 끝났습니다")
}

DispatchQueue.global(qos: .userInteractive).async {
    print("2 시작했습니다")

    for _ in 0...5 {
        print("global async 2")
    }

    print("2 끝났습니다")
}

#=> prints 2 시작했습니다
#=> prints 1 시작했습니다
#=> prints global async 2
#=> prints global async 2
#=> prints global async 1
#=> prints global async 2
#=> prints global async 2
#=> prints global async 2
#=> prints global async 2
#=> prints 2 끝났습니다
#=> prints global async 1
#=> prints global async 1
#=> prints global async 1
#=> prints global async 1
#=> prints global async 1
#=> prints 1 끝났습니다
{% endhighlight %}

이 또한 매 실행마다 같은 결과를 나타내지 않지만 대체로 userInteractive로 설정한 클로저의 코드가 먼저 실행되었다.<br/><br/>
대부분의 실행이 userInteractive의 QoS를 설정한 클로저가 먼저 실행되었지만 누가 먼저 끝나는지는 매 실행마다 달랐고 가끔 utility QoS의 클로저가 먼저 실행되는 경우도 있었다.

## 3. Custom Queue
DispatchQueue를 초기화시켜 나만의 Queue를 생성할 수도 있다.

{% highlight swift %}
let serialQueue = DispatchQueue(label: "serial")
let concurrentQueue = DispatchQueue(label: "concurrent", attributes: .concurrent)

serialQueue.sync {
  // codes...
}

concurrentQueue.async {
  // codes...
}
{% endhighlight %}

다음과 같이 `label` 파라미터에서는 해당 queue의 이름을 정해주고, `attributes` 파라미터를 통해서 해당 queue가 serial인지, concurrent한지 설정해준다. (Default는 serial이다.)<br/><br/>

## 4. 마무리
DispatchQueue의 다양한 예시를 통해 어떻게 코드가 실행되는지 알아보았다.<br/><br/>
이렇게 serial, concurrent queue와 sync/async에 대해 코드와 의미를 알아보니 내 코드를 어떻게 수정하면 될지 무언가 그려지는것 같기도...?<br/><br/>
당장 리팩토링을 하러 가보자. 이후에 내가 지금 드는 생각과 실제 리팩토링 방향이 맞는지 확인해보고 다시 포스팅 하겠음!<br/><br/>

<br/><br/><br/><br/>
참고 : Apple Documentation, DispatchQueue - main
(<https://developer.apple.com/documentation/dispatch/dispatchqueue/1781006-main>)<br/>
참고 : zeed0202, iOS ) GCD - Dispatch Queue사용법 (1)
(<https://zeddios.tistory.com/516>)<br/>

[GCD - 2]: https://jaewoonglee-swift.github.io/swift/Swift-GCD-2-(+-DispatchQueue)/
[소들이님의 포스트]: https://babbab2.tistory.com/64
[제드님의 포스트]: https://zeddios.tistory.com/519
