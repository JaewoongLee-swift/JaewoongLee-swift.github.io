---
title: "[자료구조&알고리즘] LinkedList"
excerpt:
categories:
    - 자료구조&알고리즘
tags:
    - linkedList
    - Array

toc: true
last_modified_at: 2023-01-17-17:41:00 +0900
---
## 0. 서론
면접질문 중,
>'Array와 List의 차이는 무엇인가요?'

제대로 대답하지 못했다. 쉬운거라 생각하고 쉽게 넘어간 죄...<br/><br/>
확실하게 개념과 구현방법을 알고가자!

## 1. Array와 List의 차이

### 1-1. Array
- 데이터가 많아지면 그룹 관리의 필요성이 생긴다. 이때 프로그래밍에서 사용하는 것이 배열(Array)
- 여러 데이터를 하나의 이름으로 **그룹핑해서 관리하기 위한 자료구조**
- 배열을 이용하면 하나의 변수에 여러 정보를 담을 수 있고, 반복문과 결합하면 많은 정보도 효율적으로 처리할 수 잇음
- 배열 인덱스는 값에 대한 **유일무이한 식별자**(참고로 리스트에서 인덱스는 몇번째 데이터인가 정도의 의미를 가짐)
- 배열은 연속된 물리적 메모리 공간에 데이터를 저장함
- 새로운 값을 삽입하려면 삽입하고자 하는 위치보다 뒤에 있는 원소들을 한 칸씩 뒤로 밀어주어 삽입할 공간을 확보해주어야 함
- 중간에 있는 값을 삭제하려면 뒤에 이어지는 원소들을 한 칸식 앞으로 당겨서 **삭제된 빈 공간을 채워 주어야 하는 번거로움**이 있음 (-> 재배치에 오버헤드가 발생)
- 인덱스를 통해 데이터에 접근하는 시간 복잡도는 O(1) (매우빠름)

장점 : 인덱스 값을 미리 알고 있는 경우, 데이터에 신속한 접근 가능. 새로운 요소 삽입이 빠름<br/>
단점 : 크기가 고정됨, 삭제 및 검색이 느림<br/>

### 1-2. LinkedList
- 이중 연결 리스트, 순환 연결 리스트 등 다양한 종류가 있음
- 일반적으로는 **단순 연결 리스트**로, 노드들이 자신 다음에 이어질 값의 주소 값을 가지고 있는 형태
- 원하는 때에 메모리에 공간을 할당해서 씀
- 중간 element를 삽입, 삭제 시 **재배치에 발생하는 오버헤드도 발생하지 않음**
- 단방향 연결 리스트의 경우, 데이터에 접근하려면 첫번째 데이터부터 원하는 데이터까지 순차적으로 찾아야 하기 때문에 **데이터 접근속도가 느림**
- 내 다음 데이터에 대한 연결정보를 저장하는 별도의 데이터 공간이 필요해 저장 공간의 효율이 높지 않음

장점 : 데이터 삽입 및 삭제 속도가 빠름<br/>
단점 : 검색 속도가 느림, 저장공간 효율이 높지 않음<br/>


## 2. LinkedList in Swift
List 중 단순 연결 리스트를 Swift로 구현해보았다.

{% highlight swift %}
class Node<T> {
    var data: T?
    var next: Node?

    init(data: T? = nil, next: Node? = nil) {
        self.data = data
        self.next = next
    }
}

struct LinkedList<T: Equatable> {
    private var head: Node<T>?

    ...
}
{% endhighlight %}

단순 연결 리스트에 필요한 기능들은 다음과 같다.
- `size()` : 연결리스트의 크기를 구한다
- `searchNode(at:)` : 특정 위치에 있는 노드를 반환 (찾고자 하는 위치가 리스트의 크기보다 클 경우 nil 반환)
- `appned(_:)` : 연결리스트의 맨 뒤에 새로운 노드 추가
- `insert(_:at:)` : 특정 위치에 새로운 노드를 삽입 (삽입하고자 하는 위치가 리스트의 크기보다 클 경우 맨 뒤에 추가)
- `remove(at:)` : 특정 위치에 있는 노드를 삭제 (삭제하고자 하는 위치가 리스트의 크기보다 클 경우 아무 동작하지 않음)
- `removeLast()` : 마지막에 있에 노드를 삭제 (삭제하고자 하는 위치가 리스트의 크기보다 클 경우 아무 동작하지 않음)
- `contains(_:)` : 특정 값을 가지는 노드를 포함하고 있는지 탐색하여 Bool 값을 반환
- `firstIndex(of:)` : 특정 값을 가지는 노드의 인덱스(Int?) 값을 반환

