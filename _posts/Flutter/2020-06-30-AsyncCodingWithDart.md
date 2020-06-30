---
layout: post
title: Async Coding With Dart
published: True
category: Flutter
permalink: /Flutter/:year/:month/:day/:title/
---

# Async Coding With Dart

이 글은 flutter 공식 youtube에서 5개의 영상으로 이루어진 Async Coding With Dart를 보고 정리한 글이다.

### Isolates and Event loops

Isolate는 multithread처럼 각각 돌아가게 하는데 memory공유는 안하고 각각이 memory를 가지고 있음

Dart의 event는 항상 Isolate에서 일어나고 처리

같이 작동시키려면 서로 메시지 보내면서 해야하는 단점이 있지만 장점이 있음. lock을 안해도 됨!



Event 중 어떤 일이 먼저 일어날지 모름

Event loop를 queue로 해서 event가 발생하면 하나씩 하나씩 처리함.

onPressed나 Network에서 뭐를 future로 가져오는 것도 모두 하나의 event일 뿐.



영상: [Isolates and Event loops](https://www.youtube.com/watch?v=vl_AaCgudcY&list=PLjxrf2q8roU2HdJQDjJzOeO6J3FoFLWr2&index=14)

### Future

uncompleted(상자만 제공 아직 안열음)

completed with data(상자 열엇는데 내용이 data)

completed with error(상자 열엇는데 내용이 error)



```dart
RaisedButton(
	onPressed: () {
    final myFuture = http.get('https://my.image.url');
    myFuture.then((resp) {
      setImage(resp);
    });
  },
  child: Text

)
  
//Future를 직접 만들일은 거의 없을것
  
void main() {
  Future.delayed(Duration(seconds: 2), () {
    print('Can i go to dorm?');
  });

  final myFuture = Future(() {
    print('Creating the future');
    return 12;
  });

  print('Sleepy');
}

/*
결과창
Sleepy
Creating the future
Can i go to dorm?
*/

// error가 생기면 catchError로~
void main(){
  Future<int>.delayed(Duration(seconds: 3), () {
    throw 'Error!';
  }).then((value) {
    print(value);
  }).catchError((err) {
    print('Caught $err');
    // 이걸 쓰면 err를 더 자세히 알 수 있음!
    test: (err) => err.runtimeType == String,
  });

  print('waiting...');
  
}
```

영상: [Dart Future](https://www.youtube.com/watch?v=OTS-ap9_aXc&list=PLjxrf2q8roU2HdJQDjJzOeO6J3FoFLWr2&index=15)



### Dart Stream

|        | Single value | Zero or more values |
| ------ | ------------ | ------------------- |
| Sync:  | int          | Iterator<int>       |
| Async: | Future<int>  | Stream<int>         |



```dart
final myStream = NumberCreator().stream;

// listen을 통해 stream을 받아볼 수 있음
final subscription = myStream.listen(
	(data) => print('Data: $data'),
  onError: (err){
    // error발생
  },
  // default: true, 이거로도 error 잡을수 있다
  canceelOnError: false
  // stream이 data전송 끝내면 실행
  onDone: (){
    // 대충 이런거 하는 코드
  }
);

// stream에서 각각의 value를 가져옴
NumberCreator().stream.map((value) => function)

// 조건에 해당하는 value만 가져옴
NumberCreator().stream.where(조건).map((value) => function).listen(action)
  
// future 상태에 따라 widget을 형성
StreamBuilder<type>(
	stream: NumberCreator().stream.map((value) => function),
  builder: (context, snapshot){
    /* 
    snapshot의 state에 따른 widget 형성
    */
  }
)
  
  
class NumberCreator{
  NumberCreator(){
    Timer.periodic(Duration(seconds: 1), (t) {
      // 새로운 데이터가 추가되는 sink
      _controller.sink.add(_count);
      _count++;
    });
    
    var _count = 1;
    
    final _controller = StreamController<int>();
    
    Stream<int> get stream => _controller.stream;
  }  
}
```

영상: [Dart Stream](https://www.youtube.com/watch?v=nQBpOIHE4eE&list=PLjxrf2q8roU2HdJQDjJzOeO6J3FoFLWr2&index=16)



### Async/Await



```dart
Future<int> _loadFromDisk() {}

Future<String> _fetchNetworkData(int ad) {}

// 아래 코드에 future가 없을 경우 여기서 
ProcessedData createData() {
  final id = _loadFromDisk();
  final data = _fetchNetworkData(id);
  return ProcessedData(data);
}

// async가 있으면 무조건 await도 있음
Future<ProcessedData> createData() async {
  final id = await _loadFromDisk();
  final data = await _fetchNetworkData(id);
  return ProcessedData(data);
}

// 위 코드와 같음
Future<ProcessedData> createData() {
  return _loadFromDisk().then((id) {
    return _fetchNetworkData(id);
  }).then((data){
    return ProcessedData(data);
  })
}

// 코드를 기능 기준으로 3곳으로 분할 가능
// 1. 함수가 시작하면서 loadFromDisk를 call하고 wait
Future<ProcessedData> createData() {
  return _loadFromDisk()
    
    // wait해서 가져온 data를 써서 networkData를 call하고 wait
    .then((id) {
    return _fetchNetworkData(id);
  })
    
    // 그 다음 ProcessedData를 networkData에서 가져온 데이터를 사용해 return
    .then((data){
    return ProcessedData(data);
  })
}

// 마찬가지로 3분할 가능한데 위에 코드보다 분할이 깰끔~
Future<ProcessedData> createData() async {
  final id = await _loadFromDisk();

  final data = await _fetchNetworkData(id);
  
  return ProcessedData(data);
}

// error를 잡는건 try catch로~

// for loop 쓰고싶음! very simple
// async달아주고 return type Future로 해주고 data를 기다렸다가 해야하는 작업에 await 붙여주면 done.
Future<int> getTotal(List<int> numbers) async {
  int total = 0;
  
  await for (final value in numbers){
    total += value;
  }
  
  return total;
}

class ProcessedData {
  ProcessedData(this.data);
  final String data;
  
}
```

영상: [Async/Await](https://www.youtube.com/watch?v=SmTCmDMi4BY&list=PLjxrf2q8roU2HdJQDjJzOeO6J3FoFLWr2&index=17)



### Generator

Generator를 많이 쓸일은 없을건데 쓰면 편해질 순간이 올거임ㅋ

|        | Single value | Zero or more values |
| ------ | ------------ | ------------------- |
| Sync:  | int          | Iterator<int>       |
| Async: | Future<int>  | Stream<int>         |

이 표에서 오른쪽 열인 Zero or more values가 generator를 써서 iterate 시키는 부분

```dart
// Iterator. 한번에 하나씩 하게 해서 반복을 가능하게 함
abstract class Iterator<E> {
  bool moveNext();
  E get current
}

// 특정한 부류의 iterator를 쓸 수 있는 Iterable
class MyString extends Iterable<String> {
  Mystring(this.strings);
  final List<String> strings;
  
  Iterator<String> get iterator => strings.iterator;
}

void main(){
  final myStrings = MyStrings([
    'One',
    'Two',
    'Three',
  ]);
  
  final lengths = myStrings.map((s) => s.length);
  
	// Iterable를 for/in에 넣을 수 있음
  for (final length in lengths){
    print(length);
  }
}

//위와 같은 iterable을 내보내는 generator 만들기
//function의 return type이 iterable로 설정해서 선언하면 됨
Iterable<int> getRange(int start, int finish) sync* {
  for (int i = start; i <= finish; i++){
    // yield는 return과 비슷한데 function을 끝내지는 않음
    // 대신 single value 주고 caller가 다음거 줄때까지 기다림. 이걸 반복
    yield i;
  }
}

// recursive
Iterable<int> getRange(int start, int finish) sync* {
  if (start <= finish){
    yield start;
    for (final val in getRange(start + 1, finish)) {
      yield val;
    }
  }
}

// same code
Iterable<int> getRange(int start, int finish) sync* {
  if (start <= finish){
    yield start;
		yield* getRange(start + 1, finish);
  }
}

void main() {
  final numbers = getRangee(1, 10);
  
  for(int val in numbers){
    print(val);
  }
  
  // 위에꺼 대신 이렇게 사용도 가능
  numbers.forEach(print);
}

/// Stream의 경우
// 서버에서 숫자 2배로 하는 함수가 있다고 치자
Future<int> fetchDouble(int val){
  // Fetch val * 2 from the server
}

// Stream으로 return type 설정해서 함수 선언하고
// async* 붙여주고 yield 써서 값 받고 저장
Stream<int> fetchDoubles(int start, int finish) async* {
  for(int i = start; i <= finish; i++){
    yield await fetchDouble(i);
  }
}

// recursive
Stream<int> fetchDoubles(int start, int finish) async* {
  if(start <= finish){
		yield await fetchDouble(start);
    yield* fetchDoubles(start + 1, finish);
  }
}

void main() {
  fetchDoubles(1, 10).listen(print);
  
}

```

영상: [Generator Function](https://www.youtube.com/watch?v=TF-TBsgIErY&list=PLjxrf2q8roU2HdJQDjJzOeO6J3FoFLWr2&index=18)

