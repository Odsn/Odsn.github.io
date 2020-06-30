---
layout: post
title: Simple BLoC Tutorial - Counter
published: True
category: Flutter
permalink: /Flutter/:year/:month/:day/:title/
---

# Tutorial: Counter

+버튼을 누르면 1이 증가하고 -버튼을 누르면 1이 감소하는 간단한 어플이다. 코드에 대한 설명은 주석 참고.

#### Main.dart

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
  // initialState 설정
  @override
  int get initialState => 0;

  // Evnet가 생겼을 때, state를 변화시켜서 다시 stream에다가 집어넣음.
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
    // BlocProvider로 Bloc생성
    final CounterBloc counterBloc = BlocProvider.of<CounterBloc>(context);

    return Scaffold(
      appBar: AppBar(title: Text('Counter')),
      // CounterBloc의 state인 count에 따라 표시될 text가 달라질 것.
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
                // Event 발생. 처리는 CounterBloc의 mapEventToState에서!
                counterBloc.add(CounterEvent.increment);
              },
            ),
          ),
          Padding(
            padding: EdgeInsets.symmetric(vertical: 5.0),
            child: FloatingActionButton(
              child: Icon(Icons.remove),
              onPressed: () {
                // Event 발생. 처리는 CounterBloc의 mapEventToState에서!
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

이 글은 아래의 글을 참고하여 작성되었습니다.

[Counter Tutorial Using Bloc](https://bloclibrary.dev/#/fluttercountertutorial)