`size()` 구현
{% highlight swift %}
func size() -> Int {
    if head == nil { return 0 }

    var node = head
    var count = 1
    while node?.next != nil {
        node = node?.next
        count += 1
    }

    return count
}
{% endhighlight %}

`searchNode(at:)` 구현
{% highlight swift %}
func searchNode(from data: T?) -> Node<T>? {
    if head == nil { return nil }

    var node = head
    while node?.next != nil {
        if node?.data == data { break }
        node = node?.next
    }

    return node
}
{% endhighlight %}

`appned(_:)` 구현
{% highlight swift %}
//struct 내부의 변수를 수정하기 위해선 mutating 키워드 필요
mutating func append(_ data: T?) {
//head가 없는 경우 Node를 생성 후 head로 지정해준다
    if head == nil {
        head = Node(data: data)
        return
    }

    var node = head
    while node?.next != nil {
        node = node?.next
    }

    node?.next = Node(data: data)
}
{% endhighlight %}

`insert(_:at:)` 구현
{% highlight swift %}
mutating func insert(data: T?, at index: Int) {
    if head == nil {
        head = Node(data: data)
        return
    }

    if index == 0 {
        let nextNode = head
        head = Node(data: data)
        head?.next = nextNode
        return
    }

    var node = head
    for _ in 0..<(index - 1) {
        if node?.next == nil { break }
        node = node?.next
    }

    let nextNode = node?.next
    node?.next = Node(data: data)
    node?.next?.next = nextNode
}
{% endhighlight %}

`remove(at:)` 구현
{% highlight swift %}
mutating func remove(at index: Int) {
    if head == nil { return }

    if index == 0 || head?.next == nil {
        head = head?.next
        return
    }

    var node = head
    for _ in 0..<(index - 1) {
        if node?.next?.next == nil { break }
        node = node?.next
    }

    node?.next = node?.next?.next
}
{% endhighlight %}

`removeLast()` 구현
{% highlight swift %}
mutating func removeLast() {
    if head == nil { return }

    if head?.next == nil {
        head = nil
        return
    }

    var node = head

    while node?.next?.next != nil {
        node = node?.next
    }

    node?.next = nil
}
{% endhighlight %}

`contains(_:)` 구현
{% highlight swift %}
func contains(_ data: T?) -> Bool {
    if head == nil { return false }

    if head?.data == data { return true }

    var node = head
    var isContained = false
    while node?.next != nil {
        if node?.next?.data == data {
            isContained = true
            break
        }
        node = node?.next
    }

    return isContained
}
{% endhighlight %}

`firstIndex(of:)` 구현
{% highlight swift %}
func firstIndex(of data: T?) -> Int? {
    if head == nil { return nil }

    if head?.data == data { return 0 }

    var node = head
    var index: Int? = nil
    var count = 1
    while node?.next != nil {
        if node?.next?.data == data {
            index = count
            break
        }
        node = node?.next
        count += 1
    }

    return index
}
{% endhighlight %}


## 3. 마무리
면접을 경험하고 나니 내가 얼마나 자료구조, 알고리즘과 CS 지식에 소홀히 했는지 뼈저리게 느꼈다.~~(iOS 지식도 헷갈린게 함정)~~<br/><br/>
많이 부족하다고 느꼈지만 급하지 말고 차근차근 알아간다 생각하면서 다시 하나씩 쌓아보자!
<br/><br/><br/><br/>
참고 : 소들이, Swift) 단방향 연결 리스트(LinkedList) 구현 해보기
(<https://babbab2.tistory.com/86>)<br/>
참고 : _슈프림, [Linked List 연결리스트] 배열과의 차이점 / 그리고 Swift로 구현 해보기
(<https://tngusmiso.tistory.com/20>)<br/>
