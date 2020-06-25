---
layout: post
title: Core Concepts of Bloc
published: True
category: BLoC
permalink: /BLoC/:year/:month/:day/:title/
---

# Core Concepts of Bloc



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

add method를 통해 event가 생기고 `mapEventToState`가 실행됨.

```dart
void main() {
    CounterBloc bloc = CounterBloc();

    for (int i = 0; i < 3; i++) {
        bloc.add(CounterEvent.increment);
    }
}

```

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

event나 error나 transition이 일어났을 때 여러 Bloc들이 모두 같은 행동을 한다면 그걸 하나로 모아서 관리하는 것이다.

## Core concept of flutter_bloc

#### Bloc Widgets

##### BlocBuilder

`StreamBuilder` widget과 굉장히 비슷하다. widget의 building을 new state에 대응해서 관리한다.

bloc parameter가 빠져있으면 `BlocBuilder`는 자동으로 `BlocProvider`와 현재 `BuildContext`를 확인해서 일을 한다.

```dart
BlocBuilder<BlocA, BlocAState>(
  builder: (context, state) {
    // return widget here based on BlocA's state
  }
)
```

`BlocProvider`와 현재 `BuildContext`에서 갈 수 없으면서 범위가 하나의 widget인 bloc인 경우에는 bloc을 지정해준다.

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

bloc을 children widget에게 전해주는 widget. 

대부분 `BlocProvider`를 써서 subtree의 나머지 부분에 쓸 수 있는 새로운 bloc을 만들어야 한다.

```dart
BlocProvider(
  create: (BuildContext context) => BlocA(),
  child: ChildA(),
);

```

몇몇 case에서는 이미 존재하는 bloc을 쓸 때도 있다. 이미 있는 bloc에 새로운 route를 만들어 줄 때 쓴다.

```dart
BlocProvider.value(
  value: BlocProvider.of<BlocA>(context),
  child: ScreenA(),
);

```

`ChildA`나 `ScreenA`에서 `BlocA`를 쓰려면 다음처럼 쓰면 된다

```dart
// with extensions
context.bloc<BlocA>();

// without extensions
BlocProvider.of<BlocA>(context)

```



##### MultiBlocProvider

다수의 `BlocProvider`를 하나의 widget으로 묶는 역할을 하는 widget이다. 가독성 향상과 `BlocProvider`의 중첩을 없애주는 역할을 한다.

```dart
//Same Code
BlocProvider<BlocA>(
  create: (BuildContext context) => BlocA(),
  child: BlocProvider<BlocB>(
    create: (BuildContext context) => BlocB(),
    child: BlocProvider<BlocC>(
      create: (BuildContext context) => BlocC(),
      child: ChildA(),
    )
  )
)

//Same Code
MultiBlocProvider(
  providers: [
    BlocProvider<BlocA>(
      create: (BuildContext context) => BlocA(),
    ),
    BlocProvider<BlocB>(
      create: (BuildContext context) => BlocB(),
    ),
    BlocProvider<BlocC>(
      create: (BuildContext context) => BlocC(),
    ),
  ],
  child: ChildA(),
)
```



##### BlocListener

state의 변화를 확인하는 widget. state 변할 때 마다 `listener`를 call함.

`BlocBuilder`처럼 bloc parameter가 빠져있으면 자동으로 `BlocProvider`와 현재 `BuildContext`를 확인해서 일을 한다.

```dart
BlocListener<BlocA, BlocAState>(
  listener: (context, state) {
    // do stuff here based on BlocA's state
  },
  child: Container(),
)
```

`BlocProvider`와 현재 `BuildContext`에서 갈 수 없는 bloc인 경우에는 bloc을 지정해준다.

```dart
BlocListener<BlocA, BlocAState>(
  bloc: blocA,
  listener: (context, state) {
    // do stuff here based on BlocA's state
  }
)
```

Parameter중 하나인 `condition`은 이전 state, 현재 state 중 어느 state로 building을 할지 정하는 parameter이다. true면 `listener`가 state를 부르고, false면 state를 부르지 않는다.

```dart
BlocListener<BlocA, BlocAState>(
  condition: (previousState, state) {
    // return true/false to determine whether or not
    // to call listener with state
  },
  listener: (context, state) {
    // do stuff here based on BlocA's state
  },
  child: Container(),
)
```

##### MultiBlocListener

`BlocListener`를 하나로 묶음.

```dart
// Same code
BlocListener<BlocA, BlocAState>(
  listener: (context, state) {},
  child: BlocListener<BlocB, BlocBState>(
    listener: (context, state) {},
    child: BlocListener<BlocC, BlocCState>(
      listener: (context, state) {},
      child: ChildA(),
    ),
  ),
)

// Same code
MultiBlocListener(
  listeners: [
    BlocListener<BlocA, BlocAState>(
      listener: (context, state) {},
    ),
    BlocListener<BlocB, BlocBState>(
      listener: (context, state) {},
    ),
    BlocListener<BlocC, BlocCState>(
      listener: (context, state) {},
    ),
  ],
  child: ChildA(),
)
```



