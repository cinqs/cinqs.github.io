---
layout: post
title: Good old algos and techs
tags: [blog, algorithms]
---

- **模拟退火 / simulated annealing**

  模拟退火技术在每一步都以一定的概率接受比当前更差的结果，以此来跳出局部解，或者是探索更优解。需要注意的是，模拟退火的迭代过程中，接受更差结果的概率应当
  逐渐降低，从而保证算法的收敛效果
 
  应用：
 
  * 神经网络参数寻优避免局部最小的应用
  * 强化学习中，智能体在执行（Exploitation）和探索（Exploration）方面的妥协

- **遗传算法 / Genetic algorithm**

  遗传算法在原理上跟模拟退火一样属于直觉式，启发式的算法类型。通过在模拟生物遗传进化过程交叉变异过程，加速在开放域维度上的最适点寻找。相比于贪婪型的
  grid search效率不知道高到哪里去。至今遗传算法仍然在大量的有意识和无意识的使用。OpenAI在2017年发布的Evolution Strategy的paper，就是收遗传算的启发，
  其实从某种角度来说，Evolution strategy就是遗传算法的一种。
  
  应用：
  
  * 非常广！
  * 除了基于BP的神经网络之外，很多人也在研究基于遗传算法的神经网络
  
    + [https://arxiv.org/pdf/1711.07655.pdf](https://arxiv.org/pdf/1711.07655.pdf)
    + [http://homepages.inf.ed.ac.uk/pkoehn/publications/gann94.pdf](http://homepages.inf.ed.ac.uk/pkoehn/publications/gann94.pdf)
