---
layout: post
title: What is Bloc?
published: True
category: Flutter
permalink: /Flutter/:year/:month/:day/:title/
---

# This is BLoC

## Getting Started

BLoC에 대해 알아보기 전에 준비해야 할 것.

#### OverView

Bloc은 다음과 같은 pub package로 구성이 되어 있다.

- [bloc](https://pub.dev/packages/bloc) - Core bloc library
- [flutter_bloc](https://pub.dev/packages/flutter_bloc) - Powerful Flutter Widgets built to work with bloc in order to build fast, reactive mobile applications.
- [angular_bloc](https://pub.dev/packages/angular_bloc) - Powerful Angular Components built to work with bloc in order to build fast, reactive web applications.

우리는 모바일을 통해 앱을 개발할 것이기 때문에 위의 2개만 사용하면 된다.



#### Setting

다음의 dependency들을 pubspec.yaml에 추가해주자.

```yaml
dependencies:
  bloc: ^4.0.0
  flutter_bloc: ^4.0.0
```

그리고 main.dart에 import를 시켜주자.

```dart
import 'package:bloc/bloc.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
```

이러면 사용 준비는 끝!



## Why BLoC?

#### BLoC(Business Logic Component)

UI와 Logic을 따로 분리해서 사용하는 기법으로 효율적으로 개발할 수 있게 도와준다.

개발자는 보통 어플이 어떤 방식으로 돌아가고 있고 잘 돌아가는지 알고 싶어하고, 최대한 효율적으로 관리하고 싶어 한다.

Bloc에 있는 다음과 같은 3가지 특징들은 개발자에게 큰 도움이 된다.

- Simple: 이해하기 쉽고 사용도 어렵지 않음
- Powerful: 복잡한 어플을 더 작은 components들로 쪼개서 관리할 수 있게 해줌
- Testable: 어플의 모든 부분을 쉽게 테스트 가능

이런이런 어서 빨리 Bloc을 써봐야 겠는걸?



이 글은 아래의 글을 참고하여 작성되었습니다.

[Getting Started](https://bloclibrary.dev/#/gettingstarted)

[Why Bloc?](https://bloclibrary.dev/#/whybloc)