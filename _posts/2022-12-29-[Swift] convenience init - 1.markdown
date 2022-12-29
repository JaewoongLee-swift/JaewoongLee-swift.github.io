---
title: "[Swift] convenience init - 1"
excerpt:
categories:
    - Swift
tags:
    - convenience

toc: true
last_modified_at: 2022-12-29-23:46:00 +0900
---
## 0. 서론
지난 10월...사이드 프로젝트를 하면서 앱 브랜드 색상을 편리하게 사용하고 싶어 `UIColor`를 통해 편리하게 사용할 수 있도록 코딩을 하였다.<br/><br/>
{% highlight swift %}
extension UIColor {

// 앱 브랜드 컬러
static let bbagaRain = UIColor(rgb: 0x609AF0)

// alpha 1 고정, rgb값 입력 간소화 init
    convenience init(red: Int, green: Int, blue: Int, a: Int = 0xFF) {
        self.init(
            red: CGFloat(red) / 255.0,
            green: CGFloat(green) / 255.0,
            blue: CGFloat(blue) / 255.0,
            alpha: CGFloat(a) / 255.0
        )
    }

    convenience init(rgb: Int) {
        self.init(
            red: (rgb >> 16) & 0xFF,
            green: (rgb >> 8) & 0xFF,
            blue: rgb & 0xFF
        )
    }
}
{% endhighlight %}

당시에 피그마에서는 컬러를 16진수로 먼저 보여줬기 때문에 더 편리하게 사용하고자 다음과 같이 `convenience init`을 통해 16진수 값을 바로 `UIColor`를 초기화시킬때 사용할 수 있도록 했다.<br/><br/>
당시엔 출시일정이 있어 리서치 후 바로 사용하느라 `convenience init`이 정확히 어떤 키워드인지 모르고 사용했었는데 이제서야 제대로 공부하게 되었다...!!

## 1. designated init과 convenience init

### 1-1. designated init
`convenience init`을 알기 전에는 먼저 `designated init`을 알아야 할 필요가 있다.<br/><br/>
'역시 하나를 알면 열개를 더 알아야 하는 개발의 세계... 이건 또 뭐야?' 라고 생각할 텐데 (나도 그랬다 ㅠㅠ) 다행히 별거 아님!<br/><br/>
바로 평소에 우리가 struct나 class를 만들고 초기화할 때 사용하는 `init` 키워드가 바로 `designated init`이다!

{% highlight swift %}
class Person {
  var name: String
  var age: Int

  // 이것이 바로 designated init
  init(name: String, age: Int) {
    self.name = name
    self.age = age
  }
}

{% endhighlight %}

모든 클래스는 반드시 `designated init`이 있는것을 우리는 이미 알고있다. 그리고 기본적으로 최소 하나의 `init`을 가지게 되는것도!<br/><br/>
그렇다면 이것을 왜 알아야할까?

### 1-2. convenience init
`convenience init`은 부차적인 `init`이라고 생각하면 된다.

> 일반적인 초기화 패턴에 대한 단축키처럼 사용해 시간을 절약하거나 클래스 초기화를 더욱 명확하게 할 때 convenience initializer를 만들어라. (Swift 공식문서)

Swift 공식문서에서 `convenience init`을 일종의 편의성을 위한 이니셜라이저로 설명하고 있다.<br/><br/>
`convenience init`은 동일한 클래스에서 정의되어 있는 `designated init`(구현되어 있는 `init`)을 호출한 후 편의성이나 이니셜라이저의 간소화를 위해 사용하는 것이다!<br/><br/>
따라서 클래스에 필요하지 않는 경우엔 `convenience init`을 구현할 필요가 없다.<br/><br/>
따라서 내가 구현했던 코드를 다시 보자면,

{% highlight swift %}
extension UIColor {
    // 기존의 init을 호출해 alpha값을 따로 설정하지 않으면 default값을 전달하도록 정의해 편리함을 가져다줌!
    convenience init(red: Int, green: Int, blue: Int, a: Int = 0xFF) {
        self.init(
            red: CGFloat(red) / 255.0,
            green: CGFloat(green) / 255.0,
            blue: CGFloat(blue) / 255.0,
            alpha: CGFloat(a) / 255.0
        )
    }

    // 위에서 구현한 convenience init을 다시 사용해 16진수 값을 넣어주면 해당 값을 바로 변환해서 초기화 시켜주므로 매우 편해졌음!
    convenience init(rgb: Int) {
        self.init(
            red: (rgb >> 16) & 0xFF,
            green: (rgb >> 8) & 0xFF,
            blue: rgb & 0xFF
        )
    }

    // convenience init을 통해 16진수 값으로 바로 초기화 가능
    static let bbagaRain = UIColor(rgb: 0x609AF0)
}
{% endhighlight %}

기존에 `UIColor`를 초기화 시키기 위해서 `red`, `blue`, `green`, `alpha`값을 일일히 변환해 넣어줘야 했던 작업을 16진수를 바로 초기화시에 파라미터에 할당해 편하게 초기화 할 수 있게 만들어주는 것이 `convenience init`이다! <br/><br/>

나의 코드와 같이 이미 정의한 `convenience init`을 다시 호출해 또다른 `convenience init`을 만들어 줄 수 있다!<br/><br/>
대신 `convenience init`을 사용하기 위해선 위에서 말한것과 같이 기존에 정의되어있는 `init`을 반드시 호출해주어야 한다!<br/><br/>
또한 `convenience init`은 서브클래스에서 다시 재정의해 사용할 수 없다!<br/><br/>
그 이유와 `convenience init`이 기존의 `init`을 상속하는 과정에 대해서는 다음 포스팅을 통해 더 자세하게 알아보도록 하자...


## 2. 마무리
간단하게 `convenience init`에 대해 알아보았다! <br/><br/>
개발 당시에 '이거 끝나고 공부해야지'라고 생각하고 투두리스트에 넣어둔지 거의 3달이 되서 정리하고 포스팅을 했다.<br/>
(아직도 쌓아놓은 공부리스트는 한참인데...)<br/><br/>
그래도 꾸준히 정리하고 포스팅하다 보면 나의 공부 투두리스트가 비워질것이라 믿고, 꾸준히 포스팅을 이어나가도록 하겠다!

<br/><br/><br/><br/>
참고 : zedd0202, Swift ) init과 convenience init의 차이
(<https://zeddios.tistory.com/141>)<br/>
참고 : Swift 공식문서, Initialization
(<https://docs.swift.org/swift-book/LanguageGuide/Initialization.html>)<br/>
