---
layout: post
title: What is Bloc?
published: True
category: BLoC
permalink: /BLoC/:year/:month/:day/:title/
---

## What is BLoC?

Bloc은 UI와 Logic을 따로 분리해서 사용하는 기법으로 효율적으로 개발할 수 있게 도와준다.

개발자는 보통 어플이 어떤 방식으로 돌아가고 있고 잘 돌아가는지 알고 싶어하고, 최대한 효율적으로 관리하고 싶어 한다.

Bloc에 있는 다음과 같은 3가지 특징들은 개발자에게 큰 도움이 된다.

- Simple: 이해하기 쉽고 사용도 어렵지 않음
- Powerful: 복잡한 어플을 더 작은 components들로 쪼개서 관리할 수 있게 해줌
- Testable: 어플의 모든 부분을 쉽게 테스트 가능

이런이런 어서 빨리 Bloc을 써봐야 겠는걸?







### Architecture

![Bloc Architecture](https://bloclibrary.dev/assets/bloc_architecture.png)

Bloc을 쓰면 어플을 다음과 같은 3개의 layer로 나눌 수 있다.

- Presentation
- Business Logic(Bloc)
- Data

#### Data Layer

데이터를 검색, 관리하는 부분

2가지 part로 나눌 수 있다.

##### 1. Data Provider

raw data를 제공하며 일반적이고 다용도적이다.

보통 CRUD를 수행한다.

##### 2. Repository

1개 이상의 data provider를 둘러싼 layer다. 다수의 data provider들을 서로 상호작용 시키고 Bloc Layer로 가기 전에 데이터를 수정할 수 있다.



#### Bloc Layer

Presentation Layer(UI)에 event로 인한 변화를 연결시켜주는 layer다. 일종의 다리역할



##### Bloc-to-Bloc Comuunication

Bloc은 다른 Bloc의 state 변화에 대응하기 위해 다른 bloc들의 dependency들을 가지고 있다.



#### Presentation Layer

Bloc state에 따라 screen을 render함.





### Tutorial - Counter

```dart
import 'package:flutter/material.dart';
import 'package:bloc/bloc.dart';
import 'package:flutter_bloc/flutter_bloc.dart';

enum CounterEvent { increment, decrement }

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      home: BlocProvider<CounterBloc>(
        create: (context) => CounterBloc(),
        child: CounterPage(),
      ),
    );
  }
}

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



### Tutorial - Firebase Login

