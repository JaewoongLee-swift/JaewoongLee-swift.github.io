---
title: "[iOS] UITextView에서 유사 placeholder 사용하기"
excerpt:
categories:
    - iOS
tags:
    - swift
    - iOS
    - UITextView
toc: true
last_modified_at: 2021-12-18-01:53:00 +0900
---

## 1. UITextView 사용
인스타그램 유사 앱의 게시물 업로드를 구현하면서 게시물의 이미지를 선택한 후 텍스트를 작성하는 화면을 만들었다<br/>
<br/>
<img width="328" alt="스크린샷 2021-12-17 오후 4 25 32" src="https://user-images.githubusercontent.com/83946704/146505916-616ef55c-ccba-4f1d-a27b-905a88801ac8.png"><br/>
<br/>
하지만 화면 상으로 TextView가 있는지 없는지 알아채기 어렵기 때문에, 확인할 수 있게끔 표시를 해주도록 하자.<br/>

## 2. UITextView를 사용하는 이유
iOS에서 텍스트를 입력하는 객체는 UITextView, UITextField가 있다.<br/>
<br/>
그리고 이번에 사용하는 UITextView가 아닌 UITextField를 사용할 경우, placeholder 설정을 통해 간단히 텍스트 입력안내 문구를 나타낼 수 있다.<br/>
<br/>
<img width="331" alt="스크린샷 2021-12-17 오후 4 32 43" src="https://user-images.githubusercontent.com/83946704/146506820-2d58a8d2-f9dd-4db4-9a50-8f7f4d6df395.png"><br/>
<br/>
다음 사진은 UITextView가 아닌 UITextField를 통해 텍스트 입력창을 만든 경우이다.<br/>
<br/>
UITextField 선언 변수내에 `placeholder` 문구를 설정해주기만 하면 텍스트 입력안내 문구를 나타낼 수 있다.

{% highlight swift %}
private lazy var textField: UITextField = {
    let textField = UITextField()
    // 다음과 같이 textField의 placeholder를 설정해준다.
    textField.placeholder = "문구를 입력하세요."

    return textField
    }()
{% endhighlight %}
<br/>
이처럼 쉽게 사용할 수 있는 textField이지만 textView로 만드려는 이유는 무엇일까?<br/>
<br/>
바로 textField의 '한 줄 제한'이기 때문이다.<br/>
<br/>
textField는 텍스트를 textField에 한 줄로만 입력이 가능하기 때문에 글자 수 제한 없이 작성해야 하는 게시물 업로드 페이지에는 어울리지 않다.<br/>
<br/>
<center><img width="331" alt="스크린샷 2021-12-17 오후 4 32 43" src="https://user-images.githubusercontent.com/83946704/146576723-1c0504f8-05c0-49f2-8188-8a625395c054.gif"></center><br/>

따라서 글자수 제한이 없으면서 줄 변환도 자유로운 textView를 이용해 placeholder와 유사한 효과를 만들어 내도록 하자.

## 2. placeholder 효과 나타내기
첫번째로 placeholder를 만들기 위해선 초기에 회색 글자를 나타내야 한다.<br/>
<br/>
변수 선언에서 문구 입력을 유도하는 문장과 폰트 색상을 회색으로 만들어주도록 하자.<br/>
<br/>
{% highlight swift %}
private lazy var textView: UITextView = {
    let textView = UITextView()
    textView.text = "문구 입력..."
    // placeholder와 같은 회색폰트로 설정
    textView.textColor = .secondaryLabel
    textView.font = .systemFont(ofSize: 15.0)

    return textView
   }()
{% endhighlight %}
<br/>
<img width="331" src="https://user-images.githubusercontent.com/83946704/146577644-64fe5822-b27a-4a4c-b696-77526924db43.png"><br/>
<br/>
일단 동일하게 텍스트 입력을 나타내도록 표시를 하였다!<br/>
<br/>
두번째는 placeholder같이 텍스트 입력과 동시에 텍스트입력 유도 글자가 사라지도록 구현하는 것이다.<br/>
<br/>
구현을 위해선 해당 viewController에 UITextViewDelegate 프로토콜의 함수 사용이 필요하다.<br/>
<br/>
{% highlight swift %}
extension UploadViewController: UITextViewDelegate {
    // didBiginEditing: textView에 텍스트 입력이 시작되면 실행되는 함수
    func textViewDidBeginEditing(_ textView: UITextView) {
        // 분기 설정
        guard textView.textColor == .secondaryLabel else { return }

        textView.text = nil
        textView.textColor = .label
    }
}
{% endhighlight %}
<br/>
`textViewDidBeginEditing`함수를 사용해 textView에 텍스트가 입력이 시작되고 해당 텍스트의 색상이 `.secondaryLabel`(회색)일 경우, textView내의 텍스트는 사라지고 텍스트의 색상은 `.label`(기본 라벨색상)으로 변경된다.<br/>
<br/>
또한 `guard`문을 통해 분기를 설정해 줌으로써, 텍스트를 입력하다 초기화되거나 하는 오류를 방지하였다.<br/>
마지막으로 delegate를 사용하였으므로 textView 변수로 돌아가 delegate 설정을 해주자.<br/>
<br/>
{% highlight swift %}
private lazy var textView: UITextView = {
    let textView = UITextView()
    textView.text = "문구 입력..."
    // placeholder와 같은 회색폰트로 설정
    textView.textColor = .secondaryLabel
    textView.font = .systemFont(ofSize: 15.0)
    // delegate설정
    textView.delegate = self

    return textView
   }()
{% endhighlight %}

<br/>
시뮬레이터를 통해서 확인해 보도록 하자.<br/>
<br/>
<center><img width="331" src="https://user-images.githubusercontent.com/83946704/146579230-6bec47e6-7860-4b86-aa62-b525c9749518.gif"></center><br/>
오호!! 정말로 textView를 placeholder처럼 구현에 성공하였다!

## 3. 마무리
앱을 구성하는 많은 부분에 텍스트 입력이 필요하다.<br/>
<br/>
텍스트 입력이 필요할 경우 그 방법에 따라 textView 또는 textField가 필요할텐데, 만일 textView를 사용하더라도 placeholder처럼 텍스트 입력을 유도할 수 있는 방법을 앞으로 잘 이용할 수 있을 것 같다.<br/>


<br/><br/><br/><br/>
출처 : 30개 프로젝트로 배우는 iOS 앱 개발 with Swift 초격차 패키지 Online - part 4. 인스타그램 앱 만들기