##### BlocConsumer

New state에 반응하기 위해 `builder`와 `listener`를 사용한다. `BlocWidgetBuilder`와 `BlocWidgetListener`가 필수다.

`BlocListener`와 `BlocBuilder`와 비슷하지만 boilerplate code가 적다.

bloc에서 UI의 rebuild와 state change로 인해서 reaction이 실행될 때 사용된다.

`BlocBuilder`나 `BlocListener`와 사용법은 비슷하다.

```dart
BlocConsumer<BlocA, BlocAState>(
  listener: (context, state) {
    // do stuff here based on BlocA's state
    // stuff는 state가 변했다는 것을 navigation처럼 알려주는 역할을 하면 된다.
    // 보통은 snackbar나 dialog등을 사용
  },
  builder: (context, state) {
    // return widget here based on BlocA's state
  }
)

```

`listenWhen`과 `buildWhen`은 `listener`와 `builder`를 더 세밀하게 control하기 위해 사용된다.

```
BlocConsumer<BlocA, BlocAState>(
  listenWhen: (previous, current) {
    // return true/false to determine whether or not
    // to invoke listener with state
  },
  listener: (context, state) {
    // do stuff here based on BlocA's state
  },
  buildWhen: (previous, current) {
    // return true/false to determine whether or not
    // to rebuild the widget with state
  },
  builder: (context, state) {
    // return widget here based on BlocA's state
  }
)
```



##### RepositoryProvider

`BlocProvider`처럼 repository를 children에게 전해주는 widget.

`BlocProvider`는 Bloc제공에 쓰이는 반면 `RepositoryProvider`는 repository를 쓸 때에만 사용되어야 한다.

```dart
RepositoryProvider(
  create: (context) => RepositoryA(),
  child: ChildA(),
);
```

이렇게 하면 `ChildA`는 `Repository`의 instance로 다음과 같이 사용이 가능하다.

```dart
// with extensions
context.repository<RepositoryA>();

// without extensions
RepositoryProvider.of<RepositoryA>(context)
```

##### MultiRepositoryProvider

이제는 이름만 보고도 감이 올텐데 `RepositoryProvider` 를 하나로 묶는데에 사용된다.

```dart
// Same Code
RepositoryProvider<RepositoryA>(
  create: (context) => RepositoryA(),
  child: RepositoryProvider<RepositoryB>(
    create: (context) => RepositoryB(),
    child: RepositoryProvider<RepositoryC>(
      create: (context) => RepositoryC(),
      child: ChildA(),
    )
  )
)

// Same Code
MultiRepositoryProvider(
  providers: [
    RepositoryProvider<RepositoryA>(
      create: (context) => RepositoryA(),
    ),
    RepositoryProvider<RepositoryB>(
      create: (context) => RepositoryB(),
    ),
    RepositoryProvider<RepositoryC>(
      create: (context) => RepositoryC(),
    ),
  ],
  child: ChildA(),
)
```



#### Usage

배운 이것들을 어떻게 쓸 것인가? 아래의 예제를 통해 `BlocBuilder`를 써서 CounterPage widget을 CounterBloc에 연결하는 방법에 대해 살펴보자.

##### counter_bloc.dart

```dart
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



##### counter_page.dart

```dart
class CounterPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final CounterBloc counterBloc = BlocProvider.of<CounterBloc>(context);

    return Scaffold(
      appBar: AppBar(title: Text('Counter')),
      body: BlocBuilder<CounterBloc, int>(
        builder: (context, count) {
          return Center(
            child: Text(
              '$count',
              style: TextStyle(fontSize: 24.0),
            ),
          );
        },
      ),
      floatingActionButton: Column(
        crossAxisAlignment: CrossAxisAlignment.end,
        mainAxisAlignment: MainAxisAlignment.end,
        children: <Widget>[
          Padding(
            padding: EdgeInsets.symmetric(vertical: 5.0),
            child: FloatingActionButton(
              child: Icon(Icons.add),
              onPressed: () {
                counterBloc.add(CounterEvent.increment);
              },
            ),
          ),
          Padding(
            padding: EdgeInsets.symmetric(vertical: 5.0),
            child: FloatingActionButton(
              child: Icon(Icons.remove),
              onPressed: () {
                counterBloc.add(CounterEvent.decrement);
              },
            ),
          ),
        ],
      ),
    );
  }
}

```

Presentation layer와 Business Logic layer를 위와 같이 분리해서 다룰 수 있음을 알 수 있다.

`CounterPage` widget은 버튼을 누르면 어떤 일이 일어나는지에 대해서는 아무것도 모른다. 그냥 `CounterBloc` widget에 user가 어떤 버튼을 눌렀더라~ 하고 알려주기만 할 뿐이다. 그러면 일은 아래것 아니 `CounterBloc` widget에서 해줄 것이다.



이 글은 아래의 글을 참고하여 작성되었습니다.

https://bloclibrary.dev/#/coreconcepts

https://bloclibrary.dev/#/flutterbloccoreconcepts