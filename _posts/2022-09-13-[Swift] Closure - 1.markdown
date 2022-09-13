---
title: "[Swift] 클로저 - 1"
excerpt:
categories:
    - Swift
tags:
    - Closure
toc: true
last_modified_at: 2022-09-13-22:22:00 +0900
---
## 1. 클로저도 함수
클로저도 함수이기 때문에 일급 객체 함수의 특성을 모두 가지고 있다.<br/><br/>
클로저 또한 자료형을 갖고 있다.<br/>
<br/>

## 2. 클로저 표현식
{% highlight swift %}
  { (Parameters) -> ReturnType in
    //실행구문
  }
{% endhighlight %}

클로저는 Unnamed function인 만큼, `func` 이란 키워드를 사용하지 않음.<br/><br/>
그리고 클로저는 다음과 같이 **클로저 헤드**와 **클로저 바디**로 이루어져 있음.<br/><br/>
이 둘을 구분지어주는게 바로 `in` 키워드

<br/>
<img width="507" alt="스크린샷 2022-09-13 오후 9 44 21" src="https://user-images.githubusercontent.com/83946704/189904410-f20bdfa0-12e1-41af-9011-2112d4a1b455.png">

### 2-1. Parameter와 Return Type이 둘 다 없는 클로저
클로저는 함수이고 함수는 Swift에서 일급 객체이기 때문에, 상수에 클로저를 대입할 수 있음.<br/><br/>
특히 **Parameter**와 **Return Type**이 둘 다 없는 경우는 다음과 같이 사용함.<br/><br/>
{% highlight swift %}
let closure = { () -> () in
	print(“Closure”)
}
{% endhighlight %}
클로저 또한 함수처럼 **Return Type이 없으면 생략 가능!** 심지어 **Return Type이 있더라도 생략 가능!**<br/><br/>
함수에서는 안되는 **Parameter조차 생략 가능!!!**

### 2-2. Parameter와 Return Type이 있는 클로저
{% highlight swift %}
let closure = { (name: String) -> String in
	return “Hello, \(name)”
}
{% endhighlight %}
함수와 비슷해 보여 어렵지 않지만 주의해야할 것 하나!<br/><br/>
<U>Parameter의 name은 단독으로 쓰였으니, Argument Label이자 Parameter Name이라 생각…은 안된다!!</U><br/><br/>
**클로저에선 Argument Label을 사용하지 않음**
<br/><br/>
따라서 `name`은 **Argument Label이 아니고 오직 Parameter Name!**
<br/><br/>


## 3. 1급 객체로서 클로저
1급 객체 함수의 특징을 클로저에 대입시켜보자.<br/><br/>

**(1) 클로저를 변수나 상수에 대입할 수 있다.**<br/><br/>
클로저 또한 변수나 상수에 대입 할 수 있고, 이 대입된 변수나 상수로 실행할 수도 있음.

{% highlight swift %}
let closure = { () -> () in
	print(“Closure”)
}

let closure2 = closure
{% endhighlight %}
이런식으로 **대입과 동시에 클로저를 작성**할 수도 있고, 기존에 클로저를 대입한 변수나 상수를 새로운 변수나 상수에 대입할 수도 있음!<br/><br/>

**(2) 함수의 파라미터 타입으로 클로저를 전달할 수 있다.**<br/>
{% highlight swift %}
func doSomething(closure: () -> ()) {
	closure()
}
{% endhighlight %}

함수를 파라미터로 전달받는 doSomething이라는 함수가 있음. 이 경우, 파라미터로 함수를 넘겨줘도 되지만

{% highlight swift %}
doSomething(closure: { () -> () in
	print(“Hello!”)
})

#=> Hello!
{% endhighlight %}

이렇게 **클로저**를 넘겨줘도 됨!<br/><br/>
헷갈릴 수 있는데 함수 호출을 다음과 같이 생각해보면 됨<br/><br/>
<img width="498" alt="스크린샷 2022-07-11 오전 4 52 05" src="https://user-images.githubusercontent.com/83946704/189906984-c432fb26-6e0a-4fec-9be2-ce5450ec0bd4.png"><br/><br/>
초록색 영역이 클로저로 작성된 부분이고, 이것이 `closure`란 Argument Label의 **Parameter로 전달**된 것임!!
<br/><br/>

**(3) 함수의 반환 타입으로 클로저를 사용할 수 있다.**<br/><br/>
선언부는 기존 함수와 똑같음.
{% highlight swift %}
func doSomething() -> () -> () {
}
{% endhighlight %}
<br/>
그러나 **실제 값을 return할 때 함수가 아닌**
<br/>
{% highlight swift %}
func doSomething() -> () -> () {
	return { () -> () in
		print(“Hello Curry!”)
	}
}
{% endhighlight %}
<br/>
이렇게 **클로저를 리턴**할 수 있음
<br/>
{% highlight swift %}
let closure = doSomething()
closure()
//이렇게도 가능 ㅋㅋ
doSomething()()

#=> prints Hello Curry!
{% endhighlight %}
<br/>
또한 호출하는 곳에서 클로저를 받아서 다음과 같이 실행시킬 수 있음.
<br/>

## 4. 클로저 실행하기
클로저를 실행할 수 있는 방법은 두가지가 있음.

### 4-1. 클로저가 대입된 변수나 상수로 호출하기

{% highlight swift %}
let closure = { () -> String in
	return “Hello Curry!”
}
print(closure())

#=> prints Hello Curry!
{% endhighlight %}

이런 식으로 클로저가 대입된 상수 closure를 **호출 구문인 ()**를 이용해서 실행시킬 수 있음.

### 4-2. 클로저를 직접 실행하기
클로저를 변수나 상수에 대입하지 않고 실행시키고 싶다면, (완벽한 일회성) 그땐 **클로저를 ( ) 소괄호로 감싸고, 마지막에 호출 구문인 ()**를 추가해주면 됨.

{% highlight swift %}
({ () -> () in
	print(“Hello Curry!”)
})()

#=> prints Hello Curry!
{% endhighlight %}

이런 식으로 클로저가 대입된 상수 closure를 **호출 구문인 ()**를 이용해서 실행시킬 수 있음.

## 5. 마무리
애플 디벨로퍼 아카데미에서 처음 팀으로 만난 팀원들과 공부 스터디를 하면서 클로저를 비롯한 다양한 Swift 기본 문법들을 공부하였다.<br/><br/>
이전엔 코드를 따라 치기만 하고 사용하는 방법만 알았다면 지금은 코드를 보고 '아 이렇기 때문에 이런 코드가 작성됬구나...'라고 생각하는데 도움이 많이 되었다.<br/><br/>
하지만 아직도 부족한 부분이 많고 다시 옮겨담아야 할 포스팅도 많기 때문에 다 옮겨 적을때까지 정신 차리고 포스팅을 이어나가야겠다...(ㅠㅠ)<br/><br/>

<br/><br/><br/><br/>
참고 : 개발자 소들이, Swift) 클로저(Closure) 정복하기(1/3) - 클로저, 누구냐 넌<br/>
(<https://babbab2.tistory.com/81?category=828998>)<br/>
