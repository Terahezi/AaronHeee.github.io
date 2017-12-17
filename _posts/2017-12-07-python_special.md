---
layout: post

title: "Python特殊语法集锦"

author: "Aaron He"

tags: 

-Machine Learning
---



### 三目运算 / 行内表达式

```python
var = var1 if condition else var2
```



### 函数可变参数

```python
def func(*param1)  #*代表可变长度参数前缀
```

*param1在函数中默认为 `tuple`



### 关键字参数

```python
def func(**param1)   #**代表关键字参数前缀
```

**param1 在函数中默认为 `dict`



### 全局变量

```python
a = None   # 外部定义全局变量
def fun():
    global a    # 声明全局变量
    a = 100      # 全局变量赋值
```



### import 语法

```python
import time    # 方法一，常规
time.time()   # 调用
```

```python
import time as t     # 方法二，别名
t.time()    # 调用
```

```python
from time import time, localtime  # 方法三，指定调用
time()   # 调用
```

```python
from time import *  # 方法四, 调用全部
time()   # 调用
```



### 错误处理

try: except … as …:

```python
try:
    Expression  # 会报错的代码
except Exception as e:  # 将报错存储在 e 中
    print(e)
```



### 匿名函数

```python
lambda param1,... : epression  # 相当于return, 且只有一句
```

配合 `map` 函数使用更佳：

```python
map( lambda x : x + 1, [1, 2, 3] )
```



###深浅拷贝

浅拷贝

```python
import copy
c = copy.copy(a)   # id(a) == id(c) is False
```

深拷贝

```python
c = copy.deepcopy(a)   # id(a) == id(c) is True
```

样例

```python
#copy.copy()
>>> a=[1,2,[3,4]]  # 第三个值为列表[3,4],即内部元素
>>> d=copy.copy(a) # 浅拷贝a中的[3，4]内部元素的引用，非内部元素对象的本身
>>> id(a)==id(d)
False
>>> id(a[2])==id(d[2])
True
>>> a[2][0]=3333  # 改变a中内部原属列表中的第一个值
>>> d             # 这时d中的列表元素也会被改变
[1, 2, [3333, 4]]

#copy.deepcopy()
>>> e=copy.deepcopy(a) # e为深拷贝了a
>>> a[2][0]=333 # 改变a中内部元素列表第一个的值
>>> e
[1, 2, [3333, 4]] # e中内部元素[]列表的值不会因为a中的值改变而改变
```

