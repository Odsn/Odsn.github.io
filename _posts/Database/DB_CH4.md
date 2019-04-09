## Ch.4:High-Level Database Models

----

### 1.The Entity/Relationship Model

E/R Model(Diagram)은 High-Level Design의 가장 대표적인 모델이다. 기본적으로 DB Modeling은 High-Level Design, Relational DB Schema, Relational DBMS 3가지를 통해 구현한다.

![DB4_1](https://user-images.githubusercontent.com/43085342/55796658-7658b580-5b05-11e9-8e6c-a7d3bd7349e9.PNG)

- Rectangle : Entity(table)
- Diamond: Relationship between entity
  - 어떻게 Relationship을 만들 것인가? 2개의 entity에서 모든 information을 가져올 필요 없이 각 entity의 primary key만을 가져오면 모든 정보에 접근 가능.
  - Relationship의 key의 설정은 지난 chapter에서 다뤘다.
- Oval: Attribute
- Multiplicity Relationship(Many:Many, Many:1, 1:1) 
- Arrow: M:1일때(Many to 1) 화살표가 가리키는 곳이 1
  - Round arrow: exactly one
  - Arrow pointing to: at most one(0 or 1)

**Multiway Relationship**

entity가 3개 이상인 relationship. 아래는 three-way relationship의 예시다. figure 2를 아래와 같이 표현할 수 있다.

![DB4_2](https://user-images.githubusercontent.com/43085342/55797421-56c28c80-5b07-11e9-8bb1-ca2d794913dc.PNG)

위 그림에서 Movie를 예능이라고 생각하고 star에 해당하는 사람 중 유재석이 있다고 예를 들자. 그리고 그는 아래와 같이 계약을 맺었다.

1. 유재석, 무한도전, MBC
2. 유재석, 놀러와, MBC
3. 유재석, 런닝맨, SBS
4. 유재석, 해피투게더, KBS

특정한 star가 특정한 예능을 찍기 위해 특정한 studio와 계약을 했다. 그러면 Relationship인 Contract는 3가지의 entity에 영향을 받게 된다. 그리고 사람과 예능이 정해지면 studio는 자동으로 정해지기 때문에 studio쪽으로 arrow가 있다.

**Roles in Relationship**

하나의 entity의 정보에 따라 해석이(?) 다르면 그거도 way로 친다. 어려우니까 예시를 하나 들자. 아래의 그림에서 영화의 경우 첫 영화 다음 속편이 있는 경우가 있다. 그래서 original 영화와 sequel 영화의 접근이 relationship에게는 다른 것이다. 이 경우 2-way relationship이 된다.

![DB4_4](https://user-images.githubusercontent.com/43085342/55798429-d9e4e200-5b09-11e9-9852-7cee90dfcf4f.png)

뭔가 이해하기 어려우니까 예시를 하나 더 봅시다ㅏㅏ

![DB4_3](https://user-images.githubusercontent.com/43085342/55798134-2b40a180-5b09-11e9-869d-503f2be7de39.PNG)

교수님께서는 이거도 유재석을 예시로 설명해 주셨다. 유재석이 KBS와 전속계약을 맺었는데 MBC가 유재석과 무한도전이라는 프로그램을 하고 싶어한다. 그 경우 KBS가 허락을 해줘서 유재석은 MBC와 무한도전이라는 프로그램으로 계약을 하게 된다. 다른 프로그램의 경우 다 KBS지만 무한도전의 경우는 MBC인 것이다. 

**Attributes on Relationships**

Relationship에도 attribute가 붙을 수 있다. 이 attribute는 many to one relation에서 one에 해당하는 entity의 attribute가 하나일 경우 entity를 치우고 attribute만 붙일 수 있다. 

근데 이게 many to one일경우 다 치울 수 있는건지 attribute가 하나일 경우만 되는건지는 교수님께 여쭤보자ㅠ

![DB4_5](https://user-images.githubusercontent.com/43085342/55804201-cf7d1500-5b16-11e9-9d58-7bdbe4868e7a.PNG)

