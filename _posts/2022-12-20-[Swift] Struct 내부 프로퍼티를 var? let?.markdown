---
title: "[Swift] Struct 내부 프로퍼티를 var? let?"
excerpt:
categories:
    - OOP
tags:
    - struct
    - var
    - let
    - 캡슐화
toc: true
last_modified_at: 2022-12-20-15:40:00 +0900
---
## 0. 서론
공부를 위해 만들었던 프로젝트에 대해 코드리뷰를 받은 후 (Young 감사합니다!) 해당 내용에 대해 리팩토링을 하는 중이였다.<br/><br/>
구조체 내의 프로퍼티들을 private 설정하고 캡슐화를 작업하려고 하는데 의문이 들었다. <br/><br/>
{% highlight swift %}
// 캡슐화를 진행하던 모델
//TODO: property별 privacy 설정으로 캡슐화
struct ItBook: Decodable {
    let title: String
    let subtitle: String
    let isbn13: String
    let price: String
    let imageURL: String
    let url: String

    ...
}

{% endhighlight %}

캡슐화를 통해 프로퍼티를 `var`로 선언하고 `private` 키워드로 보호하는 목적은 객체 외부에서 참조나 접근이 있을 때 데이터가 변경될 여지가 있기 때문이다.<br/><br/>
그렇다면 프로퍼티를 `var`가 아닌 `let`으로 선언한다면 데이터에 접근 하더라도 변경 및 수정이 되지 않으니 충분히 데이터가 보호되지 않을까?<br/><br/>


## 1. 나의 생각
다시 내가 리팩토링하려는 모델을 보도록 하자.

{% highlight swift %}
// 캡슐화를 진행하던 모델
// property별 privacy 설정으로 캡슐화
struct ItBook: Decodable {
    let title: String
    let subtitle: String
    let isbn13: String
    let price: String
    let imageURL: String
    let url: String

    ...
}

`ItBook`이라는 모델을 통해 각 책들의 제목, 부제, 도서번호 등의 데이터를 전달받는다.<br/><br/>
내 앱은 `ItBook`을 통해 사용자에게 도서검색기능과 도서정보확인 기능만 제공하기 때문에 `ItBook` 데이터가 수정되야 할 필요가 없고 책의 제목, 저자, 도서번호같은 정보는 변하지 않고 고유한 정보라고 판단했다.<br/><br/>
그렇기 때문에 `ItBook` 내의 프로퍼티를 모두 `let`으로 선언하였다.<br/><br/>

## 2. 제대로 알기 - class, struct, 캡슐화

### 2-1. class, struct
일단 나는 내 생각과 지식이 정확한지 확인해보자.<br/><br/>
`ItBook` 구조체 내의 프로퍼티들은 모두 `String`으로 선언되어 있다.<br/><br/>
`String` 또한 구조체 타입이기 때문에 인스턴스가 `let`으로 선언될 경우 인스턴스의 모든 데이터가 수정이 불가능하다.<br/><br/>
만일 프로퍼티가 구조체가 아닌 클래스 타입이라면 그 프로퍼티의 내부 프로퍼티가 `var`일 경우 참조 및 수정이 가능할 것이다.<br/><br/>
여기서 깨달은 한가지... 현재는 API를 통해 `ItBook` 데이터 `String`으로만 받아오기 때문에 더이상 데이터가 변경될 여지가 없다고 판단했지만 만일 **클래스 타입**으로 선언된 프로퍼티라면 충분히 외부에서의 접근 및 정보수정이 가능할 것이다.<br/><br/>
이 점은 반드시 숙지하고 데이터 구조를 선언해야할 것이다.

### 2-2. 캡슐화
일단 캡슐화의 정확한 의미를 알아보자

> 캡슐화란 하나의 객체에 대해 그 객체가 특정한 목적을 위한 필요한 변수나 메소드를 하나로 묶는 것을 의미한다. 클래스를 만들 때 이 클래스에서 만들어진 객체가 특정한 목적을 잘 수행할 수 있도록 사용해야 할 변수와 그 변수를 가지고 특정한 액션 즉 메서드를 관련성 있게 클래스에 구성해야 한다.

간단하게 말하자면 해당 객체가 특정한 목적에 맞게 사용될 수 있도록 구성한다는 의미이다.<br/><br/>
이전의 함수 중심적인 프로그래밍 언어에서는 프로그램 내부의 데이터가 어디에서 어떻게 변경되는지 파악하기 어려웠고, 그로 인해 유지보수가 힘들었다.<br/><br/>
객체 지향에서는 객체 내부의 데이터를 외부에서 참조하지 못하도록 차단해 이러한 문제를 차단할 수 있는데 이러한 개념을 **정보 은닉(Information Hiding)**이라 하며, 이것이 바로 **캡슐화**의 개념이다.<br/><br/>
지금은 작은 토이프로젝트 내에서 혼자서 `ItBook` 모델을 하기 때문에 해당 객체의 목적을 이해하고 의도에 맞게 사용하지만, 혼자가 아니게 된다면 `ItBook` 모델에 접근해 데이터를 무분별하게 사용하게 되어 `ItBook` 모델을 설정한 목적이 흐려지고 유지보수와 협업에 문제가 발생할 수 있다.<br/><br/>
이 점을 생각한다면 데이터가 수정될 여지가 없더라도 목적에 맞게 사용되도록 `private` 설정이 주요할 것 같다.

