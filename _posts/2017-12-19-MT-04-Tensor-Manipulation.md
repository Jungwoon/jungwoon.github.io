---
layout: post
title: "모두의 텐소플로우 #4 - Tensor 다루기"
image: '/assets/img/'
description: 'Tensor 다루기'
tags:
- tensorflow
- ToE
categories:
- tensorflow
- ToE
---


## Tensor 다루기

구글에서 진행했던 조대협님의 텐소플로우 강의와 [김성 교수님의 강의](https://hunkim.github.io/ml/)를 듣고
개인적으로 공부하면서 정리한 내용입니다

아래 소스는 김성 교수님 강의의 소스를 그대로 가져왔고, 수업을 들으면서 주석으로 정리한 부분입니다

---

#### 1차원

```python
import tensorflow as tf
import numpy as np

# 1차원
t = np.array([0., 1., 2., 3., 4., 5., 6.])
print(t.ndim) # 차원 -> 1차원 (dimension)
print(t.shape) # 개수(=크기) -> 7개
print(t[0]) # 배열의 인덱스와 같음
print(t[1]) # 배열의 인덱스와 같음
print(t[-1]) # 마지막 데이터 한개를 의미
print(t[2:5]) # idx[2] ~ idx[5-1]
print(t[4:-1]) # idx[4] ~ idx[마지막-1]
print(t[:2]) # ~ idx[2-1]
print(t[3:]) # idx[3] ~
```

실습

```
1
(7,)
0.0
1.0
6.0
[ 2.  3.  4.]
[ 4.  5.]
[ 0.  1.]
[ 3.  4.  5.  6.]
```

---

#### 2차원

```python
import tensorflow as tf
import numpy as np

# 2차원
t = np.array([[1., 2., 3.],
             [4., 5., 6.],
             [7., 8., 9.],
             [10., 11., 12.]]
            )

print(t.ndim) # 차원 -> 2차원
print(t.shape) # 개수 -> (4, 3) (=행, 열)
```

결과

```
2
(4, 3)
```

---

#### 다차원

```python
import tensorflow as tf
import numpy as np

t = np.array([
                [
                  [
                    [1, 2, 3, 4],
                    [5, 6, 7, 8],
                    [9, 10, 11, 12]
                  ],
                  [
                    [13, 14, 15, 16],
                    [17, 18, 19, 20],
                    [21, 22, 23, 24]
                  ]
                ]
            ])

print(t.shape)
```

결과

```
(1, 2, 3, 4)
```

---

### Matmul

행렬간 곱셈을 위해서는 '*'가 아닌 matmul 함수를 이용하여 곱셈을 해야 합니다.

```python
import tensorflow as tf
import numpy as np

matrix1 = tf.constant([[1., 2.], [3., 4.]]) # shape(2, 2)
matrix2 = tf.constant([[1.], [3.]]) # shape(2, 1)

tf.matmul(matrix1, matrix2).eval(session=tf.Session())
```

결과

```
array([[  7.],
       [ 15.]], dtype=float32)

```

---

### Broadcasting

Shape이 다르더라도 연산이 가능하도록 해준다, 가지고 있는 값을 이용하여 Shape을 맞춰춥니다.
(잘못 사용하면 예상하지 못한 값을 가져올 수 있기 때문에 사용함에 유의해야 한다)

![](https://i.stack.imgur.com/JcKv1.png)


---

### Axis

axis는 0부터 시작해서 안으로 들어갈때마다 하나씩 증가한다 (axis[-1]은 마지막 axis를 의미)

```
[[1., 2.]
 [3., 4.]]
```

일때, 
axis = 0 : 세로 -> (1, 3), (2, 4)
axis = 1 : 가로 -> (1, 2), (3, 4) (가장 안쪽)
axis = -1 : 가장 안쪽 -> (1, 2), (3, 4) (가장 안쪽)

---

### Reduce Mean

행렬에서 여러 값이 있을때 줄여 주는 역할, axis(=축)에 따라서 평균을 내어줌

![](https://cdn-images-1.medium.com/max/1000/1*Xfcz4xm4WeUS7FUs0afyFQ.png)

```python
import tensorflow as tf
import numpy as np

x = [[1., 2.],
     [3., 4.]]

print(tf.reduce_mean(x).eval(session=tf.Session()))

print(tf.reduce_mean(x, axis=0).eval(session=tf.Session())) # axis=0으로 reduce mean

print(tf.reduce_mean(x, axis=1).eval(session=tf.Session())) # axis=1로 reduce mean
```

결과

```
2.5
[ 2.  3.]
[ 1.5  3.5]
```

---

### Reshape(**)

텐소의 Shape를 변경할때 사용, 안에 shape에 원하는 값을 넣으면 해당 shape 맞게 조정된다.
보통은 가장 안쪽은 그대로 가져오고 바깥쪽 reshape 하게 된다.
`(** shape에 '-1'을 넣게 되면 변경에 맞춰서 값이 들어가게 된다)`

```python
import tensorflow as tf
import numpy as np

# reshape

t = np.array([[[0, 1, 2],
               [3, 4, 5]], 
              [[6, 7, 8],
               [9, 10, 11]]])

print(t.shape)

sess = tf.Session()
print(tf.reshape(t, shape=[-1, 3]).eval(session=sess))
print(tf.reshape(t, shape=[-1, 1, 3]).eval(session=sess))
```

결과

```
(2, 2, 3)

[[ 0  1  2]
 [ 3  4  5]
 [ 6  7  8]
 [ 9 10 11]]
 
[[[ 0  1  2]]

 [[ 3  4  5]]

 [[ 6  7  8]]

 [[ 9 10 11]]]
```

---

### One hot Encoding

데이터를 0과 1의 나열로 바꿔주는 것, depth에 따라서 Rank가 생김

```python
import tensorflow as tf
import numpy as np

tf.one_hot([[0], [1], [2], [0]], depth=3).eval(session=tf.Session())
```

결과

```
array([[[ 1.,  0.,  0.]],

       [[ 0.,  1.,  0.]],

       [[ 0.,  0.,  1.]],

       [[ 1.,  0.,  0.]]], dtype=float32)
```


---

### Casting

dtype으로 바꿔준다, 주로 True, False를 0과 1로 바꿔준다 (=Onehot Encoding)

```python
import tensorflow as tf
import numpy as np

print(tf.cast([1.8, 2.2, 3.3, 4.9], tf.int32).eval(session=tf.Session()))

print(tf.cast([True, False, 1 == 1, 0 == 1], tf.int32).eval(session=tf.Session()))
```

결과

```
[1 2 3 4]

[1 0 1 0]
```

---

### Stack

Stack 구조로 쌓아주는 의미

```python
import tensorflow as tf
import numpy as np

x = [1, 4]
y = [2, 5]
z = [3, 6]

tf.stack([x, y, z]).eval(session=tf.Session())
```

결과

```
array([[1, 4],
       [2, 5],
       [3, 6]], dtype=int32)
```

---

### Ones and Zeros like

주어진 shape에 0 or 1을 채운 행렬을 만들어줌

```python
import tensorflow as tf
import numpy as np

x = [[0, 1, 2],
     [2, 1, 0]]

print(tf.ones_like(x).eval(session=tf.Session())) # 1로 채운
print(tf.zeros_like(x).eval(session=tf.Session())) # 0으로 채운
```

결과

```
[[1 1 1]
 [1 1 1]]
 
[[0 0 0]
 [0 0 0]]
```

---

### Zips

복수개의 텐소를 한번에 추출 할때 사용

```python
import tensorflow as tf
import numpy as np

for x, y in zip([1, 2, 3], [4, 5, 6]): # x와 y에 담아서 처리
  print(x, y)
```

결과

```
(1, 4)
(2, 5)
(3, 6)
```