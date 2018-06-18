---
layout: post
title: "모두의 텐소플로우  #1 - Machine Learning 개념 정리 & Linear Regression"
image: '/assets/img/'
description: 'Machine Learning 개념 정리'
tags:
- tensorflow
- ToE
categories:
- tensorflow
- ToE
---


## Machine Learning 개념 정리

구글에서 진행했던 조대협님의 텐소플로우 강의와 김성 교수님의 강의를 듣고 개인적으로 공부하면서 정리한 내용입니다

### 머신러닝?

데이터를 기반으로 컴퓨터를 학습 시켜서 예측하게 만드는 기법

### 종류

- 지도 학습 (Supervised Learning)
    - 문제와 문제에 대한 답을 알려주고 정답을 구하도록 하는 방법, 정확도 측정이 가능
- 비지도 학습 (Unsupervised Learning)
    - 특정 데이터를 학습 시키고 알아서 패턴을 알게 하는 방법, 정확도 측정이 어려움
- 강화 학습 (Reinforcement Learning)
    - 데이터가 없이 환경만 구축하여 모델이 돌면서 학습을 하게 함
    

### 머신러닝에서 중요한 거

1. 데이터
2. 파이프 라인
3. 아이디어(**)


### 머신러닝의 대표적인 문제

- Classfication : 입력값에 대한 결과값이 연속적이지 않고 몇개의 종류로 딱딱 나눠서 끊어지는 결과가 나오는 것, 예를 들어서 사물 인식이 있다
- Regression : 결과값이 연속성을 가지고 있을때 Regression 문제라고 한다, 예를 들어서 진행거리에 따른 택시 요금이 대표적인 케이스가 될 수 있다


### 인공 지능망 (Neural Network)

머신러닝 모델 중 인공 지능망이라는 모델이 있었는데, 발표 초기에는 계층을 깊게 들어가면 갈수록 계산이 복잡하여 연산이 불가능하다는 것때문에
큰 관심을 가지지 못하다가 Hinton 교수의 논문을 통해서 뉴럴 네트워크에 입력하는 초기값을 제대로 입력하면 깊은 계층의 레이어도 연산이
가능하다는 것이 증명되었고 Yosua Bengio 라는 분의 논문을 통해서 복잡한 문제를 풀 수 있는걸 증명하였다


### 머신 러닝의 순서

1. 데이터 수집 
2. 피쳐 정의
3. 가설 정의 (Hypothesis)
4. 코스트 함수 & 옵티마이저
5. 학습 (데이터의 70%)
6. 모델 평가 (데이터의 30%)
6. 예측

---


## Tensorflow

여기서는 기본적인 텐소플로우의 대한 설명을 하도록 하겠습니다. 기본적으로 텐소플로우는 `Tensor`라는 형태로 저장이 되기 때문에,
반드시 `Session`을 열어서 `Session에 담아서 연산을 해야 합니다` 

### 자료형

- Constant : 상수
    - 선언 : tf.constant(값, dtype=데이터 타입, shape=[n,n], name='이름')
    - 예시 : tf.constant([5], dtype=tf.float32, shape=[1, None], name='Hello')
- Variables : 변수로 값의 변화는 텐소가 연산되면서 바뀌게 됩니다.
    - 선언 : tf.Variable(값, name='이름')
    - 예시 : tf.Variable([1, 2, 3, 4, 5], name='weight')
- PlaceHolder : 여러가지 데이터 타입을 닮을 수 있는 자료형으로 초기값 없이 선언하고, 나중에 `feed_dict`을 통해 데이터를 주입한다
    - 선언 : tf.placeHolder(dtype=데이터 타입, shape=[n,n], name='이름')
    - 예시 : tf.placeholder(tf.float32, shape=None, name='Hello')
    

### 텐서플로우의 연산

계산 요청이 오면 아래와 같이 내부적으로 `방향성 그래프`가 생성이 되는데 이게 곧 하나의 Session이 됩니다

텐소플로우는 이렇게 `방향성 그래프`를 통해서 연산이 되기 때문에 반드시 Session에 값을 넣어서 연산을 해주어야 합니다.

![](https://cdn-images-1.medium.com/max/1600/1*e3O1NudNNly4YLwETaPzEw.png)


---

## Linear Regression (선형 회귀)

연속적인 데이터에 대해서 예측을 할때 이용하며, 과거의 데이터(빨간점)과의 모델(선)과의 거리가 가장 짧은 W(=Weight)와 b(=bias)를 찾는게 목적입니다.

![](https://upload.wikimedia.org/wikipedia/commons/thumb/e/e9/Thiel-Sen_estimator.svg/1920px-Thiel-Sen_estimator.svg.png)

어려운 수학식이나 이런부분 다 제외합니다(물론 제가 잘 몰라서 입니다!!)

바로 Tensorflow로 실습을 해봅니다

```python
import tensorflow as tf

# 변수 설정, session이 돌면서 W와 b의 값이 변한다
W = tf.Variable(tf.random_normal([1]), name='weight') # tf.random_normal() : 정규분포를 따르는 난수로 텐서를 생성
b = tf.Variable(tf.random_normal([1]), name='bias')
 
X = tf.placeholder(tf.float32, shape=[None])
Y = tf.placeholder(tf.float32, shape=[None])

# Our Hypothesis H(x) = Wx + b
hypothesis = X * W + b
b = tf.Variable(tf.random_normal([1]), name='bias')

# cost/Loss function
cost = tf.reduce_mean(tf.square(hypothesis - Y)) # tf.reduce_mean() : 지정한 차원을 따라 평균을 계산

# Optimizer
optimizer = tf.train.GradientDescentOptimizer(learning_rate = 0.01) # GradientDescent 알고리즘을 이용하여 최소화 / learning_rate는 기울기가 이동하는 크기
train = optimizer.minimize(cost)

# Session
sess = tf.Session()
sess.run(tf.global_variables_initializer()) # for use Variable

for step in range(2001):
  # feed_dict을 통해서 X와 Y에 변수를 넣어줍니
  cost_val, W_val, b_val, _ = sess.run([cost, W, b, train], feed_dict={X: [1, 2, 3, 4, 5], Y: [2.1, 3.1, 4.1, 5.1, 6.1]})
  
  if step % 20 == 0:
    print(step, cost_val, W_val, b_val)
    
```



