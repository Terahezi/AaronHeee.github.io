---
layout: post
title: "NAIS实验结束后的随想"
author: "Aaron He"
tags: 
  - Life in NUS
  - Machine Learning
---

第一次写 Machine Learning 的代码，第二次写Python代码，生疏得很。中途各种不顺，甚至犯一些低级错误。

在这里回想一下踩过的坑，以及接下来的应对措施~



# 代码结构踩过的坑

## 可以通过flag解决的代码非用两个文件：

* NAIS_prod.py 和 NAIS_concat.py 分成两个文件

## 强行复用他人代码：

* Sparse Matrix结构不如List，却偏偏直接复用参照代码导致结构复杂



# Python 中踩过的坑

## 基本语法踩坑：

* 注释疏忽，也可以说是没有做到**量名与语义唯一对应”**同一个变量名按照时间顺序反复赋予新值，如`self.W`既指attention的权重，也指attention权重的升维；`self.embedding_q`既是embedding层的item特征，也是该特征的升维……因为多指向，所以自己对代码注释的异常不敏感，从而让实验在排查这个bug上花费了很长时间，非常低级的错误。

* python基本语法还有一个细节就是，传值还是传地址。实际上，对于不可变量，如num, tupe传值。在这种情况下，赋值符号传递的是地址，当值发生新的改变时指向另一个地址。而对于可变变量，如list，传递地址之后，改变也是传址改变。

  ```python
  a = [[1, 2, 3], [4, 5, 6]]
  b = a[1]
  b[2] = 5
  # a = [[1, 2, 3], [4, 5, 5]]
  ```

*  for 的语法糖形式总是写不对

## 多线程踩坑：

* python 2.7 中多线程用`multiprocessing`库，其中`map`函数只能使用一个参数，不过我们可以使用**全局变量** 的技巧来实现多参数传入。想试一试python3.6+的多线程库，写了ML的程序才发现并行运算的重要性，这是以前写课程代码的我不能体会到的。
* `multiprocessing`库中的`apply_async`函数莫名效率很低，这一点还未解决



# TensorFlow踩过的坑：

## **版本变化的坑：**

* r0.1中没有`sequence_mask`函数，自己试着写发现写不出来，熟练运用一个框架，自己应该也能随手写小轮子才对，没有什么工具是百分百会随时趁手的。
* r0.1中的`tf.contrib.layers.log_loss`和r1.2中的`tf.losses.log_loss`参数labels prediction恰好是反过来的，完全不理解这样摆一道有啥意义。

## **TensorBoard的坑：**

- Graph无法显示，实际是`tensorboard --logdir="./"`的等于号周围不应该有空格

## **训练过程的坑：**

* 训练过程拎不清：

  将训练步数当作是周期，于是添加了多余的变量`index`，把参数弄得很复杂训练。是以**epoch**为一个周期，即数据集完全训练一遍为周期，训练完毕之后，用当前数据集全部进入模型计算**training_loss**，之后有选择性地收集测试样本，进行**testing_loss**和其他指标的计算。

  ```python
  for epoch in range(epochs):
      train_batch()
      training_loss()
      evaluate()
  ```

* 三维矩阵乘法：

  这种情况经常在batch_size导致的三维矩阵与权重的二维矩阵乘法中遇到，有两种解决办法，现在倾向于后者：

  2 -> 3 用`tf.tile`函数将二维权重矩阵扩展为三维。

  3 -> 2 将三维矩阵的前两维展开，用`tf.reshape`降维之后再与二维权重矩阵相乘。

鉴于踩过的坑很多，这段时间学到的也很多，有一些知识细碎，但是有用。我希望用键盘记录下来，既是纪念，也是以后继续写模型的一个参照。



# 矩阵维度注意事项：

> 一个好习惯： 注释中标记维度变化

## 数据维度类型：

标量：n
(行)向量：(n,)
(列)向量：(n,1)
矩阵：(m,n)

## Boardcast 机制

比拓展再计算更快：

  >  Broadcasting provides a means of vectorizing array operations so that looping occurs in C instead of Python. It does this without making needless copies of data and usually leads to efficient algorithm implementations.

维度匹配原则：

> 1. they are equal, or
> 2. one of them is 1

以及，维度匹配从最后一项开始。

维度匹配样例：

```python
A      (4d array):  8 x 1 x 6 x 1
B      (3d array):      7 x 1 x 5
Result (4d array):  8 x 7 x 6 x 5
  
A      (2d array):  5 x 4
B      (1d array):      4
Result (2d array):  5 x 4
```
计算实例：

```python
>>> a = np.array([0.0, 10.0, 20.0, 30.0])  #（4,）
>>> b = np.array([1.0, 2.0, 3.0])  #（3,）
>>> a[:, np.newaxis] + b  #（4,1）+（3,）= （4,3）
array([[  1.,   2.,   3.],
       [ 11.,  12.,  13.],
       [ 21.,  22.,  23.],
       [ 31.,  32.,  33.]])
```
## 升维与降维

##### `reduce_sum` 与 `newaxis` `expend_dims `

* `reduce_sum`可以用来降维，也可以用`keep_dims=True`保持原有维度
* `np[:,np.newaxis]`和`tf.expend_dims()`可以用来提升维度

##### `reshape`

* 若对(a,b,c)矩阵使用```reshape([-1,c])```，则按最低维度顺序展开
* 经典案例：qW + b 其中```q.shape = (batch_size, a, b)```, ```W.shape = (b, w)```




# 一些实用的工具

## **Python Profile 工具**

### Line_profiler 

- 对函数效率进行剖析，得到相对占比，特点是方便

  ```python
   @profile
   def slow_function(a, b, c):  #标记
  ```

  ```python
   $ kernprof -l script_to_profile.py   #生成
  ```

  ```python
   $ python -m line_profiler script_to_profile.py.lprof   #输出
  ```

### CProfile

  - 对函数效率进行剖析，特点是函数跟踪详细

    ```python
    import cProfile 

    def func_a(x): 
      return x ** 2 

    def func_b(x): 
      r = 0 for i in range(x): 
        r += func_a(i) 
        return r   #创建一个性能分析对象 

    cp = cProfile.Profile() #在调试环境下运行函数 
    print 'func result is %d' % cp.runcall(func_b,(10000)) #打印性能分析信息 
    cp.print_stats()
    ```


### **Sever 软件**

- ZOC 7

  一款配置ssh的服务器客户端，替代`ssh`命令
- Xftp 5

  一款服务器文件传输软件，替代`scp`命令

### **Linux 命令**

- `screen`

  保证服务器ssh断线之后，程序仍在后台运行。

  常用命令：离线：`screen -d` 恢复`screen -r` 
- `watch`

  将log文件映射在屏幕，动态刷新查看结果的工具

  常用命令：`watch`