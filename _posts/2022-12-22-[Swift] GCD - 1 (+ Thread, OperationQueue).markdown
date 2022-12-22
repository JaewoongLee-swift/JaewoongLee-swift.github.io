---
title: "[Swift] GCD - 1 (+ Thread, OperationQueue)"
excerpt:
categories:
    - Swift
tags:
    - GCD
    - Thread
    - OperationQueue

toc: true
last_modified_at: 2022-12-22-23:17:00 +0900
---
## 0. 서론
지난 게시글 중, 애플 디벨로퍼 아카데미의 멘토, Young에게 개인 프로젝트에 대한 간단한 코멘트를 받던 중, 멀티쓰레드 관리에 대한 코멘트트를 받았었다.<br/><br/>
_'멀티 쓰레드... 관리...?'_<br/><br/>
어지럽다. GCD와 DispatchQueue에 대해서 정말 간단하게만 보고 지나갔는데, 이번에는 확실하게 Swift와 Apple이 어떻게 쓰레드 관리를 할 수 있도록 제공하고 어떤식으로 사용해야 하는지 알아가겠다.<br/><br/>

## 1. GCD가 만들어진 이유
### 1-1. 프로세서 성능개선의 방식 변화
과거에는 CPU의 성능을 늘리기 위해 마이크로 프로세서의 clock 속도를 높이는 방식으로 연산 속도를 높였다.<br/><br/>
그 후 점점 CPU의 전력 소비와 발열로 인해 단일 프로세서의 clock 속도 증가는 한계를 맞게 되었고 특히 모바일기기에서 큰 영향을 받게 되었다.<br/><br/>
따라서 CPU 제조사들은 단일 CPU의 클럭 속도를 개선하는 대신 여러개의 CPU를 탑재하는 형태로 효율을 높이는 것에 초점을 맞추게 되었고, 프로세서의 clock 속도가 빨라지면서 자연스럽게 소프트웨어도 같이 빨라지던 과거와 달리 멀티코어 프로세서에서는 멀티 프로세서에게 어떻게 잘 프로그램 동작을 배분하는지가 더욱 중요하게 되었다.

### 1-2. 멀티 프로세서에 잘 적응하기 위한 Apple의 방식
위에 이유 때문에 멀티쓰레드를 잘 관리하는게 중요해졌다. <br/><br/>
GCD가 만들어지기 이전에는 멀티 쓰레드 관리를 위해 Thread와 OperationQueue 등의 클래스를 사용했는데, Thread는 복잡하고 [Critical Section(임계구역)]을 이용한 Lock 관리가 힘들어졌다. 그리고 OperationQueue는 GCD에 비해 무겁고 [Boiler Plate]들이 많이 필요하다는 문제가 있었다. <br/><br/><br/>

그래서 내놓은것이 바로 **GCD**!!<br/><br/><br/>

**GCD(Grand Central Dispatch)**는 멀티 코어 프로세서 시스템에 대한 응용 프로그램 지원을 최적화하기 위해 Apple에서 개발한 기술로, 스레드 관리와 실행에 대한 책임을 애플리케이션 레벨에서 운영체제 레벨로 넘겨버린다.<br/><br/>
GCD의 작업단위는 Block(Swift에서는 클로저)라고 불리며, DispatchQueue가 이 Block들을 관리한다.<br/><br/>

GCD는 각 어플리케이션에서 생성된 DispatchQueue를 읽는 멀티 코어 실행 엔진을 가지고 있으며, Queue에 등록된 각 작업을 꺼내 스레드에 할당하고 개발자는 내부 동작을 자세하게 알 필요 없이 Queue에 딱 작업만 넘기면 된다.<br/><br/>
Thread를 직접 생성하고, 관리하는 것에 비해 작업만 넘기면 되니 관리 용이성과 이식성, 성능이 증가하게 되었다.<br/><br/>


## 2. Thread와 OperationQueue
GCD가 왜 개발되고 어떤점이 좋은지 알아봤으니 이전에 사용되었던 Swift의 Thread와 OperationQueue 클래스에 대한 설명만 간단히 알아보자.

### 2-1. Thread
Thread는 쓰레드를 실행하기 위한 클래스이다. <br/><br/>

