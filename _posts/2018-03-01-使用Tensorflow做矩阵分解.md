---
layout: post
title: 使用Tensorflow做矩阵分解
---


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
