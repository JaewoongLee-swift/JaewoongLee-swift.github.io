---
title: "[iOS] SF Symbol의 사이즈가 다를때"
excerpt:
categories:
    - iOS
tags:
    - swift
    - iOS
    - SF Symbol
toc: true
last_modified_at: 2021-12-10-02:30:00 +0900
---

## 1. SF Symbol이란?
SF Symbol은 애플에서 개발한 샌프란시스코 폰트(San Francisco font)와 자연스럽게 이용할 수 있게 만든 이미지이다.<br/>
<br/>
자세한 내용은 아래를 참고<br/>
<br/>
<https://developer.apple.com/design/human-interface-guidelines/sf-symbols/overview/>
<br/>
(Apple Human Interface Guidelines - SF Symbol)
<br/><br/>
## 2. SF Symbol을 사용하는 경우
애플에서 직접 사용하도록 만든 만큼 앱의 이미지로 사용하기에 매우 편리하다.<br/>
<br/>
아래와 같은 코드를 통해 버튼에 이미지를 입힐 수 있다.

{% highlight swift %}
private lazy var likeButton: UIButton = {
        let button = UIButton()
        button.setImage(UIImage(systemName: "heart"), for: .normal)

        return button
    }()
{% endhighlight %}

`UIImage`에 heart라는 SF Symbol 이름을 통해 간단하게 이미지를 입힐 수 있다.
<br/><br/>
## 3. SF Symbol의 크기가 다를 때
하지만 다음과 같이 SF Symbol을 사용하지만 아주 미세하게 크기가 다른 경우가 있다.

<center><img src="https://user-images.githubusercontent.com/83946704/145448857-a9b1caaf-0a15-4509-87cd-caf4bddf0db3.png" width="45%" height="45%"></center>
<br/>
다음과 같이 heart, message, paperplane 이미지를 사용했고 버튼의 height과 width를 동일하게 했음에도 미세하게 크기가 다르다. (불편...)<br/>
<br/>
하지만 SF Symbol은 이미 만들어진 이미지이기 때문에 내가 일일히 크기를 조절하기엔 매우 불편함이 있다.<br/>
<br/>
따라서 이런 경우엔 Helper method를 만들어서 좀 더 편리하게 크기를 통일시킬 수 있도록 해보자.

## 4. Helper method 작성 및 사용

{% highlight swift %}
extension UIButton {
    func setImage(systemName: String) {
        // 가로, 세로 정렬
        contentHorizontalAlignment = .fill
        contentVerticalAlignment = .fill
        // 이미지를 꽉차게 만듬
        imageView?.contentMode = .scaleAspectFit
        // 기존 setImage 메소드
        setImage(UIImage(systemName: systemName), for: .normal)
    }
}
{% endhighlight %}

다음과 같이 `UIButton`의 extension을 만들어 동일한 이름의 `setImage`메소드를 일정한 사이즈로 나타나도록 만들었다.<br/>
<br/>
<img width="595" alt="스크린샷 2021-12-10 오전 3 08 47" src="https://user-images.githubusercontent.com/83946704/145451886-f3089918-eab6-4bc3-acbb-bead24127cfc.png">
<br/><br/>
이후, 다음과 같이 helper method를 이용해서 SF Symbol을 이용하면?

<center><img src="https://user-images.githubusercontent.com/83946704/145452261-a00a731c-a99a-454d-b541-d6620aefae5f.png" width="45%" height="45%"></center>
<br/><br/>
정~~~말 미세하지만 이미지들의 크기가 자연스럽게 변했다는 것을 확인할 수 있다!<br/>
<br/>

## 5. 마무리
SF Symbol이 생각보다 크기가 일정하지 않을 경우 다음과 같이 helper method를 만들어 조절할 수 있다.<br/>
<br/>
하지만 extention의 경우, 기존 코드들과 겹쳐서 더러워 보일 수 있으므로 `UIButton+.swift`같이 다른 swift파일에 따로 저장하는 것도 하나의 방법이 될 수 있다.<br/>
<br/>
<img width="507" alt="스크린샷 2021-12-10 오전 3 25 57" src="https://user-images.githubusercontent.com/83946704/145454184-3fc0dc01-e5a3-41af-915a-56594a9a43a6.png">
<br/><br/><br/><br/>
출처 : 30개 프로젝트로 배우는 iOS 앱 개발 with Swift 초격차 패키지 Online - part 4. 인스타그램 앱 만들기
