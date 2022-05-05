---
title: "[Swift] Subscript"
excerpt:
categories:
    - Swift
tags:
    - Subscript
toc: true
last_modified_at: 2022-05-06-03:41:00 +0900
---
## 1. Subscript란?
Subscript란 class, struct, enum에서(collection, array 등등) 각 sequence의 요소에 접근하기 위한 단축키이다.<br/>
<br/>
Subscript를 사용하여 설정 및 검색을 위한 별도의 메서드 없이 인덱스 별로 값을 설정하고 검색할 수 있다.<br/>
<br/>
설명으로는 이해가 잘 안되니 예시를 통해 보도록 하자.<br/>
<br/>
ex)<br/>
Array 인스턴스의 element를 `someArray[index]` 로 액세스<br/>
Dictionary 인스턴스의 element를 `someDictionary[key]`로 액세스<br/>
<br/>
이때 사용하는 대괄호 []가 Subscript인 것이다.


## 1. Subscript 정의
Subscript의 정의방식은 Computed Property와 비슷하다.<br/>
<br/>
`subscript` 키워드로 Subscript의 정의를 작성하고 인스턴스 메서드와 같은 방식으로 하나 이상의 입력 파라미터와 반환 타입을 지정한다.<br/>
<br/>
인스턴스 메서드와 달리, Subscript는 get-set 또는 get-only(읽기전용)일 수 있다.<br/>
<br/>
이러한 동작방식은 Computed Property와 같은 방식으로 getter와 setter에 의해 전달된다.<br/>
<br/>

{% highlight swift %}

subscript(index: Int) -> Int {
	get {
	// Return an appropriate subscript value here.
	}
	set(newValue) {
	// Perform a suitable setting action here.
	}
}

{% endhighlight %}

newValue의 타입은 Subscript의 반환 값과 동일하다.<br/>
<br/>
Computed Property와 마찬가지로, setter의 (newValue) 매개변수를 지정하지 않도록 선택할 수 있다.<br/>
<br/>
직접 변수를 설정하지 않으면 newValue라는 기본 매개 변수가 setter에게 제공된다.

## 2. Subscript 사용

{% highlight swift %}
extension Int {
    subscript(multipleNumber: Int) -> Int {
        get {
            return multipleNumber * self
        }
    }
}

let multipledNumber = 3[3]
print(multipledNumber)

#=> prints 9

{% endhighlight %}

다음과 같이 Subscript를 Int의 extension에 정의해주었다.<br/>
<br/>
Subscript를 통해 Int타입 값을 넣어주면 원래 값과 Subscript를 통해 넣은 값을 곱해서 반환한다.<br/>
<br/>
물론 Computed Property와 동일하게 get-only일 경우 더욱 간단하게 선언할 수 있다.

{% highlight swift %}
extension Int {
    subscript(multipleNumber: Int) -> Int {
        return multipleNumber * self
    }
}

{% endhighlight %}

아래의 예시와 같이 Dictionary에서도 사용 가능하다.

{% highlight swift %}
var nameDictionary = ["Curry" : 1, "YungSix" : 2, "Neis" : 3]

// getter에 접근
if let curryValue = nameDictionary["Curry"] {
    print(curryValue)
}

// setter에 접근
nameDictionary["Rang"] = 4
print(nameDictionary)

#=> prints ["Rang": 4, "Neis": 3, "Curry": 1, "YungSix": 2]
{% endhighlight %}


## 3. Subscript의 옵션
Subscript는 함수와 같이 하나 이상의 파라미터를 사용할 수 있다.<br/>
<br/>
주로 하나만 사용하긴 하지만, 필요하다면 여러개의 파라미터를 사용하면 된다.

{% highlight swift %}
struct Matrix {
    let rows: Int, columns: Int
    var grid: [Double]

    init(rows: Int, columns: Int) {
        self.rows = rows
        self.columns = columns
        grid = Array(repeating: 0.0, count: rows * columns)
    }

    func indexIsValid(row: Int, column: Int) -> Bool {
        // 인덱스니까 최대 rows, columns보다 1씩 낮아야함
        return row >= 0 && row < rows && column >= 0 && column < columns
    }

    subscript(row: Int, column: Int) -> Double {
        get {
            assert(indexIsValid(row: row, column: column), "Index out of range임!")
            return grid[(row * columns) + column]
        }

        set {
            assert(indexIsValid(row: row, column: column), "Index out of range임!")
            grid[(row * columns) + column] = newValue
        }
    }
}

var matrix = Matrix(rows: 2, columns: 2)

matrix[0, 0] = 1
matrix[0, 1] = 2
matrix[1, 0] = 3
matrix[1, 1] = 4

#=> prints Matrix(rows: 2, columns: 2, grid: [1.0, 2.0, 3.0, 4.0])
{% endhighlight %}

## 4. Type Subscript
위에서 설명한 바와 같이 인스턴스 Subscript는 특정한 타입의 인스턴스에서 호출하는 Subscript이다.<br/>
<br/>
하지만 타입 자체에서 호출되는 Subscript를 정의할 수도 있다.<br/>
<br/>
이런 종류의 Subscript는 Type Subscript라고 불린다.<br/>
<br/>
Subscript 앞에 `static` 키워드를 작성하여 Type Subscript를 나타낸다.<br/>
<br/>
클래스는 대신 `class` 키워드를 사용하여 서브클래스가 슈퍼클래스의 해당 Subscript 구현을 다시 정의할 수 있도록 만들준다.<br/>
<br/>
아래의 예시를 통해 Type Subscript를 정의하고 호출하는 방법을 보도록 하자.

{% highlight swift %}
enum Planet: Int {
	case mercury = 1, venus, earth, mars, jupiter, saturn, uranus, neptune

	static subscript(n: Int) -> Planet {
		return Planet(rawValue: n)!
	}
}
let mars = Planet[4]
print(mars)

#=> prints mars
{% endhighlight %}

## 5. 마무리
Swift를 사용하다 자주 보이던 Subscript라는 키워드...<br/>
<br/>
항상 보일때마다 '나중에 공부해야지' 라고 생각해놓고 미뤄두었는데 이번을 계기로 확실하게 개념과 사용방법을 익힌것 같다.<br/>
<br/>
그러니 앞으로도 모르는거 있으면 미리미리 하자!!

<br/><br/><br/><br/>
출처 : Swift 공식문서, Subscript<br/>
(<https://docs.swift.org/swift-book/LanguageGuide/Subscripts.html>)<br/>
참고 : 개발자 소들이, Swift) 서브스크립트(Subscript) 정복하기<br/>
(<https://babbab2.tistory.com/123>)
