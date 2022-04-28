---
title: "[Swift] Unicode - Extended Grapheme Clusters"
excerpt:
categories:
    - Swift
tags:
    - Unicode
    - Character
toc: true
last_modified_at: 2022-04-29-02:12:00 +0900
---
## 0. Unicode in swift
유니코드는 다른 쓰기 시스템에서 텍스트를 인코딩, 표현 및 처리하는 국제 표준이다.<br/>
<br/>
모든 언어의 거의 모든 문자를 표준화된 형태로 표현하고, 텍스트 파일이나 웹 페이지와 같은 외부 소스에서 해당 문자를 읽고 쓸 수 있다.<br/>
<br/>
Swift의 String과 Character는 유니코드를 완전히 준수하고 있다.

## 1. Extended Grapheme Clusters
Swift의 Character의 모든 인스턴스는 하나의 `Extended Grapheme Clusters`를 나타낸다고 한다.<br/>
<br/>
`Extended Grapheme Clusters`란 결합될 때 사람이 읽을 수 있는 단일 문자를 생성하는 하나 이상의 유니코드 스칼라의 시퀀스이다.<br/>
<br/>
문장으로만 보니 의미가 제대로 전달되지 않는다.<br/>
<br/>
공식문서의 예시를 통해 어떤것인지 알아보자.<br/>
<br/>

### 예시 1. é
{% highlight swift %}
let eAcute: Character = "\u{E9}"
print("eAcute is \(eAcute)")

let combinedEAcute: Character = "\u{65}\u{301}"
print("combinedEAcute is \(combinedEAcute)")

#=> prints eAcute is é
#=> prints combinedEAcute is é

{% endhighlight %}

다음의 에시를 보자.<br/>
<br/>
é는 단일 유니코드 스칼라(U+00E9)로 표현될 수 있다.<br/>
<br/>
하지만 같은 문자임에도 한 쌍의 유니코드 스칼라(일반문자 e: U+0065, 양음 악센트 \`: U+00E9)를 통해 표현될 수 있다.<br/>
<br/>
양음 악센트 스칼라는 앞의 스칼라에 그래픽으로 적용되며, 유니코드 인식 텍스트 렌더링 시스템에 의해 렌더링될 때 e를 é로 바꾼다.<br/>
<br/>

### 예시 2. 한글
`Extended Grapheme Clusters`는 많은 복잡한 스크립트 문자를 단일 Character 값으로 표현할 수 있는 좋은 방법이다.<br/>
<br/>
한글을 예시로 들 수 있는데, 한글 음절은 미리 구성되거나 분해된 시퀀스로 표현될 수 있다.<br/>
<br/>
이 두 표현 모두 Swift에서 하나의 Character 값을 가진다.

{% highlight swift %}
let precomposed: Character = "\u{D55C}"
let decomposed: Character = "\u{1112}\u{1161}\u{11AB}"

print(precomposed)
print(decomposed)

#=> prints 한
#=> prints 한

{% endhighlight %}

### 예시 3. 특수문자
문자뿐만 아니라 다양한 특수문자도 가능한데, 문자를 감싸는 특수문자(U+20DD) 또한 하나의 Character값으로 구성하는데 사용될 수 있다.

{% highlight swift %}
let enclosedEAcute: Character = "\u{E9}\u{20DD}"
let circle: Character = "\u{20DD}"

print(enclosedEAcute)
print(circle)

#=> prints é⃝
#=> prints   ⃝

{% endhighlight %}

### 예시 4. 이모지
이모지 또한 가능하다.<br/>
<br/>
지역 지표 기호의 유니코드 스칼라를 결합하여 (지역지표 기호 U : U+1F1FA, 지역지표 기호 S : U+1F1F8) 미국국기 이모지를 만들 수 있다.

{% highlight swift %}
let regionalIndicatorForUS: Character = "\u{1F1FA}\u{1F1F8}"
print(regionalIndicatorForUS)
print("\u{1F1FA}")
print("\u{1F1F8}")

#=> prints 🇺🇸
#=> prints 🇺
#=> prints 🇸
{% endhighlight %}


## 2. 마무리
Swift에서 사용되는 Unicode의 Extended Grapheme Clusters라는 개념을 배워봤다.<br/>
<br/>
앞으로 유니코드와 관련된 String, Character를 사용하게 된다면 조금 더 수월하게 이해할 수 있을 것 같다. (제발ㅠㅠ...)


<br/><br/><br/><br/>
출처 : Swift 공식문서, Strings and Characters (Unicode - Extended Grapheme Clusters)<br/>
(<https://docs.swift.org/swift-book/LanguageGuide/StringsAndCharacters.html>)
