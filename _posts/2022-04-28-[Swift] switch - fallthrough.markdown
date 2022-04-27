---
title: "[Swift] Switch문 - fallthrough"
excerpt:
categories:
    - Swift
tags:
    - swift
    - switch
    - fallthrough
toc: true
last_modified_at: 2022-04-28-02:18:00 +0900
---
## 0. Swift에서의 switch문
fallthrough를 알아보기 전, Swift에서의 switch문과 C에서의 switch문의 차이를 알아보자.<br/>
<br/>
C에서의 switch문은 각 case 마다 끝에 break문을 삽입하지 않으면 모든 case를 실행하게 된다.<br/>
<br/>
하지만 Swift에서의 switch문은 각 case 조건에 일치하면 해당 case가 완료되자마자 실행을 종료한다.<br/>
<br/>
따라서, Swift의 switch문은 C에서와 같은 문제를 보완하여 더 간결하면서 예측 가능하고, 실수로 여러 case들을 실행하는 것을 피할 수 있는 장점을 가진다.<br/>
<br/>

## 1. fallthrough
위에서 알아봤듯, Swift의 switch문은 C에서의 switch문의 불편함을 해결하기 위해 다르게 만들어졌다.<br/>
<br/>
하지만 C에서와 비슷하게 동작하는 switch문을 필요로 할 수 있으므로, 대체하여 사용할 수 있도록 한 것이 `fallthrough`문 이다.<br/>

아래 예제를 통해 알아보자.

{% highlight swift %}

enum CoffeeMachineType {
    case iceAmericano
    case iceWater
    case water

    var name: String {
        switch self {
        case .iceAmericano:
            return "아이스 아메리카노🥤"
        case .iceWater:
            return "얼음물🧊"
        case .water:
            return "물💧"
        }
    }
}

func selectMenu(selectedMenu: CoffeeMachineType) {
    print("\(selectedMenu.name)을 선택하셨습니다. 제조 시작하겠습니다...")
    switch selectedMenu {
    case .iceAmericano:
        print("에스프레소 샷을 넣었습니다...")
    case .iceWater:
        print("얼음을 넣었습니다...")
    case .water:
        print("물을 넣었습니다...")
        print("완성되었습니다, 맛있게드세요 !! 😄")
    }
}

selectMenu(selectedMenu: .iceAmericano)

#=> prints 아이스 아메리카노🥤을 선택하셨습니다. 제조 시작하겠습니다...
#=> prints 에스프레소 샷을 넣었습니다...
{% endhighlight %}

`CoffeeMachineType` 타입의 enum을 선언하였다.<br/>
<br/>
그리고 `selectMenu`메소드를 통해 원하는 음료를 선택하면 선택한 음료와 제조과정이 나타난다.<br/>
<br/>
하지만 기존의 switch문을 그대로 사용했기 때문에 `.iceAmericano` case가 실행된 후 switch문이 완료됬음을 알 수 있다. <br/>
<br/>
나는 아아제조과정의 베이스가 되는 얼음을 넣는 과정과 물을 넣는 과정을 상위 case를 선택하였을 때도 넣고싶다.<br/>
<br/>
이러한 경우에 `fallthrough`문을 사용해보자.

{% highlight swift %}
func selectMenu(selectedMenu: CoffeeMachineType) {
    print("\(selectedMenu.name)을 선택하셨습니다. 제조 시작하겠습니다...")
    switch selectedMenu {
    case .iceAmericano:
        print("에스프레소 샷을 넣습니다...")
        fallthrough                   // 다음 case 또한 포함할것이므로 fallthrough문 사용
    case .iceWater:
        print("얼음을 넣습니다...")
        fallthrough                   // 다음 case 또한 포함할것이므로 fallthrough문 사용
    case .water:
        print("물을 넣습니다...")
        print("완성되었습니다, 맛있게드세요 !! 😄")
    }
}


selectMenu(selectedMenu: .iceAmericano)

#=> prints 아이스 아메리카노🥤을 선택하셨습니다. 제조 시작하겠습니다...
#=> prints 에스프레소 샷을 넣습니다...
#=> prints 얼음을 넣습니다...
#=> prints 물을 넣습니다...
#=> prints 완성되었습니다, 맛있게드세요 !! 😄
{% endhighlight %}

다음과 같이 `.iceAmericano` case와 `.iceWater` case에 `fallthrough`문을 삽입하였기 때문에 해당 case 아래의 조건 또한 실행되는 모습을 볼 수 있다.<br/>
<br/>


## 2. 마무리
Swift의 switch문에 있는 fallthrough문에 대하여 간략하게 알아보았다.<br/>
<br/>
오랜만에 정리한 내용을 올린 만큼 앞으로 더 자주올리길!


<br/><br/><br/><br/>
출처 : Swift 공식문서, Control Flow (Control Transfer Statements - Fallthrough)
(<https://docs.swift.org/swift-book/LanguageGuide/ControlFlow.html>)

참고 : <https://0urtrees.tistory.com/134>
