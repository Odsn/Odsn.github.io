---
layout: post
title: Core Concepts of Bloc
published: True
category: BLoC
permalink: /BLoC/:year/:month/:day/:title/
---

### Core Concepts of Bloc

Bloc의 사용을 위해 꼭 알아야 할 것들은 다음과 같다.

#### Events

Bloc의 input이다. 버튼을 눌렀다거나 페이지가 로드된다거나 등의 사건이 일어나는 것을 말한다.

#### States

Bloc의 output이다. 어플의 현재 상태를 나타낸다.

#### Transitions

현재 state가 A라는 event를 만났을 때, 어떤 state로 변하게 되는데 이 변화를 transition이라고 한다.

#### Streams

Stream은 asynchronous data들을 모아둔 pipe라고 생각하면 된다. Asynchronous data들은 물이라고 생각하고 pipe에 물이 흐르고 있는 것을 생각하면 접근이 쉬워진다.

Stream을 생성할 때는 async*로 생성할 수 있다.

```dart
Stream<int> countStream(int max) async* {
    for (int i = 0; i < max; i++) {
        yield i;
    }
}
```

`yield`는 stream으로 data를 넣는 keyword이다. 위 함수는 0~max-1까지 stream에 data를 넣는 작업을 하고 있다.



Stream을 다룰 때에는 Future, async, await으로 다룰 수 있다. 지금 당장이 아닌 미래에 값이 들어올 것이기 때문이다.

```dart
Future<int> sumStream(Stream<int> stream) async {
    int sum = 0;
    await for (int value in stream) {
        sum += value;
    }
    return sum;
}
```

Stream에 있는 data들을 모두 받아서 sum에다가 더하고 이를 반환하는 함수이다.

#### Blocs

**B**usiness **Lo**gic **C**omponent**s**의 약자로 events를 states로 바꾸는 역할을 한다.

모든 bloc은 아무런 event가 일어나지 않은 `inital state`를 만들어 줘야 하고, event가 들어오면 state를 반환하는 `mapEventToState`도 만들어 줘야 한다.

```dart
import 'package:bloc/bloc.dart';

enum CounterEvent { increment, decrement }

class CounterBloc extends Bloc<CounterEvent, int> {
  @override
  int get initialState => 0;

  @override
  Stream<int> mapEventToState(CounterEvent event) async* {
    switch (event) {
      case CounterEvent.decrement:
        yield state - 1;
        break;
      case CounterEvent.increment:
        yield state + 1;
        break;
    }
  }
}
```

add method를 통해 event가 생기고 mapEventToState가 실행됨.

state가 변하는 transition을 관리하려면 먼저 `onTransition` method를 override시켜야 한다. `onTransition`은 모든 local Bloc을 관리할 수 있고, Bloc의 state가 update되기 직전에 call된다.

그리고 `onError`를 사용하면 exception도 관리가 가능하다. 

```dart
@override
void onTransition(Transition<CounterEvent, int> transition) {
    print(transition);
    super.onTransition(transition);
}

@override
void onError(Object error, StackTrace stackTrace) {
  print('$error, $stackTrace');
}
```



#### BlocDelegate

Bloc을 쓰면 한 곳에서 모든 transition에 접근이 가능하다.

이건 뭔소린지 모르겠는데 나에게 이것은 한동안 쓸 일이 없을 것 같다.



### Core concept of flutter_bloc

#### Bloc Widgets

##### BlocBuilder

StreamBuilder 위젯과 굉장히 비슷하다. 위젯의 building을 new state에 대응해서 관리한다.

bloc parameter가 빠져있으면 `BlocBuilder`는 자동으로 `BlocProvider`와 현재 `BuildContext`를 확인해서 일을 한다.

```dart
BlocBuilder<BlocA, BlocAState>(
  builder: (context, state) {
    // return widget here based on BlocA's state
  }
)
```

`BlocProvider`와 현재 `BuildContext`에서 갈 수 없으면서 범위가 하나의 위젯인 bloc인 경우에는 bloc을 지정해준다.

```dart
BlocBuilder<BlocA, BlocAState>(
  bloc: blocA, // provide the local bloc instance
  builder: (context, state) {
    // return widget here based on BlocA's state
  }
)

```

Parameter중 하나인 `condition`은 이전 state, 현재 state 중 어느 state로 building을 할지 정하는 parameter이다. true면 rebuild하고 false면 그대로 내비둔다.

```dart
BlocBuilder<BlocA, BlocAState>(
  condition: (previousState, state) {
    // return true/false to determine whether or not
    // to rebuild the widget with state
  },
  builder: (context, state) {
    // return widget here based on BlocA's state
  }
)
```



##### BlocProvider





##### MultiBlocProvider

##### BlocListener

##### BlocConsumer

##### RepositoryProvider

##### MultiRepositoryProvider

#### Usage

##### counter_bloc.dart

##### counter_page.dart



이 글은 아래의 글을 참고하여 작성되었습니다.

https://bloclibrary.dev/#/coreconcepts

https://bloclibrary.dev/#/flutterbloccoreconcepts