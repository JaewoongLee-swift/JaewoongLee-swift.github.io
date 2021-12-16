---
title: "[iOS] actionSheet 사용하기"
excerpt:
categories:
    - iOS
tags:
    - swift
    - iOS
    - actionSheet
toc: true
last_modified_at: 2021-12-15-17:50:00 +0900
---

## 1. 더보기(또는 기타설정) 버튼을 만들 때
인스타그램 유사 앱의 프로필 화면을 구현하면서 네이게이션 바 우측버튼으로 더보기 버튼을 만들었다.<br/>
<br/>
<center><img width="379" alt="더보기버튼업로드" src="https://user-images.githubusercontent.com/83946704/146152896-c83c05c9-30fb-43e4-9aac-13c3d60d25af.png"></center>
<br/>
이 버튼을 클릭하면 하단에 프로필 관련 설정을 할 수 있는 actionSheet를 만들기 위해 구현해보자.

## 2. 버튼액션함수 생성
나는 이전에 네비게이션 바에 버튼을 다음과 같이 생성했다.
{% highlight swift %}
...

let rightBarButton = UIBarButtonItem(
    image: UIImage(systemName: "ellipsis"),
    style: .plain,
    target: self,
    action: nil
)

navigationItem.rightBarButtonItem = rightBarButton

...
{% endhighlight %}

`UIBarButtonItem`으로 선언할 때 action함수를 지정해야 한다.<br/>
<br/>
action함수는 `#selector`를 통해 받으므로 objective-C를 통해 함수를 선언하도록 하자.<br/>
<br/>

{% highlight swift %}
@objc func didTapRightBarButtonItem() {
      // title: 나타낼 alert의 제목, message: title과 함께 나타낼 메세지, preferredStyle: alert 스타일
      let actionSheet = UIAlertController(title: nil, message: nil, preferredStyle: .actionSheet)

      // alert발생시 나타날 액션선언 및 추가
      [
      UIAlertAction(title: "회원 정보 변경", style: .default),
      UIAlertAction(title: "탈퇴하기", style: .destructive),
      UIAlertAction(title: "닫기", style: .cancel)
      ].forEach{ actionSheet.addAction($0) }

      // alert 나타내기  
      present(actionSheet, animated: true)
}
{% endhighlight %}
<br/>

actoinSheet를 나타낼 함수를 만들었으니 버튼을 눌렀을때 작동하도록 코드를 수정해주자

{% highlight swift %}
...

let rightBarButton = UIBarButtonItem(
    image: UIImage(systemName: "ellipsis"),
    style: .plain,
    target: self,
    action: #selector(didTapRightBarButtonItem)
)

navigationItem.rightBarButtonItem = rightBarButton

...
{% endhighlight %}
<br/>

함수 추가 완료! 그럼 이제 버튼을 누르면 actionSheet가 잘 나타나는지 확인해보자.<br/>
<br/>

<center><img width="379" src="https://user-images.githubusercontent.com/83946704/146324946-52f8de3e-1a86-421c-a310-733941c4e9bf.gif"></center>
<br/>

완성! 원하는 대로 actionSheet가 잘 작동한다!

## 3. UIAlertController 알아보기

actionSheet를 활용하는 방법은 알았다.<br/>
<br/>
그런데 `UIAlertController`를 선언할 때 설정할 수 있는 여러 변수들이 있었으니 좀 더 알아보도록 하자.<br/>
<br/>

{% highlight swift %}
@objc func didTapRightBarButtonItem() {
      // title, message 수정
      let actionSheet = UIAlertController(title: "타이틀", message: "메세지는 어떻게 나타날까?", preferredStyle: .actionSheet)

      [
      UIAlertAction(title: "회원 정보 변경", style: .default),
      UIAlertAction(title: "탈퇴하기", style: .destructive),
      UIAlertAction(title: "닫기", style: .cancel)
      ].forEach{ actionSheet.addAction($0) }

      present(actionSheet, animated: true)
}
{% endhighlight %}
<br/>

`UIAlertController`선언에서 title, message 변수를 넣어봤다.

<center><img width="379" src="https://user-images.githubusercontent.com/83946704/146327306-c5b24597-ec49-4f89-a904-e4d1fec820c9.gif"></center>
<br/>
오호라... 다음과 같이 타이틀과 메세지가 입력이 되었다.<br/>
<br/>
그러면 이번엔 `preferredStyle`을 변경해보자.

{% highlight swift %}
@objc func didTapRightBarButtonItem() {
      // preferredStyle을 .actionSheet >> .alert 로 변경
      let actionSheet = UIAlertController(title: "타이틀", message: "메세지는 어떻게 나타날까?", preferredStyle: .alert)

      [
      UIAlertAction(title: "회원 정보 변경", style: .default),
      UIAlertAction(title: "탈퇴하기", style: .destructive),
      UIAlertAction(title: "닫기", style: .cancel)
      ].forEach{ actionSheet.addAction($0) }

      present(actionSheet, animated: true)
}
{% endhighlight %}
<br/>

<center><img width="379" src="https://user-images.githubusercontent.com/83946704/146329454-f532454a-b0fd-43aa-a8c7-76aa75b3d9af.gif"></center>
<br/>

alert스타일로 변경하면 alert메세지가 화면 가운데에 발생하게 된다<br/>
<br/>
`UIAlertController`를 선언할 땐 .actionSheet, .alert 이 둘중 필요에 맞는 스타일을 활용하면 될 것 같다.

## 4. 마무리
actionSheet와 alert는 굉장히 많이 사용된다.<br/>
<br/>
기타설정, 더보기 등등 다양한 액션에 사용되므로 이번 포스팅을 통해 필요한 상황이 발생하면 간단하게 이용할 수 있도록 하자!


<br/><br/><br/><br/>
출처 : 30개 프로젝트로 배우는 iOS 앱 개발 with Swift 초격차 패키지 Online - part 4. 인스타그램 앱 만들기
