---
layout: post
title: Architecture of BLoC
published: True
category: BLoC
permalink: /BLoC/:year/:month/:day/:title/
---



![Bloc Architecture](https://bloclibrary.dev/assets/bloc_architecture.png)

Bloc을 쓰면 어플을 다음과 같은 3개의 layer로 나눌 수 있다.

- Presentation
- Business Logic(Bloc)
- Data

### Data Layer

데이터를 검색, 관리하는 부분

2가지 part로 나눌 수 있다.

##### 1. Data Provider

raw data를 제공하며 일반적이고 다용도적이다.

보통 CRUD를 수행한다.

##### 2. Repository

1개 이상의 data provider를 둘러싼 layer다. 다수의 data provider들을 서로 상호작용 시키고 Bloc Layer로 가기 전에 데이터를 수정할 수 있다.



### Bloc Layer

Presentation Layer(UI)에 event로 인한 변화를 연결시켜주는 layer다. 일종의 다리역할



##### Bloc-to-Bloc Comuunication

Bloc은 다른 Bloc의 state 변화에 대응하기 위해 다른 bloc들의 dependency들을 가지고 있다.



### Presentation Layer

Bloc state에 따라 screen을 render함.





이 글은 아래의 글을 참고하여 작성되었습니다.

https://bloclibrary.dev/#/architecture