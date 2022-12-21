---
title: "[Swift] Copy On Write"
excerpt:
categories:
    - Swift
tags:
    - struct

toc: true
last_modified_at: 2022-12-21-15:44:00 +0900
---
## 0. 서론
iOS 면접질문을 찾던 중이였던가... 그냥 공부중이였나... 기억은 확실이 나지 않지만 어제 'Copy On Write'라는 키워드를 보고 처음 보는 키워드여서 궁금해서 찾아보게 되었다.<br/><br/>


## 1. Copy On Write(COW)
일단 'Copy On Write'라는 키워드가 지닌 의미를 먼저 보자.

> 때때로 암시적 공유 또는 섀도잉이라고도 불리는 COW(Cocopy-on-write)는 수정 가능한 리소스에 대한 "복제" 또는 "복사" 작업을 효율적으로 구현하기 위해 컴퓨터 프로그래밍에 사용되는 자원 관리 기술이다. 리소스가 중복되었지만 수정되지 않은 경우, 새 리소스를 만들 필요가 없다. 리소스는 복사본과 원본 간에 공유할 수 있다. 수정은 여전히 복사본을 만들어야 하므로 리소스 복사 작업은 첫 번째 수정까지 연기된다. 이러한 방식으로 리소스를 공유함으로써, 수정되지 않은 복사본의 자원 소비를 크게 줄이는 동시에 자원 수정 작업에 작은 오버헤드를 추가할 수 있다.
(Wikipedia - Copy-on-write)

아하 이게 바로 COW 구나... 의미로는 알겠는데 아직 어떤식으로 쓰이는지 감이 잘 안온다. 코드 예시를 통해 보자.<br/><br/>

{% highlight swift %}
// 매개변수의 메모리주소를 리턴하는 함수
func address(of object: UnsafeRawPointer) -> String {
    let address = Int(bitPattern: object)

    return String(format: "%p", address)
}

var arrayA = [1, 2, 3, 4, 5]
var arrayB = arrayA

print(address(of: &arrayA))
print(address(of: &arrayB))

print("------------------")

arrayB.append(6)
print(address(of: &arrayA))
print(address(of: &arrayB))


#=> prints 0x600002108750
#=> prints 0x600002108750
#=> prints ------------------
#=> prints 0x600002108750
#=> prints 0x600002904020

#=> prints 0x6000021087a0
{% endhighlight %}

코드를 보면 `arrayA`변수에 Collection 타입을 할당했고 `arrayB` 변수에는 `arrayA`를 할당했다.<br/><br/>
내가 알고있는 사실로는 Collection 타입은 struct 이기 때문에 인스턴스 자체가 할당되면 값은 복사된다고 알고있다.<br/><br/>
하지만 메모리주소를 확인해보면 `arrayA`, `arrayB` 두 변수가 가리키는 메모리주소는 동일한 것을 알 수 있다.<br/><br/>
그리고 `arrayB`의 값을 변경하게 되면 그때야 비로소 `arrayB`의 메모리 주소가 변하는 것을 볼 수 있다.<br/><br/>
이렇게 Swift에서는 Collection 타입같은 Value 타입의 경우 COW를 통해 성능을 최적화 하였다고 명시되어있다.

> All standard library containers in Swift are value types that use COW (copy-on-write) to perform copies instead of explicit copies. In many cases this allows the compiler to elide unnecessary copies by retaining the container instead of performing a deep copy. (Swift 깃헙 레포지토리에서)

## 2. Copy On Write with Value Type
Value 타입은 모두 COW를 가지고 있다 이거지?<br/><br/>
그럼 가장 많이 사용하는 Value 타입인 String 타입도 똑같이 동작하는지 확인해보자.
{% highlight swift %}
var strA = "안녕하세용"
var strB = strA

print(address(of: &strA))
print(address(of: &strB))

#=> prints 0x100008008
#=> prints 0x100008018
{% endhighlight %}

**"엥? String은 왜 주소가 다르냐?!"** 라는 생각이 들었는데, [naljin님의 블로그]를 통해서 이유를 확인할 수 있었다.<br/><br/>

> UnsafeRawPointer를 인자로 받는 함수에 Array를 전달하는 것은 Array 인자의 첫번째 배열 인자의 주소를 전달하는 것이다. 따라서 Array들이 같은 저장공간을 사용한다면 동일한 값이 나오게 된다.
> UnsafeRawPointer를 인자로 받는 함수에 String을 전달하는 것은 String을 나타내는 '임시 UTF-8의 Pointer'를 전달하는 것이다. 따라서 이 주소는 같은 String일지라도 매 호출마다 달라질 수 있다.

<br/>
`UnsafeRawPointer`에 대해 제대로 이해하고 있어야 제대로 이해가 되겠지만, 간단히 정리해보면 타입에 따라 어떤 주소값을 전달하는지 메커니즘이 다른것으로 이해하면 될 것 같다!

## 3. 마무리
오늘은 Copy On Write의 개념을 알게되었다.<br/><br/>
이번 내용은 실제 개발에 응용할 때도 중요하지만 CS적인 개념을 알아간다는데에 더 의미가 있던것 같다.<br/><br/>
CS 공부도 하고, iOS 개념도 공부하고, 알고리즘 공부도 하고, 코딩도 해야할텐데 언제 다하지...

<br/><br/><br/><br/>
참고 : Wikipedia, Copy-on-write
(<https://en.wikipedia.org/wiki/Copy-on-write>)<br/>
참고 : 냄수, [iOS] Swift Memory - COW (Copy On Write)
(<https://nsios.tistory.com/56>)<br/>
참고 : GitHub, apple/swift
(<https://github.com/apple/swift/blob/main/docs/OptimizationTips.rst#id4>)<br/>
참고 : naljin, [Swift] Value Type의 메모리 주소
(<https://sujinnaljin.medium.com/swift-value-type의-메모리-주소-7d4c3de73b11>)<br/>
참고 : 유셩장, iOS) [번역] Swift의 Copy-on-Write 메커니즘
(<https://sihyungyou.github.io/iOS-copy-on-write/>)<br/>
참고 : StackOverflow, How to prove "copy-on-write" on String type in Swift
(<https://stackoverflow.com/questions/46747363/how-to-prove-copy-on-write-on-string-type-in-swift>)</br>


[naljin님의 블로그]: https://sujinnaljin.medium.com/swift-value-type의-메모리-주소-7d4c3de73b11
