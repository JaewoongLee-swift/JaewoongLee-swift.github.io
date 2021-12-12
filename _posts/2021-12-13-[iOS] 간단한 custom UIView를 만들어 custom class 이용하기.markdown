---
title: "[iOS] 간단한 custom UIView를 만들어 custom class 이용하기"
excerpt:
categories:
    - iOS
tags:
    - swift
    - iOS
    - UIView
    - class
toc: true
last_modified_at: 2021-12-13-04:30:00 +0900
---

## 1. custom UIView를 사용할 때
인스타그램 유사 앱의 프로필 화면을 구현하는 중, 다음과 같은 화면을 구성하려 했다.<br/>
<br/>
<center><img width="284" alt="스크린샷 2021-12-13 오전 3 46 41" src="https://user-images.githubusercontent.com/83946704/145725371-42f6b5d8-5c3a-4125-a758-76ef17b2bb0b.png"></center>
<br/>
프로필 화면에서 게시물, 팔로우, 팔로잉이 나타나는 부분은 각각의 게시물, 팔로우, 팔로잉의 텍스트 라벨로 나타내고 각 숫자 또한 텍스트 라벨로 나타내면 된다.<br/>
<br/>
하지만 각각 6개의 라벨을 따로 만들어주고 서로의 오토레이아웃을 설정해줄 경우 코드 구성이 복잡해질 뿐더러 유지보수에서도 불편하게 된다.<br/>
<br/>
따라서 숫자를 나타내는 라벨 1개, 숫자의 이름을 나타내는 라벨 1개가 포함된 custom UIView를 만들고 그로 인해 만들어진 custom class를 이용해서 3개의 View를 StackView로 만들어 보도록 하자.

## 2. custom UIView 생성
일단 swift파일을 생성해 Snapkit과 UIKit을 import해주고 필요한 이름으로 class 선언을 해준다.<br/>
(Snpakit을 이용해 오토레이아웃을 설정하였음)<br/>
<br/>
그리고 필요한 두개의 라벨을 선언해주고 각각의 설정을 넣어준다.
<br/>

{% highlight swift %}
import Snapkit
import UIKit

// 최종 하위클래스이므로 final class로 선언
final class ProfileDataView: UIView {
  private lazy var titleLabel: UILabel = {
    let label = UILabel()
    label.font = .systemFont(ofSize: 14.0, weight: .medium)
    label.text = "게시물"

    return label
  }()

  private lazy var countLabel: UILabel = {
    let label = UILabel()
    label.font = .systemFont(ofSize: 14.0, weight: .bold)
    label.text = "100"

    return label
  }()

}
{% endhighlight %}
<br/>
그 다음 extention을 통해 레이아웃을 설정하는 함수를 만들고 StackView와 오토레이아웃 설정을 해준다

{% highlight swift %}
private extension ProfileDataView {
    func setupLayout() {
        // stackview는 순서에 영향을 받으므로 countLabel 먼저 넣어줌
        let stackView = UIStackView(arrangedSubviews: [ countLabel, titleLabel ])
        // 세로배치, 가운데 정렬, 라벨 사이공간 4.0
        stackView.axis = .vertical
        stackView.alignment = .center
        stackView.spacing = 4.0

        // View에 추가(Snapkit 사용)
        addSubview(stackView)
        stackView.snp.makeConstraints{ $0.edges.equalToSuperview() }
    }
}
{% endhighlight %}
<br/>
우리는 3개의 뷰를 모두 100, 게시물 이란 내용으로 사용할 것이 아니다.<br/>
<br/>
따라서 상위 뷰에서 class 선언에 따라 필요한 데이터를 넣어줄 수 있도록 변수와 초기화 설정을 해주자.<br/>
<br/>
{% highlight swift %}
final class ProfileDataView: UIView {
  private let title: String
  private let count: Int

  init(title: String, count: Int) {
    self.title = title
    self.count = count

    super.init(frame: .zero)

    //오토레이아웃 설정
    setupLayout()
  }

  required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
  }

  ...

}

{% endhighlight %}
<br/>
그리고 마지막으로 라벨에 임시적으로 설정했던 텍스트의 내용도 변경해주도록 하자

{% highlight swift %}
final class ProfileDataView: UIView {

  ...

  private lazy var titleLabel: UILabel = {
    let label = UILabel()
    label.font = .systemFont(ofSize: 14.0, weight: .medium)
    // label.text = "게시물"
    label.text = title

    return label
  }()

  private lazy var countLabel: UILabel = {
    let label = UILabel()
    label.font = .systemFont(ofSize: 14.0, weight: .bold)
    // label.text = "100"
    label.text = "\(count)"

    return label
  }()
}
{% endhighlight %}
<br/>
custom UIView class 생성 완료!

## 3. custom UIView 사용
custom UIView를 사용할 class로 이동해 다음과 같이 선언해주고 stackview 및 오토레이아웃을 설정해주자.
{% highlight swift %}
class ProfileViewController: UIViewController {

  ...

  private let photoDataView = ProfileDataView(title: "게시물", count: 123)
  private let followerDataView = ProfileDataView(title: "팔로워", count: 246)
  private let followingDataView = ProfileDataView(title: "팔로잉", count: 369)

  ...

  // 오토레이아웃은 생략
  let dataStackView = UIStackView(arrangedSubviews: [photoDataView, followerDataView, followingDataView])
  dataStackView.spacing = 4.0
  dataStackView.distribution = .fillEqually
}
{% endhighlight %}
<br/>
설정 후 계층(hierarchy)을 확인해보면,<br/>
<br/>
<center><img width="284" alt="스크린샷 2021-12-13 오전 4 27 33" src="https://user-images.githubusercontent.com/83946704/145726665-d395701b-a175-40ad-a7b7-2c368d0949a7.png"></center>
<br/>
정상적으로 3개의 뷰가 스택뷰로 이루어진 것을 확인할 수 있다!!<br/>
<br/>
이제 실제로 데이터를 연동해서 사용할 경우 custom UIView를 선언해 필요한 데이터(팔로워 수, 게시글 수 등)를 넣어줄 수 있을 것이다.

## 4. 마무리
코드를 작성함에 있어서 중요한 것은 간결한 코드작성, 편리한 유지보수라고 생각한다. <br/>
<br/>
따라서 같은 구성의 뷰를 여러번 사용해야 할 경우 custom class를 이용해 간결하고 편리하게 코드를 작성할 수 있도록 하자.






<br/><br/><br/><br/>
출처 : 30개 프로젝트로 배우는 iOS 앱 개발 with Swift 초격차 패키지 Online - part 4. 인스타그램 앱 만들기
