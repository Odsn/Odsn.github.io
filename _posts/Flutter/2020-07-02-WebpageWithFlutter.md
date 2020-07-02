---
layout: post
title: Flutter로 Web page 만들기
published: True
category: Flutter
permalink: /Flutter/:year/:month/:day/:title/
---

# Make Webpage Using Flutter

flutter에는 android, ios 뿐만 아니라 web에도 동시에 적용이 가능하다. 사실상 1개 만들면 3개가 동시에 만들어 지는 셈이다. 그래도 모바일 앱과 웹은 다를 수 밖에 없는데 모바일 앱은 `pubspec.yaml`에서 여러 플러그인을 설정하고 가져오는 반면, 웹은 `index.html`에서 설정하고 들어가는게 차이점이다.(firebase 연동 밖에 안해봐서 index.html말고 다른 곳에서 설정을 추가로 할수도 있다)

그리고 가장 중요한 부분은 실제 webpage처럼 구현이 되는 것이 아니라 블루스택을 통해 모바일 앱을 플레이 하는 것처럼 웹에서 어플을 사용할 수 있도록 구현이 되는 것이다. 그렇기에 android studio에서 flutter project를 다룰 때 있는 flutter inspector도 적용이 가능하다.

즉, flutter를 통해 웹을 구현하는 것은 webpage에 구현한 모바일 앱을 띄우는 것 뿐이다.

<center> Web </center>

![webimage](https://user-images.githubusercontent.com/43085342/86302811-dbd21e00-bc44-11ea-8d51-2bb958ae2e09.png)



<center> Mobile App </center>

![image](https://user-images.githubusercontent.com/43085342/86303101-bf82b100-bc45-11ea-822d-d1d4ef037d2b.png)

T H E E N D

