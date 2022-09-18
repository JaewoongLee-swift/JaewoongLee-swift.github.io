---
title: "[Swift] 클로저 - 3"
excerpt:
categories:
    - Swift
tags:
    - Closure
toc: true
last_modified_at: 2022-09-19-00:43:00 +0900
---
## 1. 클로저에서 값을 캡쳐한다는 것은…
클로저의 기본 개념부터 보면...<br/><br/>
**Closure란 내부 함수와 내부 함수에 영향을 미치는 주변 환경을 모두 포함한 객채이다.**<br/><br/>

예제를 통해 보자면

{% highlight swift %}
func doSomething() {
    var message = "Hi, I'm Curry!"

    //클로저 범위 시작
    var num = 10
    let closure = { print(num) }
    //클로저 범위 끝

    print(message)
}
{% endhighlight %}

closure란 익명함수는, 클로저 내부에서 **외부 변수인 num이라는 변수를 사용(print)** 하기 때문에 **num의 값을 클로저 내부적으로 저장** 하고 있는데, 이것을 클로저에 의해 **‘num의 값이 캡쳐되었다’** 라고 표현함.<br/><br/>

message란 변수는 클로저 내부에서 사용하지 않기 때문에 **클로저에 의해 값이 캡쳐되지 않음!**<br/><br/>

이것이 캡쳐라는 것이고… 이번엔 클로저의 값 캡쳐 방식에 대해 알아보자.

### 1-1. 클로저의 값 캡쳐방식
결론부터 말하자면,<br/><br/>

Closure는 값을 캡쳐할 때 Value/Reference 타입에 관계 없이 Reference Capture한다.<br/><br/>

이게 또 무슨말이냐?<br/><br/>

위의 예시에서 `num` **변수를 클로저 내부적으로 저장**한다고 했음. 근데 `num`은 **Int 타입이고 구조체 형식**이기 때문에 **Value 타입**임. 따라서 **값을 복사해서 저장**해야 되는 것이 일반적!<br/><br/>

그러나 클로저는 **Value/Reference 타입에 관계없이** 캡쳐하는 값을 **참조**함!!<br/><br/>

이것을 **Reference Capture**라고 함.<br/><br/>

예시를 통해 보자.

{% highlight swift %}
func doSomething() {
    var num: Int = 0
    print("num check #1 = \(num)")

    let closure = {
        print("num check #3 = \(num)")
    }

    num = 20
    print("num check #2 = \(num)")
    closure()
}

#=> prints num check #1 = 0
#=> prints num check #2 = 20
#=> prints num check #3 = 20
{% endhighlight %}

먼저, `closure`는 `num`이라는 외부 변수를 클로저 내부에서 사용하기 때문에 `num`을 캡쳐할 것임! 근데 어떻게 캡쳐한다?<br/> Reference Capture. 즉, `num`이란 변수를 참조함!<br/><br/>

따라서, `closure`를 실행하기 전에 `num` 이란 값을 외부에서 변경하면 클로저 내부에서 사용하는 `num`의 값 또한 변경됨!

{% highlight swift %}
func doSomething() {
    var num: Int = 0
    print("num check #1 = \(num)")

    let closure = {
        num = 20
        print("num check #3 = \(num)")
    }

    closure()
    print("num check #2 = \(num)")
}

#=> prints num check #1 = 0
#=> prints num check #3 = 20
#=> prints num check #2 = 20
{% endhighlight %}

클로저 외부에 있는 `num`의 값도 변경이 됨!<br/><br/>

이렇듯, Closure는 값의 타입이 Value건 Reference건 모두 **Reference Capture**를 한다는 사실!<br/><br/>
그럼 만약 나는 **Value Type으로 Capture**를 하고 싶으면 어떻게 할까??

## 2. 클로저의 캡쳐 리스트 (Capture Lists)

