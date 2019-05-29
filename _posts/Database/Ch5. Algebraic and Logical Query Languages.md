## Ch5. Algebraic and Logical Query Languages

-----------

### 1. Relational Operation on Bags

Relational algebra: operand가 relation인 algebra

Aggregate: sum, avg등

* Union, Intersection, Difference
* Projection
* Product
* Join



### 2. Extended Operators of Relational Algebra



* Distinct
* Aggregation(relational operator는 아님): Sum, Avg, Max, Min, Count
* Grouping: 어떤 attribute를 기준으로 group화 해서 sorting할 것인지 정함.
  * 
* Extended Projection
* Sorting
* Outerjoin



4/23

Nodejs 사용. 장점: javascript로 다 할 수 있 음 !

npm 등록된 패키지 수가 엄청 많음 잠이 옴

오라클 말고 MySQL 쓰기로 결정. Object Relation Mapping(ORM)옴 잠이 옴... 으갸갸갹

ORM만 쓰면 query를 잘 못만드니까 둘 다 해봅시당.

[TA님 github](https://github.com/rjxjr66/db-example)

localhost 3000

npm init(terminal)

index.js

package.json

"start": "node index.js"



도커파일 만드는 방법

Dockerfile로 이름을 설정. 시작할 때 어떤 베이스 이미지를 가지고 할지 정할 수 있음.

```
FROM node:8

WORKDIR /app

ADD ./package.json .

//source 코드는 제일 나중에 쓰는게 좋음
ADD ./index.js .


docker run -it 이미지이름:latest
```







Team Project: 