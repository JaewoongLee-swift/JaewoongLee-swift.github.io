---
title: "[Swift] 클로저 - 2"
excerpt:
categories:
    - Swift
tags:
    - Closure
toc: true
last_modified_at: 2022-09-18-22:22:00 +0900
---
## 1. 트레일링 클로저(Trailing Closure)
트레일링 클로저란?<br/><br/>
함수의 마지막 파라미터가 클로저일 때, 이를 파라미터 값 형식이 아닌 함수 뒤에 붙여 작성하는 문법. 이때, Argument Label은 생략된다.<br/><br/>
어렵지만 중요한것 두가지,<br/>
**마지막 파라미터가 클로저! Argument Label은 생략!**
<br/><br/>
예제를 통해 보자..

### 1-1. 파라미터가 클로저 하나인 함수
다음과 같이 클로저 하나만 파라미터로 받는 함수가 있음.

{% highlight swift %}
func doSomething(closure:  () -> ()) {
	closure()
}
{% endhighlight %}

이 함수를 호출하려고 하면 어떻게 해야 했느냐…

{% highlight swift %}
doSomething(closure: { () -> () in
	print(“Hello”)
})
{% endhighlight %}

이렇게 해야했다.<br/><br/>

다음과 같이 클로저가 파라미터의 값 형식으로 함수 호출 구문 ( ) 안에 작성되어 있는 것을 Inline Closure라 부름.

지금처럼 클로저를 파라미터 값 형식으로 보내는 것이 아닌, **함수의 가장 마지막에 클로저를 꼬리처럼 덧붙여서** 쓸 수 있음.

{% highlight swift %}
doSomething() { () -> () in
	print(“Hello”)
}
{% endhighlight %}

이렇게 쓰는 것이 바로 **Trailing Closure**<br/><br/>

중요한 핵심 두가지<br/><br/>
1. 파라미터가 클로저 하나일 경우, 이 클로저는 첫 파라미터이자 **마지막 파라미터** 이므로 트레일링 클로저가 가능
2. `closure`라는 **Argument Label**은 트레일링 클로저에선 **생략**됨

<br/>
근데 여기서 파라미터가 클로저 하나일 경우엔 더 진화해서 호출구문인 **(** **)**도 생략할 수 있음.

{% highlight swift %}
doSomething { () -> () in
	print(“hello”)
}
{% endhighlight %}

### 1-2. 파라미터가 여러 개인 함수
다음과 같이 첫 번째 파라미터로 success라는 클로저를 받고, 두 번째 파라미터로 fail이라는 클로저를 받는 함수가 있음.

{% highlight swift %}
func fetchData(success: () -> (), fail: () -> ()) {
	//do something…
}
{% endhighlight %}

이런 함수가 있을 때 Inline Closure의 경우…

{% highlight swift %}
fetchData(success: { () -> () in
	print(“Success”)
}, fail: { () -> () in
	print(“Fail”)
})
{% endhighlight %}

이렇게 호출할 것이다. 하지만 **트레일링 클로저의 경우... 마지막 파라미터의 클로저는 함수 뒤에 붙여 쓸 수 있다!!**

## 2. 클로저의 경량문
문법을 최적화 하여 클로저를 단순하게 쓸 수 있게 하는 것<br/><br/>
다음과 같은 함수가 있다고 가정해보자.

{% highlight swift %}
func doSomething(closure: (Int, Int, Int) -> Int) {
	closure(1, 2, 3)
}
{% endhighlight %}

이 함수는 **파라미터로 받은 클로저**를 실행하는데, 이때 **클로저의 파라미터로 1, 2, 3**이란 숫자를 넘겨주고 있음.<br/><br/>
그렇다면, 실제 이 함수를 호출할 때 어떻게 했어야 했냐면,<br/><br/>

{% highlight swift %}
doSomething(closure: { (a: Int, b: Int, c: Int) -> Int in
	return a + b + c
})
{% endhighlight %}

이렇게 클로저를 full로 작성했어야 했음! (+ Inline Closure 방식)<br/><br/>
이를 경량 문법으로 간단하게 바꿔보겠음.

### 2-1. 파라미터 형식과 리턴 형식을 생략할 수 있다
{% highlight swift %}
doSomething(closure: { (a, b, c) in
	return a + b + c
})
{% endhighlight %}

### 2-2. Parameter Name은 Shortand Argument Names으로 대체하고, 이 경우 Parameter Name과 in 키워드를 삭제한다.

**Shortand Argument Names**란?<br/>
**Parameter Name 대신 사용**할 수 있는것.<br/><br/>

예를 들어, 위의 함수를 통해 보자면

{% highlight swift %}
doSomething(closure: { (a, b, c) in
	return a + b + c
})
{% endhighlight %}

