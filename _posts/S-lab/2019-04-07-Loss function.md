---
layout: post
title: 손실함수와 경사하강법
published: True
category: S-lab
permalink: /S-lab/:year/:month/:day/:title/
---

## 손실함수, 경사하강법

------

### **학습**

훈련데이터로부터 가중치의 최적값을 찾는 행위.

학습 시 데이터는 훈련데이터와 시험 데이터(보지 못한 new data) 2가지의 데이터가 필요하다.

하나의 data set에만 지나치게 최적화(오버피팅)되는 문제는 조심해야 한다.



### 손실 함수

신경망이 학습할 수 있도록 해주는 지표. 출력값과 사용자가 원하는 출력값의 차이, 즉 오차를 말한다. 이 오차를 지표로 삼아 미분을 통해 매개변수(가중치, 편향)의 기울기를 개산하고, 그 값을 줄이는 방향으로 나아간다.

#### 평균 제곱 오차(Mean Squared Error : MSE)

$$
E = 1/2\sum_k(y_k - t_k)^2 \\
y_k:신경망이\ 추측한\ 값 \\
t_k:정답(0\ or\ 1)
$$

계산이 간편하여 가장 많이 사용되는 손실 함수이다. 위의 식에서는 1/2를 곱하는 것으로 되어 있으나 실제로는 2가 아닌 전체 데이터의 수를 나누어 1/n을 곱해주어야 한다.

거리 차이를 제곱하는 이유: 거리 차이가 작은 데이터와 큰 데이터 오차의 차이가 커진다. 어느 부분에서 오차가 크게 나는지 쉽게 확인할 수 있다.



#### 교차 엔트로피 오차(Cross Entropy Error: CEE)

$$
E=-\sum_kt_klny_k
$$

교차 엔트로피 오차는 기본적으로 분류(Classification) 문제에서 정답(1)은 하나뿐이고 나머지는 다 오답(0)인 `원-핫 인코딩(one-hot encoding)`했을 경우에만 사용할 수 있는 오차 계산법이다. 교차 엔트로피 오차는 정답일 때의 모델 값에 자연로그를 계산하는 식이 된다. 아래는 -log(y)의 그래프이다.

![img](https://t1.daumcdn.net/cfile/tistory/99384F355A928B1C06)

### 미니배치 학습(mini-batch training)

모든 data의 손실함수를 일일이 구하고 합하면 시간이 너무 많이 소요가 되기에 랜덤하게 어느정도 data를 표본으로 뽑아서 그 표본을 대상으로 손실함수를 구하는 방법이다.



### 경사법(Gradient Method)

손실함수의 값을 어떻게 해야 낮출 수 있을까? 이는 기울기를 통해 할 수 있다. 손실함수에서 기울기를 편미분한 값으로 구하면 얼마나 멀리 떨어져 있는지와 어느 방향으로 가야할 지를 구할 수 있다.
$$
y = w_0x +w_1 \\
w_0 = w_0 -\eta\frac{\partial f}{\partial w_0} \\
w_1 = w_1 -\eta\frac{\partial f}{\partial w_1} \\
f :비용함수\\
w_0, w_1:변수
$$
먼저 data의 값을 y = w0x + w1의 직선의 함수로 값을 예측한다. 그리고 이 직선을 data와의 값을 비교해서 오차를 구한다. 이를 비용함수라고 하는데 이 비용함수를 w0을 기준으로 정사영을 한 다음 그 함수를 기준으로 학습을 한다. 미분의 값이 0에 가까워 지도록 갱신을 하는 방식이다. 이를 w1에도 적용해서 비용함수의 값을 줄이는 방향으로 w0와 w1을 조정해 나간다.

위의 수식에서 n과 비슷하게 생긴 기호는 에타인데, 이는 학습률을 나타낸다. 학습률은 값을 얼마나 갱신할지를 결정한다. 학습률이 너무 크면 발산해버려서, 너무 작으면 거의 갱신이 안되서 제대로 학습을 하지 못하기 때문에 학습률의 적절한 조정이 필요하다.

하지만 경사하강법의 단점도 있다. 미분의 값이 0일 때 그 값이 최솟값일 수도 있지만 극솟값이나 안장점(어느 방향에서는 극대, 어느 방향에서는 극소의 값을 가짐)일 수도 있기 때문이다. 이러면 학습이 제대로 안되고 멈춰버릴 가능성이 있다.



### 손실함수와 경사하강법의 코드

손실함수

- 평균제곱오차

```python
import numpy as np

def mean_square_error(y, t):
    return 0.5 * np.sum((y-t)**2)
```

사용 결과

```python
>> t = [0,0,1,0,0,0,0,0,0,0]
>> y = [0.1,0.05,0.6,0.0,0.05,0.1,0.0,0.1,0.0,0.0]
>> mean_square_error(np.array(y),np.array(t))
0.097500000000000031
```



- 교차 엔트로피 오차

```python
import numpy as np

#평균 제곱오차
def mean_square_error(y, t):
    return 0.5 * np.sum((y-t)**2)

#교차 엔트로피 오차
def cross_entropy_error(y,t):
    delta = 1e-7
    return -np.sum(t*np.log(y+delta))

#출력 결과
>>t = [0,0,1,0,0,0,0,0,0,0]
>>y = [0.1,0.05,0.6,0.0,0.05,0.1,0.0,0.1,0.0,0.0]

>>cross_entropy_error(np.array(y),np.array(t))
0.51082545709933802

>> y = [0.1,0.05,0.0,0.0,0.05,0.7,0.0,0.1,0.0,0.0]
>> cross_entropy_error(np.array(y),np.array(t))
16.11809565095832
```



### 경사하강법

- 편미분

```python
def numerical_gradient(f,x):
    h = 1e-4
    grad = np.zeros_like(x) #x와 같은 형상의 배열을 생성

    for idx in range(x.size):
        tmp_val = x[idx]
        x[idx] = tmp_val + h
        fxh1 = f(x)

        x[idx] = tmp_val-h
        fxh2 = f(x)

        grad[idx] = (fxh1-fxh2) / (2*h)
        x[idx] = tmp_val #값 복원
    return grad

#2차원 벡터를 입력으로 받는 변수 2개의 다항함수
def function_2(x):
    return x[0]**2 + x[1]**2

#출력 결과
>> numerical_gradient(function_2, np.array([3.0,4.0]))
array([ 6.,  8.])
```



- 경사하강법(편미분 벡터 이용)

```python
def gradient_descent(f, init_x, lr=0.01, step_num=100):
    x = init_x
    for i in range(step_num):
        grad = numerical_gradient(f,x)
        x -= lr*grad
    return x

#출력 결과
>> init_x=np.array([3.0,4.0])
>> gradient_descent(function_2,init_x, lr=0.1, step_num=100)
array([  6.19392843e-21,   5.60497583e-21])

#실제 최솟값인 0,0과 비슷한 결과
```



**이미지 출처**

[김콜리님의 블로그](<https://kolikim.tistory.com/37?category=733477>)