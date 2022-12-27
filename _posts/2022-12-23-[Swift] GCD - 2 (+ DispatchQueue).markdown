---
title: "[Swift] GCD - 2 (+ DispatchQueue)"
excerpt:
categories:
    - Swift
tags:
    - GCD
    - Thread
    - OperationQueue

toc: true
last_modified_at: 2022-12-23-17:47:00 +0900
---
## 0. 서론
지난 [GCD - 1] 글에서 GCD는 무엇이고 왜 생겨났는지 알아보았다.<br/><br/>
이어서 이번엔 GCD가 어떠한 특징을 가지고 있고 DispatchQueue는 어떻게 이루어져 있는지 알아보자!

## 1. GCD의 특징
GCD는 다음과 같은 특징을 가지고 있다.

- 앱의 메모리 공간에 스레드 스택을 저장하면 메모리 페널티를 줄여준다.
- 스레드를 만들고 구성하는 데 필요한 코드를 제거해준다.
- 스레드 작업을 관리하고 예약하는 데 필요한 코드를 제거한다.
- 코드를 단순화시킬 수 있다.

GCD는 어플리케이션이 Block(Swift에선 클로저) 객체 형태로 작업을 전송할 수 있는 `FIFO 대기열(Queue)`을 제공하고 관리한다.<br/><br/>
Queue에 전달된 작업은 시스템이 전적으로 관리하는 `쓰레드 풀(a pool of threads)에서 실행`된다.

~~(한 단락으로 빼놓았는데 너무 짧아서 당황)~~

## 2. DispatchQueue
GCD의 특징까지 알아보았으니 이제 DispatchQueue는 어떻게 구성되어 있고 어떻게 사용하면 되는지 알아보자!<br/><br/>

일단, DispatchQueue는 **2개의 타입(Serial, Concurrent)**으로 구분된다.<br/><br/>
앱을 실행하면 시스템이 자동으로 메인 쓰레드 위에서 동작하는 **Main Queue(Serial Queue)**를 만들어서 작업을 수행하고, 그 외에 추가적으로 여러개의 **Global Queue(Concurrent Queue)**를 만들어서 큐를 관리한다.<br/><br/>
각 작업들은 **동기(sync)** 방식과 **비동기(async)** 방식으로 실행 가능하지만, Main Queue에서는 async만 사용 가능하다.<br/><br/>
Main Queue를 sync 방식으로 동작시키면 [데드락(deadlock)] 상태에 빠지게 된다.

- Serial Queue : 큐에 추가된 순서대 한번에 하나의 task를 실행한다.

<img width="923" alt="Serial" src="https://user-images.githubusercontent.com/83946704/209284749-d269632b-10ca-4487-a130-6669de957877.png">

- Concurrent Queue : 동시에 하나 이상의 task를 실행하지만, task는 queue에 추가된 순서대로 시작한다.

<img width="923" alt="Concurrent" src="https://user-images.githubusercontent.com/83946704/209284766-94892459-d0d0-4f7a-aa64-c88135fc0965.png">
<br/>
Global Queue의 경우에는 **QoS**클래스를 지정해 우선순위 설정이 가능하다.

## 2-1. QoS (Quality of Service)
하나를 알면 하나가 나온다. QoS는 그렇다면 무엇일까?

> 작업 실행의 우선 순위를 지정하는 서비스 품질 클래스.
(Apple 공식문서)

> 사용자는 QoS를 사용하여 앱이 수행하는 작업의 의도를 전달할 수 있어 사용 가능한 리소스가 주어진 task를 실행하는 가장 좋은 방법을 결정하도록 한다. 예를 들어, 시스템은 user-interactive task가 포함된 스레드에 더 높은 우선 순위를 부여하여 해당 작업이 빠르게 실행되도록 한다. 반대로, background task에는 낮은 우선 순위를 부여하며, 효율적인 CPU 코어에서 실행하여 전력을 절약하려고 시도한다. 이처럼 시스템은 시스템 조건과 사용자가 스케줄링한 task에 따라 task를 동적으로 실행하는 방법을 결정한다.

