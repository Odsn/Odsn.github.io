---
layout: post
title: 활성화 함수
published: True
category: S-lab
permalink: /S-lab/:year/:month/:day/:title/
---

## 활성화 함수

------

활성화 함수란 신호의 총합을 출력 신호로 변환하는 함수다.

(출처:http://cs231n.github.io/neural-networks-1/)

![img](https://t1.daumcdn.net/cfile/tistory/99DAD33359F1FD8A34)

**하나의 노드내에서 input data가 활성화함수에 의해 output data로 바뀌게 된다.**

#### 계단함수(step function)

   그래프 모양이 임계점(threshold)을 기준으로 0 또는 1의 값을 가지게 되는 함수.

   주로 퍼셉트론에서 사용한다.

![img](https://t1.daumcdn.net/cfile/tistory/996C203359F5EE5101)

![img](https://t1.daumcdn.net/cfile/tistory/99B3CD3359F5F56B36)

Step function의 구현

```python
import numpy as np
import matplotlib.pylab as plt

#계단함수
def step_function(x):
    y = x>0
    return y.astype(np.int)

x = np.arange(-5.0, 5.0, 0.1)
y = step_function(x)
plt.plot(x,y)
plt.ylim(-0.1,1.1)
plt.show()
```



#### 시그모이드 함수(sigmoid function)

   `step function`처럼 입력이 작으면 0, 크면 1인 것과 비선형 함수인 것은 같지만 더 매끄러운 그래프를 가짐.

   매끄러운 그래프가 신경망 학습의 key point다.

   **비선형 함수란** `y = ax + b` 의 꼴로 나오는 직선의 방정식 하나로 함수의 값을 정의할 수 없는 함수다.  선형함수로 활성화 함수를 하게 되면 은닉층의 의미가 없어진다.
   $$
   y(x) = h(h(h(x))) = c * c * cx = c^3x = c_2x
   $$


![img](https://t1.daumcdn.net/cfile/tistory/9932623359F5F26007)

![img](https://t1.daumcdn.net/cfile/tistory/99F5B53359F5F57D02)

Sigmoid Function의 구현

```python
import numpy as np
import matplotlib.pylab as plt

def sigmoid(x):
    return 1/(1+np.exp(-x))

x = np.arange(-5.0, 5.0, 0.1)
y = sigmoid(x)
plt.plot(x,y)
plt.ylim(-0.1,1.1)
plt.show()
```

#### 렐루함수(ReLU function)

`sigmoid function`의 `Gradient Vanishing` 문제를 해결하기 위해 최근에 많이 사용되는 함수.

**Gradient Vanishing 문제란** 0과 1사이의 값을 가지는 sigmoid function에서 아주 작은 값을 가질 경우 0에 매우 가까운 값을 가지게 되는 문제. 신경망에서 많은 은닉층을 거쳐가면서 0과 가까운 값을 가지게 되면 활성화 함수에 의해 0으로 값이 수렴되어 버린다.

![img](https://t1.daumcdn.net/cfile/tistory/9938873359F5F43C09)

![img](https://t1.daumcdn.net/cfile/tistory/99F8D13359F5F58F32)

Relu function의 구현

```python
import numpy as np
import matplotlib.pylab as plt

def relu(x):
    return np.maximum(0,x)

x = np.arange(-5.0, 5.0, 0.1)
y = relu(x)
plt.plot(x,y)
plt.ylim(-0.1,1.1)
plt.show()
```



#### 소프트맥스 함수(softmax funtion)

   위의 활성화 함수들은 은닉층에서 node들이 적용하는 함수라면 소프트맥스 함수는 출력츠에서의 node들이 적용하는 함수이다.
   $$
   y_k = e^{a_k}/ \sum_{i=1}^ne^{a_i} \\
   a_k : 은닉층에\ 있는\ k번째\ 노드의\ output
   $$
   위의 식에서 y_k의 값은 0~1 사이의 값을 가지고, y_k의 값을 다 합치면 1이 나오게 되기 때문에 softmax 함수의 값을 확률로 표현한다.

Softmax funtion 구현

```python
def softmax(x):
    c = np.max(x) #연산 숫자가 너무 커지는 경우 오버플로가 발생할 수 있음
    exp_x = np.exp(x-c) #오버플로 대책
    sum_exp_x = np.sum(exp_x)
    y = exp_x / sum_exp_a

    return y
```



**출처**

[활성화 함수 이미지](https://zamezzz.tistory.com/215)

[코드](<https://m.blog.naver.com/PostView.nhn?blogId=htk1019&logNo=220965622077&proxyReferer=https%3A%2F%2Fwww.google.com%2F>)

