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



###装饰器

在不修改函数定义的情况下，代码运行期间动态增加功能的方式称为**装饰器**(Decorator)，其本质是一个可以返回函数的高阶函数。

**无参数传入例子：**

```python
def log(func):
    def wrapper(*args, **kw):
        print 'call %s():' % func.__name__
        return func(*args, **kw)
    return wrapper

@log  # 相当于 now = log(now)
def now():
    print '2013-12-25'
    
# >>> now()
# call now():
# 2013-12-25
```

**有参数传入例子：**

```python
def log(text):
    def decorator(func):
        def wrapper(*args, **kw):
            print '%s %s():' % (text, func.__name__)
            return func(*args, **kw)
        return wrapper
    return decorator

@log('execute')
def now():
    print '2013-12-25'
```

**注意事项：**

装饰器会改变 `func.__name__` 的值，若要维持原值，不能通过 `wrapper.__name__ = func.__name__` 而是需要 `functools.wraps` 装饰器。

```python
import functools

def log(func):
    @functools.wraps(func)   # 保持__name__为原值
    def wrapper(*args, **kw):
        print 'call %s():' % func.__name__
        return func(*args, **kw)
    return wrapper
```





> 参考资料：
>
> https://morvanzhou.github.io/tutorials/python-basic/
>
> https://www.liaoxuefeng.com/wiki/