1. Thread는 긴 작업을 수행해야 하지만 나머지 애플리케이션의 실행을 차단(block)하고 싶지 않을 때 특히 유용하다. (특히, Thread를 사용하여 사용자 인터페이스와 이벤트 관련 작업을 처리하는 애플리케이션의 메인 스레드를 차단하지 않도록 할 수 있다)
2. 스레드는 또한 큰 작업을 여러 개의 작은 작업으로 나누는 데 사용될 수 있으며, 이는 멀티코어 컴퓨터에서 성능을 향상시킬 수 있다.
<br/><br/>

Thread 클래스는 스레드의 런타임 상태를 모니터링하기 위한 작업을 지원한다.<br/><br/>
Thread의 실행을 취소하거나 Thread가 여전히 실행 중이거나 작업을 완료했는지 확인할 수 있고 Thread를 취소하려면 Thread 코드의 지원이 필요하다. (자세한 내용은 cancel() 설명을 참고)<br/><br/><br/>

(Thread는 시작, 멈춤, 실행상태 결정, Main Thread 작업, 우선순위 등의 작업을 직접 컨트롤 할 수 있다. 진짜 복잡...)

## 2-2. OperationQueue
OperationQueue는 이름 그대로 대기열(Queue)에 작업실행(Operation)을 관리하는 추상 클래스이다. <br/><br/>

1. Operation : single task에 관한 데이터와 코드를 나타내는 추상 클래스. 해당 클래스를 서브클래싱하여 사용하면 안정적으로 task를 실행시킬 수 있는 효과가 있다.
2. OperationQueue : 위의 Operation을 우선순위에 맞춰 실행시킴. 한번 operation queue에 Opertaion 객체를 담아놓으면 task가 끝날때까지 queue가 없어지지 않고 존재하며 해당 queue에서 Operation들을 삭제하거나 명령시킬 수 있는 장점이 존재함 (task관리에 이점)
<br/><br/>

위를 통한 OperationQueue의 핵심은 다음과 같다.
- Operation을 서브클래싱한 것은 기능을 캡슐화 했다는 의미이므로, 기능에 관한 모듈성 향상
- task의 실행, 정지, 대기와 같은 실행 상태를 알 수 있고 이를 통해 Operation들을 취소, 순서 지정이 가능
- 진행 상황과 종속성을 추적하면서 여러 클래스에 대한 책임을 분리할 수 있는 방법

## 3. 마무리
진짜 어렵다. 내용을 파고들면서 내가 원하는 멀티쓰레드 관리에 들어가기 전에 모르는 내용들이 너무 많아서 하나하나 알아가기 정말 바쁘다.<br/><br/>
GCD와 DispatchQueue에 대해 좀 알아보겠거니 싶었지만 Thread와 OperationQueue가 나오고, 다음엔 더 많은게 기다리고 있다...<br/><br/>
오늘은 이정도만 알아보고 내일은 더 자세하게 GCD와 DispatchQueue에 대해 알아보도록 하자.

<br/><br/><br/><br/>
참고 : 해콩, GCD (Grand Central Dispatch)
(<https://beenii.tistory.com/155>)<br/>
참고 : hanni66, iOS - GCD API 동작 방식과 필요성(2)
(<https://velog.io/@hanni66/iOS-GCD-API-동작-방식과-필요성2>)<br/>
참고 : 위키백과, 임계구역
(<https://ko.wikipedia.org/wiki/임계_구역>)<br/>
참고 : Charlezz, 보일러플레이트 코드란?(Boilerplate code)
(<https://charlezz.medium.com/보일러플레이트-코드란-boilerplate-code-83009a8d3297>)<br/>
참고 : [Swift] Thread
(<https://velog.io/@bibi6666667/Swift-Thread>)<br/>
참고 : Apple Documentation, Thread
(<https://developer.apple.com/documentation/foundation/thread>)<br/>
참고 : jake-kim, [iOS - swift] Operation, OperationQueue, 동시성
(<https://ios-development.tistory.com/799>)<br/>
참고 : 국산앨런, [iOS] OperationQueue 톺아보기
(<https://k-elon.tistory.com/21>)<br/>
참고 : Apple Documentation, OperationQueue
(<https://developer.apple.com/documentation/foundation/operationqueue>)<br/>


[Critical Section(임계구역)]: https://ko.wikipedia.org/wiki/임계_구역
[Boiler Plate]: https://charlezz.medium.com/보일러플레이트-코드란-boilerplate-code-83009a8d3297