## 3. 다른 사람들의 생각들
나와 비슷한 고민을 하고있는 사람들의 의견도 확인할 수 있었다. ([링크](https://forums.swift.org/t/to-var-or-let-struct-properties/52363))<br/><br/>
많은 의견중 하나가 대부분의 프로퍼티의 경우 값이 다른 프로퍼티와 연관되어 `getter`를 통해 받아질 수 있기 때문에 `var`를 통해 선언하고 그 이후에 `private`을 통해서 접근제어를 설정해준다고 하였다.<br/><br/>
그리고 다음과 같은 코드 예시를 통해 프로퍼티를 객체 내부에서만 변경 가능하게 설정하고 외부에서는 읽기만 가능하도록 설정해주기 위해 `private(set)` 키워드를 사용하기도 한다 하였다.

{% highlight swift %}
struct Person {
    private let name: String
    private(set) var age: Int

    static var curry: Person {
        return Person(name: "커리", age: 27)
    }

    mutating func addAge() {
        age += 1
    }
}

var curry = Person.curry

// 에러 발생! age 프로퍼티는 쓰기(set) 불가능
//curry.age = 28

// 내부에서 구현된 addAge 메소드를 통해서만 변경 가능
curry.addAge()

// age 프로퍼티는 읽기만 가능
print(curry.age)

#=> prints 28
{% endhighlight %}

추가로 프로퍼티에 `private` 설정을 해주면 초기화시에 해당 프로퍼티에 접근이 불가능하기 때문에 모델을 임의의 값을 통해 할당하는 것 또한 방지할 수 있었다.

{% highlight swift %}
struct Animal {
    let name: String
}

struct Person {
    private let name: String

    static var curry: Person {
        return Person(name: "커리")
    }
}

var tiger = Animal(name: "호랑이")
// tiger 변수에 임의의 값을 할당해 데이터를 변경
tiger = Animal(name: "사자")

var curry = Person.curry
// 에러 발생! init의 매개변수로 name 프로퍼티에 접근 불가능
//curry = Person(name:)

{% endhighlight %}

마지막으로 대부분의 경우엔 `var`로 프로퍼티를 선언하는 것이 옳다고 하였지만 생년월일(`ItBook`같은 경우 `isbn13`같은 도서번호가 될 것이다)같은 특수한 경우에만 `let`을 선언해 앞으로 절대 변하지 않을 값임을 명시하는게 올바르다고 하였다.<br/><br/>

## 4. 그렇다면 나의 결론과 변경점은?
최종적으로 리팩토링하게 된 방향은 다음과 같다.

{% highlight swift %}
struct ItBook: Decodable {
    private let title: String
    private let subtitle: String
    private let isbn13: String
    private let price: String
    private let imageURL: String
    private let url: String

    func getTitle() -> String {
        return title
    }

    func getSubTitle() -> String {
        return subtitle
    }

    func getISBN13() -> String {
        return isbn13
    }

    func getPrice() -> String {
        return price
    }

    func getImageURL() -> String {
        return imageURL
    }

    func getURL() -> String {
        return url
    }

    ...
}
{% endhighlight %}


1. 모든 프로퍼티들의 `private` 키워드 설정.<br/><br/>
모든 프로퍼티들의 접근제한을 통해 의도치 않은 곳에서의 데이터 참조를 막기로 하였다.

2. 프로퍼티들의 `let` 선언 유지.<br/><br/>
조사한 자료를 통해 판단하면 `title`, `subtitle`, `isbn13`을 제외하면 모두 `var` 선언으로 변경해주는게 더 옳은 방향이지만 현재의 프로젝트는 데이터를 불러오는 것에만 목적이 있고 프로젝트 자체가 그 외적인 기능을 위해서 구현한 모델이기 때문에 유지하도록 하였다.<br/><br/>
하지만 연습 프로젝트가 아닌 실제 서비스를 목적으로 하는 프로젝트가 된다면 이 점은 반드시 고려하고 프로퍼티를 선언해야 할 것이다.

3. `getSomething()` 메소드를 통한 데이터 쓰기
모델의 의도에 맞게 `getTitle`, `getURL` 등의 메소드를 통해서 해당 객체를 통해서 데이터를 받기만 할 수 있다는 의도를 표현하였다.

## 5. 마무리
Young에게 코드리뷰를 받은 후에 다음과 같은 고민과 공부를 하게 되었다.<br/><br/>
지금 보니 코드를 작성할 때 OOP에 대한 이해가 많이 부족한 상태에서 코드 작성을 해왔던 것 같다.<br/><br/>
여러 자료조사를 통해서 캡슐화를 진행해보긴 했지만 아직 많이 부족한 코드라고 생각하기 때문에 더 디벨롭 해보면서 어떤식으로 좋은 코드를 작성할 수 있을지 고민해 봐야겠다.<br/><br/>
마지막으로 간단한 코드리뷰 부탁했는데 좋은 인사이트를 남겨주신 Young에게 감사말씀 드립니다!<br/><br/>

<br/><br/><br/><br/>
프로젝트 코드 : jaewoonglee-swift, ItBookSearchApp - ItBookStore.swift
(<https://github.com/JaewoongLee-swift/ItBookSearchApp/blob/develop/ItBookSearchApp/ItBookSearchApp/Model/ItBookStore.swift>)<br/>
참고 : 둥찬, [Swift] private, 접근제어, 캡슐화, 은닉화
(<https://seungchan.tistory.com/entry/Swift-private-접근제어-캡슐화-은닉화>)<br/>
참고 : Swift Forum - To var or let struct properties?
(<https://forums.swift.org/t/to-var-or-let-struct-properties/52363>)<br/>
참고 : jake-kim, [iOS - swift] private(set) 외부에서 읽기만 가능하고, 내부에서는 쓰기가 가능하도록 하는 간결한 코드 (getter, setter)
(<https://ios-development.tistory.com/690>)<br/>