Apple 공식문서의 설명과 같이, 사용자는 QoS를 통해 스케줄링, CPU 및 I/O 처리량, 타이머 대기 시간 등의 우선순위를 조정할 수 있다.<br/><br/>

QoS의 종류는 아래와 같다.<br/>
- User interactive
  - **즉각 반응해야 하는 작업**으로, 반응성 및 성능에 중점을 둔다.
  - main thread에서 동작하는 인터페이스 새로고침, 애니메이션 작업 등 **즉각 수행되는 유저와의 상호작용 작업** 할당한다.
- User Initiated
  - **몇 초 이내의 짧은 시간 내 수행해야하는 작업**으로, 반응성 및 성능에 중점을 둔다.
  - 문서(또는 PDF)를 열거나 버튼을 터치해 액션을 수행하는 것 처럼 **빠른 결과를 요구하는 유저와의 상호작용 작업**에 할당한다.
- Utility
  - **수초 ~ 수분에 걸쳐 걸쳐 수행되는 작업**으로 반응성, 성능, 에너지 효울성 간에 균형을 유지하는데 중점을 둔다.
  - 데이터를 읽어들이거나 다운로드 하는 등 작업을 완료하는데 어느 정도 시간이 걸릴 수 있으며 보통 진행 표시줄로 표현한다.
  - 주의 : 저전력 모드에서는 네트워킹을 포함하여 백그라운드 작업은 일시중지된다.
- background
  - 앱이 **색인 생성, 동기화, 백업 같이 사용자가 볼 수 없는 작업**에 할당한다.
  - **수분 ~ 수시간처럼 상당한 시간이 걸리는 작업**같은  백그라운드에서 실행되는 동안 작업을 수행하는 데 사용하는 작업으로 에너지 효율성에 중점을 둔다.
  - background는 모든 작업 중 우선순위가 가장 낮다.

다음은 기본 QoS 클래스 이외의 특수 타입의 QoS이다.
- Default
  - QoS를 별도로 지정하지 않으면 **기본값으로 사용**되는 형태이며 User Initiated와 Utility의 중간 레벨이다.
  - GCD Global Queue의 기본 동작 형태
- Unspecified
  - QoS 정보가 없으므로 시스템이 QoS를 추론해야 한다는 것을 의미한다.


## 3. 마무리
원래는 코드로 테스트도 해보고 예시까지 확인하면서 포스팅을 하려했는데 생각보다 길이 글어지고 집중도 많이 안되서 여기까지 작성했다.(금요일이라 그런가...!)<br/><br/>
다음 포스팅에선 GCD 3편!! (몇펀까지 될련지 ㅠㅠ) DispatchQueue를 커스텀하기 위해서 설정하는 label과 attributes 파라미터를 알아보고 여러 테스트들을 통해서 어떻게 사용되는지 알아보자!<br/><br/>


<br/><br/><br/><br/>
참고 : 해콩, GCD (Grand Central Dispatch)
(<https://beenii.tistory.com/155>)<br/>
참고 : hanni66, iOS - GCD API 동작 방식과 필요성(2)
(<https://velog.io/@hanni66/iOS-GCD-API-동작-방식과-필요성2>)<br/>
참고 : jinShine, [iOS] GCD(Grand Central Dispatch)
(<https://jinshine.github.io/2018/07/09/iOS/GCD(Grand%20Central%20Dispatch)/#dispatchqueue-1>)<br/>
참고 : Apple Documentation, DispatchQueue
(<https://developer.apple.com/documentation/dispatch/dispatchqueue>)<br/>
참고 : Zedd0202, iOS ) Prioritize Work with Quality of Service Classes
(<https://zeddios.tistory.com/521>)<br/>

[GCD - 1]: https://jaewoonglee-swift.github.io/swift/Swift-GCD-1-(+-Thread,-OperationQueue)/
[데드락(deadlock)]: https://ko.wikipedia.org/wiki/교착_상태