{% highlight swift %}
let closure = { [num, num2] in
{% endhighlight %}

클로저의 시작인 `{` 의 바로 옆에 `[ ]`를 이용해 캡쳐할 멤버를 나열한다. 이때 `in` 키워드도 꼭 함께 작성한다.<br/><br/>

### 2-1. Value Type의 값을 복사해서 Capture할 수 없을까?

가능!<br/><br/>
방금 설명한 Capture Lists를 사용하면 가능하다.<br/><br/>
Value Type의 경우, **Value Capture 하고 싶은 변수를 리스트로 명시**해주는 것!!<br/><br/>

{% highlight swift %}
func doSomething() {
    var num: Int = 0
    print("num check #1 = \(num)")

    let closure = { [num] in
        print("num check #3 = \(num)")
    }

    num = 20
    print("num check #2 = \(num)")
    closure()
}

#=> prints num check #1 = 0
#=> prints num check #2 = 20
#=> prints num check #3 = 0
{% endhighlight %}

closure를 실행하기 전에 외부변수 num의 값을 20으로 변경했지만 클로저의 num에는 영향을 주지 않음! <br/><br/>
근데, 한 가지 더 유의해야 할 점은 Value Type으로 캡쳐한 경우,<br/><br/>
**Closure를 선언할 당시의 num의 값을 Constant Value Type으로 캡쳐함.**<br/><br/>
자, 여기서 중요한 것은 Constant Value Type, 즉 “**상수**”로 캡쳐된다는 것.<br/><br/>

따라서 다음과 같이

{% highlight swift %}
let closure = { [num] in
        num = 2 // error : Cannot assign to value: 'num' is an immutable capture
        print("num check #3 = \(num)")
    }
{% endhighlight %}

closure 내부에서 **Value Capture된 값을 변경할 수 없음**<br/><br/>

정리하자면,<br/><br/>
클로저는 기본적으로 Value Type의 값도 Reference Capture를 하지만 Capture Lists를 사용하면 Constant Value Type으로 캡쳐가 가능함!

### 2-2. Reference Type의 값을 복사해서 Capture할 수 없을까?

Value Type의 값을 Capture Lists를 통해 Value Capture가 가능하니까…<br/><br/>

**Reference Type의 값도 Capture Lists를 통해서 Value Capture가 가능할까?**

{% highlight swift %}
class Human {
    var name: String = "Curry"
}

var human1: Human = .init()

let closure = { [human1] in
    print(human1.name)
}

human1.name = "Unknown"
closure()
{% endhighlight %}

위와 같은 코드가 있을 때, human1이라는 인스턴스는 Reference Type임. 근데 내가 **Closure Capture Lists를 통해 human1을 캡쳐했으니까, human1은 복사되어 캡쳐 됬을까?**<br/><br/>

{% highlight swift %}
//결과는...
#=> prints Unknown
{% endhighlight %}

아니. **Capture Lists를 사용한다 해도, Reference Type은 Reference Capture를 한다!** <br/><br/>
‘그럼 Reference Type은 Closure Capture Lists를 사용할 필요가 없겠네?’ 싶겠지만, 이건 클로저와 ARC를 보면 언제 쓰는지 이해할 수 있음. <br/><br/>

## 3. 클로저와 ARC
일단 ARC란?<br/>
인스턴스의 Reference Count를 자동으로 계산하여 메모리를 관리하는 방법!?<br/>
(참고 : https://jaewoonglee-swift.github.io/swift/Swift-ARC/)<br/><br/>


더 세세하게 얘기하기 위해 클로저와 인스턴스 간의 관계에 대해 이야기 해보자.<br/><br/>

Human이란 클래스를 만들고, name을 얻을 수 있는 Lazy 프로퍼티를 클로저를 통해 초기화했음.<br/><br/>

{% highlight swift %}
class Human {
    var name: String = ""

    lazy var getName: () -> String = {
        return self.name
    }

    init(name: String) {
        self.name = name
    }

    deinit {
        print("Human Deinit!")
    }
}

var curry: Human? = .init(name: "Lee-Curry")
print(curry!.getName())

curry = nil

#=> prints Lee-Curry
{% endhighlight %}
이후 curry란 인스턴스를 만들고, **클로저로 작성**되어 있는 `getName`이란 **지연 저장 프로퍼티를 호출함.**<br/><br/>

그리고 더이상 curry란 인스턴스가 필요없어서 nil을 할당함.<br/><br/>

그러면 인스턴스에 nil이 할당되었고, 나는 이 인스턴스에 다른 변수를 대입한 적 없고, 따라서 **인스턴스의 Reference Count가 0이 되어 deinit**이 호출되어야 할 것인데…<br/><br/>

**근데 deinit이 불리지 않음!!!!**<br/><br/>

왜일까??? 이유는 바로 클로저에 있음.

### 3-1. 클로저의 강한 순환 참조
클로저는 Reference Type으로, Heap에 살고 있음!<br/><br/>
따라서, 내가 생성한 human이란 인스턴스는 getName을 호출하는 순간<br/><br/>

{% highlight swift %}
print(curry!.getName())
{% endhighlight %}

`getName`이란 클로저가 Heap에 할당되며, 이 클로저를 참조할 것임!<br/>
(지연 저장 프로퍼티니까 인스턴스 생성 직후가 아닌, 호출되는 순간 메모리에 올라감)<br/><br/>
근데, `getName` 클로저를 보면

{% highlight swift %}
class Human {
	…
    lazy var getName: () -> String = {
        return self.name
    }
	…
}
{% endhighlight %}

`self`를 통해 `Human` **이란 인스턴스의 프로퍼티**에 접근하고 있음!!<br/><br/>
클로저는 Reference 값을 캡쳐할 때 기본적으로 “**strong**”으로 캡쳐를 함.<br/><br/>
따라서, 이때 **Human이란 인스턴스의 Reference Count가 증가**해버림….!!!<br/><br/>

`Human` **인스턴스는 클로저를 참조하고, 클로저는** `Human` **인스턴스(의 변수)를 참조**하기 때문에<br/><br/>

서로가 서로를 참조하고 있어 둘 다 **메모리에서 해제되지 않는 강한 순환 참조**가 발생해 버린것!<br/><br/>

그럼 어떻게 할까? >> 강한 순환참조는 weak, unowned를 통해 해결 가능함!

### 3-2. 클로저의 강한 순환 참조 해결법
클로저에서 해결하려면 앞서 공부한 weak & unowned에 우리가 Reference Type일 땐 필요 없다 느꼈던 Capture Lists를 이용해야함

> ‘weak, unowned + Capture Lists’

클로저가 프로퍼티에 접근할 때 **self를 참조하면서 문제가 발생함**. 따라서 **self에 대한 참조를 Closure Capture Lists**를 통해 **weak, unowned로 캡쳐**해버리는 것이다!

{% highlight swift %}
// weak 키워드 사용할 때
class Human {
	…

    lazy var getName: () -> String = { [weak self] in
        return self?.name ?? ""
    }
	…
}

// unowned 키워드 사용할 때
class Human {
    …

    lazy var getName: () -> String = { [unowned self] in
        return self.name
    }
    …
}

#=> prints Lee-Curry
#=> prints Human Deinit!
{% endhighlight %}

이런 식으로 weak, unowned로 Reference Capture를 하는 것이다.<br/><br/>
이렇게 클로저 리스트를 통해 강한 순환 참조를 해결해 줄 수 있음.<br/><br/>
또한 deinit이 정상 실행되는것을 볼 수 있다!

## 4. Swift에서 클로저는 여러 개니까
<img width="797" alt="%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-09-12%20%EC%98%A4%ED%9B%84%208 03 44" src="https://user-images.githubusercontent.com/83946704/190913759-5a5cc7eb-35b1-45cc-9d3d-60361641c742.png">

클로저는 **전역 함수, 중첩 함수, 익명 함수** 이 세가지를 모두 아우르는 말<br/>
(일반적으로 익명 함수를 칭하긴 함)<br/><br/>

위에서 Unnamed Closure, **익명 함수**일때만 값 캡쳐 방식을 살펴봤으므로 **Named Closure**일 때 값 캡쳐하는 방식을 살펴보자.

### 4-1. 전역함수

우리가 일반적으로 func 쓰고 작성하는 함수를 말하는데 (중첩x) 이 함수는 주변의 어떠한 값도 캡쳐하지 않음!

### 4-2. 중첩함수

**자신을 포함하고 있는 함수의 값**을 캡쳐함!

{% highlight swift %}
func outer() {
    var num: Int = 0

    func inner() {
        print(num)
    }
}
{% endhighlight %}

이런 함수가 있으면 **inner 함수**는 나를 포함하고 있는 **함수 outer의 num이란 값을 사용하니 캡쳐함** (당연히 Reference Capture)

## 5. 중첩 함수 & @escaping과 메모리의 관계
‘[클로저 - 2]’에서 배운 @escaping의 의문을 풀어보자<br/><br/>

함수 파라미터를 받을 때 @escaping이란 키워드 없이 받은 클로저는 모두 **non-escaping** 클로저이고, 따라서 다음과 같은 특징을 가짐.<br/><br/>

- 함수 내부에서 직접 실행하기 위해서만 사용한다
- 따라서 파라미터로 받은 클로저는 변수나 상수에 대입할 수 없고, 중첩 함수 내부에서 클로저를 사용할 경우, 중첩 함수를 리턴할 수 없다
- 함수의 실행 흐름을 탈출하지 않아, 함수가 종료되기 전에 무조건 실행 되어야 한다

위와 같은 제약이 왜 생겼을까?<br/><br/>
바로 **클로저가 함수 외부로 탈출하지 못하게 하기 위해서**임.<br/><br/>

무슨말일까?<br/><br/>
non-escaping 클로저는 해당 클로저가 **함수가 종료되기 직전에 무조건 실행**이 된다는 조건. 근데 만약 변수나 상수에 대입할 경우, 해당 클로저나 변수가 상수로 **함수에서 리턴**될 수 있고, **중첩 함수 내부에서 클로저를 사용한 경우, 중첩 함수가 클로저를 캡쳐하기 때문에** 중첩 함수를 리턴 시 **클로저가 중첩 함수에 의해 함수 외부에서 실행**될 수 있기 때문!!<br/><br/>

{% highlight swift %}
func outer(closure: () -> ()) -> () -> () {
    func inner() {
        closure()
    }

    return inner // error: Escaping local function captures non-escaping parameter 'closure'
}
{% endhighlight %}

이런 식으로 closure를 사용하는 inner가 리턴되어 버리면 외부에서 inner를 받고 실행할 때 closure가 호출되어야 하니까 제약을 걸어둔 것!

### 5-1. 왜 non-escaping과 escaping을 나눴을까?
“편하게 모두 escaping 클로저로 선언하면 되지 않나?” 라고 생각할 수 있음.<br/><br/>

non-escaping 클로저에 ‘**함수 직전에 무조건 실행 되어야 한다**’는 조건이 붙는 이유는, 클로저가 이 **함수 내부**에서만 쓰이기 때문에 컴파일러가 메모리 관리를 지저분하게 하지 않아도 되기 때문에 <u>성능향상</u>이 이루어지기 때문.<br/><br/>

**non-escaping**의 경우 **함수가 종료됨과 동시에 클로저도 사용이 끝난다**. 하지만 **escaping**의 경우, 함수가 종료되더라도 **실제 클로저가 사용되지 않을 때까지 메모리를 추적**해야 함.

## 6. 마무리
클로저의 대단원 마무리!!<br/>
Swift를 쓰면서 수없이 만난 클로저들을 이제 어떤 녀석들이였는지 모두 알게 되었다.<br/><br/>

이번 포스팅에선 특히 클로저의 캡쳐방식, 메모리 관리에 대해 알게 되어서 자주 사용되는 @escaping 클로저를 만날때 `[weak self]`의 이유와 사용처를 확실히 이해할 수 있게 되었다.<br/><br/>

앞으로 계속 개발공부를 하면서 생각없이 사용하는게 아닌 그에 대한 이유와 원리를 알아가면서 사용해야 더 좋은 개발자가 될거라 생각한다.<br/><br/>

앞으로도 잘하자! :)




<br/><br/><br/><br/>
참고 : 개발자 소들이, Swift) 클로저(Closure) 정복하기(3/3) - 클로저와 ARC
(<https://babbab2.tistory.com/83?category=828998>)<br/>

[클로저 - 2]: https://jaewoonglee-swift.github.io/swift/Swift-Closure-2/