a -> **$0**, b -> **$1**, c -> **$2**, in을 생략 하는것임.<br/><br/>

이런 식으로 **$**와 **index**를 이용해 **Parameter에 순서대로 접근**하는 것이 바로 **Shortand Argument Name**임.<br/>
($의 개수는 Parameter의 개수만큼 있을것임!)<br/><br/>

따라서, 경량 문법 규칙에 의해 위 구문은
{% highlight swift %}
doSomething(closure: {
	return $0 + $1 + $2
})
{% endhighlight %}

이렇게 간단하게 경량화가 가능!

### 2-3. 단일 리턴문만 남을 경우, return도 생략한다.
단일 리턴문이란 것은,

{% highlight swift %}
doSomething(closure: {
	return $0 + $1 + $2
})
{% endhighlight %}

이렇게 **클로저 내부에 코드**가 **return 구문 하나만 남은 경우**를 말함.<br/><br/>
이때는 **return이란 키워드**도 다음과 같이 **생략** 가능!!

{% highlight swift %}
doSomething(closure: {
	$0 + $1 + $2
})
{% endhighlight %}

만일 단일 리턴문이 아닐 경우엔, 에러발생!!
{% highlight swift %}
doSomething(closure: {
	print(“\($0 + $1 + $2)”)
	$0 + $1 + $2		// 에러발생…
})
{% endhighlight %}

### 2-4. 클로저 파라미터가 마지막 파라미터면, 트레일링 클로저로 작성한다.
{% highlight swift %}
doSomething(closure: {
	$0 + $1 + $2
})
{% endhighlight %}

우리는 트레일링 클로저를 배웠으므로 위의 함수를 바꿔보자면,

{% highlight swift %}
doSomething() {
	$0 + $1 + $2
}
{% endhighlight %}

이렇게 작성이 가능함. 또한 파라미터가 하나인 경우 ( ) 도 생략 가능함!

### 2-5. ( )에 값이 아무 것도 없다면 생략한다

{% highlight swift %}
doSomething {
	$0 + $1 + $2
}
{% endhighlight %}

**이것이 최종화된 클로저의 경량 문법!!!**

## 3. @autoclosure
autoclosure란?<br/>
**파라미터로 전달된 일반 구문 & 함수를 클로저로 래핑(Wrapping) 하는것**<br/><br/>

먼저, autoclosure는 **파라미터 함수 타입 정의 바로 앞**에다가 붙어야함.<br/><br/>

{% highlight swift %}
func doSomething(closure: @autoclosure () -> ()) {
}
{% endhighlight %}
이렇게 했을 경우, 이제 closure란 파라미터는 **실제 클로저를 전달받지 않지만, 클로저처럼 사용이 가능!**<br/><br/>

다만, 클로저와 다른 점은 실제 클로저를 전달하는 것이 아니기 때문에 파라미터로 값을 넘기는 것처럼 **( )를 통해 구문을 넘겨줄 수 있음**

{% highlight swift %}
doSomething(closure: 1 > 2)
{% endhighlight %}

이렇게!! 여기서 `1 > 2`는 **클로저가 아닌 일반 구문**이지만, 실제 함수 내에서는

{% highlight swift %}
func doSomething(closure: @autoclosure () -> ()) {
	closure()
}
{% endhighlight %}

이렇게 **일반 구문을 클로저처럼 사용 할 수 있음!!** 왜냐? **클로저로 래핑**한 것이니까!!! <br/><br/>

다만 주의점은,

{% highlight swift %}
func doSomething(closure: @autoclosure (Int) -> (Bool)) {	// 에러발생
}
{% endhighlight %}

autoclosure를 사용할 경우, 반드시 파라미터가 없어야함!!! 리턴타입은 상관 없음!

### 3-1. autoclosure 특징 : 지연된 실행
원래, 일반 구문은 작성되자마자 실행되어야 하는 것이 맞음.<br/><br/>
근데 autoclosure로 작성하면, 함수 내에서 클로저를 실행할 때 까지 구문이 실행되지 않음.<br/><br/>
왜냐? 함수가 실행될 시점에 구문을 클로저로 만들어주니까…<br/><br/>

따라서, autoclosure의 특징은<br/><br/>
원래 바로 실행되어야 하는 구문이 지연되어 실행한다는 특징이 있음.

