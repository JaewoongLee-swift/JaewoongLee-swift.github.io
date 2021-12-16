---
title: "[iOS] UIImagePickerController - 사진앱 사용하기"
excerpt:
categories:
    - iOS
tags:
    - swift
    - iOS
    - UIImagePickerController
toc: true
last_modified_at: 2021-12-17-00:50:00 +0900
---

## 1. 사진앱을 사용해야 할 때
sns 업로드, 카톡 전송 등등 앱을 사용하면서 다양한 경우에 사진을 사용할 때가 있다.<br/>
<br/>
그럴 경우 아이폰 자체 앱인 사진앱에 접근해 사진을 선택해야 한다.<br/>
<br/>
이러한 사진앱에 대한 접근이 필요할 때 사용하는 `UIImagePickerController`를 알아보도록 하자

## 2. UIImagePickerController 선언
나는 인스타그램 유사 앱을 만들면서 게시물 업로드 버튼을 누르면 `UIImagePickerController`가 나타나도록 할 것이다.<br/>
<br/>
일단 `UIImagePickerController`를 선언하도록 하자

{% highlight swift %}
private lazy var imagePickerController: UIImagePickerController = {
    let imagePickerController = UIImagePickerController()
    // 사진첩 열기
    imagePickerController.sourceType = .photoLibrary

    return imagePickerController
}()

{% endhighlight %}
<br/>

다음과 같이 `imagePickerController`를 선언해주었다.<br/>
<br/>
`imagePickerController`변수를 선언하였으니 버튼을 터치하면 넘어갈 수 있도록 설정하자.

{% highlight swift %}
...

let uploadButton = UIBarButtonItem(
    image: UIImage(systemName: "plus.app"),
    style: .plain,
    target: self,
    action: #selector(didTapUploadButton)
)

@objc func didTapUploadButton() {
    present(imagePickerController, animated: true)
}

...
{% endhighlight %}
<br/>
이제 사진앱의 사진첩으로 이동할 수 있도록 버튼을 설정해주었으니 테스트해보자.<br/>
<br/>
<center><img width="379" src="https://user-images.githubusercontent.com/83946704/146401644-d9e2bd27-c6ba-4f7b-b6c8-8d428ef025d0.gif"></center>
<br/>
오오...!! 버튼을 터치하니 사진앱이 나타나게 되었다!<br/>
<br/>
하지만 이미지를 터치하면 아무런 액션이 발생하지 않고 `imagePickerController`가 닫혀 버린다.<br/>
<br/>
실제 앱에서 사진 이미지를 이용할 때 그 사진의 일부분만 필요해 이미지를 확대해 잘라내기도 한다.<br/>
<br/>
따라서 선택된 이미지를 편집할 수 있도록 설정해주자.

{% highlight swift %}
private lazy var imagePickerController: UIImagePickerController = {
    let imagePickerController = UIImagePickerController()
    // 사진첩 열기
    imagePickerController.sourceType = .photoLibrary
    // 선택된 이미지 편집허용
    imagePickerController.allowsEditing = true

    return imagePickerController
}()

{% endhighlight %}
<br/>
<center><img width="379" src="https://user-images.githubusercontent.com/83946704/146403366-3bd40ed5-8d48-407c-97b0-75aac1a36879.gif"></center>
<br/>
`imagePickerController.allowsEditing = true`로 설정해주니 이미지를 선택했을 때 편집화면으로 넘어가게 되었다.

## 3. 사진앱 접근 권한설정
지금은 Xcode 시뮬레이터의 사진앱을 사용했기 때문에 자연스럽게 사진앱에 접근권한 alert가 나타나지 않았는데, 실제 앱에서 사진앱을 사용하기 위해선 접근권한 부여가 필요하다.
<br/><br/>
<img width="379" src="https://user-images.githubusercontent.com/83946704/146404919-59271b9b-6bdc-40bc-b391-1ddbbd2a9620.png">
<br/><br/>
다음과 같이 권한설정을 위해 alert를 나타내도록 설정하자.<br/>
<br/>
![스크린샷 2021-12-17 오전 12 59 46](https://user-images.githubusercontent.com/83946704/146405601-eca2fc08-1b75-4d16-b0f2-c7543d081c9c.png)
`info.plist`로 이동해 사진과 같이 'Privacy - Photo Library Addtions Usage Description' Key를 추가해 주고 Value로 권한을 요청하는 텍스트를 작성하면 된다.

## 4. 마무리
`UIImagePickerController`를 이용해 사진앱에 접근하는 방법을 알았다.<br/>
<br/>
실제 앱에서 사용하기 위해선 위와 같은 과정에 더해 이미지를 터치했을 때 터치된 이미지를 이용하는 과정이 필요하다.<br/>
<br/>
따라서 이후 포스팅에선 선택된 이미지를 다른 화면으로 전달하는 법을 알아보도록 하겠다.




<br/><br/><br/><br/>
출처 : 30개 프로젝트로 배우는 iOS 앱 개발 with Swift 초격차 패키지 Online - part 4. 인스타그램 앱 만들기<br/>
<br/>
권한설정 alert 이미지 출처 : <https://guide.worksmobile.com/kr/settings/settings-guide/set-device/set-device-privacy/> (네이버웍스 헬프센터)
