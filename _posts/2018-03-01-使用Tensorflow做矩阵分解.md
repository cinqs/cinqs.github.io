---
layout: post
title: 使用Tensorflow做矩阵分解
tags: [blog, tensorflow]
---

>  一些简单的隐语义模型的场景中需要使用到矩阵分解，目的是为了找到前向和后向的隐类。这些经常出现在协同过滤中

>  除了下面的这种矩阵中没有缺失元素的分解，还可以分解缺失元素的矩阵

> 需要做的修改为：

> 1. loss只计算未缺失的元素的差异

> 当未缺失的元素的loss已经降到最小了，使用新生成的矩阵来代替原有矩阵的缺失的元素，就找到了前向与后向之间的关系

```py
import tensorflow as tf
import numpy as np
from matplotlib import pyplot as plt

a = np.array([0, 1] * 18).reshape((6, 6))

k = 6; tf.reset_default_graph()
with tf.variable_scope('test') as scope:
    p = tf.get_variable(name='p', shape=[a.shape[0], k], dtype=tf.float32)
    q = tf.get_variable(name='q', shape=[k, a.shape[1]], dtype=tf.float32)

    truth = tf.constant(a); result = tf.matmul(p, q)

    mse_loss = tf.losses.mean_squared_error(truth, result)

    regu = tf.contrib.layers.l2_regularizer(scale=0.1)
    reg_loss = tf.contrib.layers.apply_regularization(regu, [p, q])

    loss = mse_loss + reg_loss

    step = tf.train.AdamOptimizer(0.01).minimize(loss)


sess = tf.Session()
sess.run(tf.global_variables_initializer())
for _ in range(5000):
    l, r, _ = sess.run([loss, result, step])
    print(l, r[0,0])

plt.matshow(a)
plt.colorbar()
plt.title('a matrix color')

plt.matshow(r)
plt.colorbar()
plt.title('r matrix color')
```

![矩阵分解结果](/images/a.png)
![矩阵分解结果](/images/r.png)