(예시1: <https://jusung.github.io/AutoClosure/>)<br/>
(예시2: <https://eunjin3786.tistory.com/468>)

## 4. @escaping
{% highlight swift %}
func doSomething(closure: () -> ()) {
}
{% endhighlight %}

지금까지 우리가 짜왔던 위와 같은 클로저는 모두 **non-escaping Closure!** 무슨말이냐면…<br/><br/>

non-escaping Closure는
1. 함수 내부에서 직접 실행하기 위해서만 사용한다.
2. 따라서 파라미터로 받은 클로저를 변수나 상수에 대입할 수 없고, 중첩 함수에서 클로저를 사용할 경우, 중첩함수를 리턴할 수 없다.
3. 함수의 실행 흐름을 탈출하지 않아, 함수가 종료되기 전에 무조건 실행되어야 한다.

실제로 상수에 클로저를 대입해보면,

{% highlight swift %}
func doSomething(closure: () -> ()) {
	let function: () -> () = closure	// 에러발생(non-escaping parameter)
}
{% endhighlight %}

**non-escaping parameter**라고 에러가 뜸<br/><br/>
또한 함수의 흐름을 탈출하지 않는다는 말은, **함수가 종료되고 나서 클로저가 실행될 수 없다는 말!**

{% highlight swift %}
func doSomething(closure: () -> ()) {
	print(“function start”)

	DispatchQueue.main.asyncAfter(deadline: .now() + 10) {	// 에러발생(non-escaping parameter)
		closure()
	}
	print(“function end”)
}
{% endhighlight %}

따라서, 10초 뒤 클로저를 실행하는 구문을 넣으면, **함수가 끝나고 클로저가 실행되기 때문에 에러가 발생!!**<br/><br/>

또한, 만약 **중첩함수 내부**에서 **매개변수로 받은 클로저를 사용**할 경우,

{% highlight swift %}
func outer(closure: () -> ()) -> () -> () {
	func inner() {
		closure()
	}
	return inner	// 에러발생
}
{% endhighlight %}

중첩함수를 리턴할 수 없음.<br/><br/>

이 모든 에러의 원인은 **non-escaping closure의 주변 값 capture 방식**에 있음 <br/>(클로저-3에서 다룰 예정).<br/><br/>

이렇게 함수 실행을 벗어나서 함수가 끝난 후에도 클로저를 실행하거나, 중첩함수에서 실행 후 중첩 함수를 리턴하고 싶거나, 변수/상수에 대입하고 싶은 경우!!<br/><br/>

이때 사용하는것이….. **@escaping**

{% highlight swift %}
//사용방법 : parameter type 앞에 @escaping 키워드 작성
func doSomething(closure: @escaping () -> ()) {
}
{% endhighlight %}

변수나 상수에 **파라미터로 받은 클로저를 대입** 가능!

{% highlight swift %}
func doSomething(closure: @escaping () -> ()) {
	let function: () -> () = closure
}
{% endhighlight %}

**함수가 종료된 후에도 클로저가 실행**될 수 있음!!

{% highlight swift %}
func doSomething(closure: @escaping () -> ()) {
	print(“function start”)

	DispatchQueue.main.asyncAfter(deadline: .now() + 10) {
		closure()
	}
	print(“function end”)
}

doSomething { print(“closure”) }

#=> prints function start
#=> prints function end
#=> prints closure
{% endhighlight %}

근데 이 **escaping 클로저**를 사용할 경우 주의해야할 점이 하나 있는데, **메모리 관리**와 관련된 부분!!!<br/><br/>

예를 들어, 만약 함수가 종료된 후 클로저를 실행하는데, 이때 클로저가 함수 내부 값을 사용함. 그럼 이때 함수는 이미 종료 되었는데, **클로저는 함수 내부 값을 어떻게 사용할까?**<br/><br/>

이런 메모리 관련 부분!! 클로저-3 에서….

## 5. 마무리
이번편에서는 트레일링 클로저를 비롯한 클로저 경량화, `@escaping`문법을 배웠다.<br/><br/>
경량화 문법은 수도 없이 클로저를 쓰면서도 매번 다르게 쓰면서 원리를 전혀 알지 못했는데 이번을 계기로 코드를 보고 사용하는데 훨씬 유용할것 같다.<br/><br/>
그리고 특히 `@escaping`키워드는 뭔지도 모르고 사용해왔다. 내가 응용해서 사용하는것도 아니고 특정 코드(특히 비동기 처리가 있는 코드들)를 가져올때 그 코드에 있었기 때문에 사용해왔는데 확실하게 함수의 정의와 `@escaping`키워드를 이해하게 되었다.<br/><br/>
덕분에 앞으로 함수, 클로저코드를 보고 사용할 때 따라치는게 아닌 이해하고 응용할 수 있을것 같다!

<br/><br/><br/><br/>
참고 : 개발자 소들이, Swift) 클로저(Closure) 정복하기(2/3) - 문법 경량화/@escaping/@autoclosure<br/>
(<https://babbab2.tistory.com/82?category=828998>)<br/>
