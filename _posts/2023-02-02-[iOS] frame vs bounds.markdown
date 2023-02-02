---
title: "[iOS] frame vs bounds"
excerpt:
categories:
    - iOS
tags:
    - frame
    - bounds

toc: true
last_modified_at: 2023-02-02-13:17:00 +0900
---
## 0. 서론
볼 때 마다 헷갈려서 결국 정리해서 포스팅하는 frame vs bounds<br/><br/>
오늘 이후로는 안헷갈리고 확실히 알아갔으면 좋겠다.

## 1. Frame
SuperView 좌표계에서 View의 위치와 크기를 나타낸다.

### 1-1. frame의 origin(x, y)
frame이 나타내는 orgin(x, y) 좌표는<br/><br/>

SuperView의 원점을 (0, 0)으로 두고 원점으로 부터 얼마나 떨어져 있는지를 나타낸 것<br/>
(원점이란 View의 가장 왼쪽 윗부분을 말함)

### 1-2. frame의 size(width, height)
frame의 size는 View 영역을 모두 감싸는 사각형으로 나타낸 것<br/><br/>
<img width="341" alt="스크린샷 2023-02-02 오전 10 46 55" src="https://user-images.githubusercontent.com/83946704/216220158-ce34e360-fa3c-4e7d-9c14-9c3513c04bd2.png"><br/>

<img width="311" alt="스크린샷 2023-02-02 오전 10 47 33" src="https://user-images.githubusercontent.com/83946704/216220193-91a4bc3a-fce6-4523-8658-36f51671e409.png"><br/>

("View 영역을 모두 감싸는 사각형"의 예시)<br/><br/><br/>

<img width="327" alt="스크린샷 2023-02-02 오전 10 48 28" src="https://user-images.githubusercontent.com/83946704/216220292-cc848aa4-eede-4183-90b4-de3b49233c6b.png"><br/>

<img width="502" alt="스크린샷 2023-02-02 오전 10 48 38" src="https://user-images.githubusercontent.com/83946704/216220321-53a1667e-252f-4d01-a5a4-b2d8371b7ae8.png"><br/><br/>

secondView가 회전하여 secondView를 감싸는 사각형의 크기가 변경됨.

## 2. Bounds
자신의 좌표계에서 View의 위치와 크기를 나타낸다.

### 2-1. bounds의 origin(x, y)
bounds가 나타내는 orgin(x, y) 좌표는 자신의 원점을 (0, 0)으로 놓음<br/><br/>

따라서 view를 처음 생성하면 bounds의 origin은 무조건 (0, 0)

### 2-2. bounds의 size(weight, height)
bounds의 size는 View 자체의 영역을 나타낸 것

<img width="334" alt="스크린샷 2023-02-02 오전 10 56 23" src="https://user-images.githubusercontent.com/83946704/216220565-519b2eb9-ea95-4b43-bf72-566957a53396.png"><br/>

따라서 View를 회전시키더라도 View 자체의 영역은 변하지 않으므로 bounds의 size도 변하지 않는다.

## 3. Frame과 Bounds의 origin을 변화시켰을 때
### 3-1. frame의 origin 변화
![R1280x0](https://user-images.githubusercontent.com/83946704/216229747-4d818cd1-0bcc-4e98-9b0b-706b89327d13.png)<br/>
(출처 : 개발자 소들이님)<br/><br/>
frame의 origin 값을 변경하면 origin 값에 맞는 위치로 이동

### 3-2. bounds의 origin 변화
![R1280x0](https://user-images.githubusercontent.com/83946704/216229886-ce6466b1-89bb-4a18-b360-45f2847071fd.png)<br/>
(출처 : 개발자 소들이님)<br/><br/>
독특하게 thirdView가 움직인다.<br/><br/>
bounds의 변화는 secondView가 (50, 50) 위치로 이동하라는게 아닌 secondView가 보여주는 화면의 좌표계가 (50, 50)으로 이동하는것.<br/><br/>
따라서 secondView가 보여주는 화면이 (0, 0)일 땐 thirdView가 가운데 위치에 있지만, 보여주는 화면의 위치가 (50, 50) 움직이므로 (보여주는 화면이 오른쪽 50 아래 50으로 이동) thridView가 마치 (-50, -50)으로 이동한 것 처럼 보이는것.<br/><br/>
당연하게, secondView, thirdView의 frame은 전혀 변하지 않는다.<br/><br/>
결과적으로 bounds의 orgin을 변경하면 x, y값의 반대방향으로 subView들의 frame이 "움직인 것 처럼" 보이는 것

## 4. Frame과 Bounds의 사용
### 4-1. frame의 사용
frame은 UIView의 위치 및 크기를 설정할 때 사용된다.

{% highlight swift %}
let myView: UIView = .init(frame: .init(x: 100, y: 100, width: 100, height: 100))
{% endhighlight %}

### 4-2. bounds의 사용
- View를 회전한 후 View의 실제 크기를 알고 싶을 때
- View 내부에 그림을 그릴 때(drawRect)
- ScrollView에서 스크롤을 할 때

## 5. 마무리
포스팅을 꾸준히 하자고 마음먹었는데 생각보다 쉽지 않다. 완전히 정리된 글로 작성하고자 하니 쉽게 손이 잘 안떨어진다...
<br/><br/>
그래도 내 기록을 남긴다는 생각으로 공부한 내용들을 자주 올려놔야겠다.
<br/><br/><br/><br/>


참고 : 소들이, iOS) Frame vs Bounds 제대로 이해하기 (1/3)
(<https://babbab2.tistory.com/44>)<br/>
참고 : 소들이, iOS) Frame vs Bounds 제대로 이해하기 (2/3)
(<https://babbab2.tistory.com/45>)<br/>
참고 : 소들이, iOS) Frame vs Bounds 제대로 이해하기 (3/3)
(<https://babbab2.tistory.com/46>)<br/>
