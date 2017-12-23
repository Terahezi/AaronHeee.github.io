---
layout: post
title: "代码设计模式：工厂模式"
author: "Aaron He"
tags: 
  - Life in NUS
---

# 导语

代码量累加的过程，通常是一个“熵增”的过程，常出现的问题有：

* 如果需要添加新模块，则通过If-else实现，导致处处出现分支，拓展困难、阅读困难、易出错。
* 如果需要加入新的合作者，则合作者需要了解整个工程的所有代码，才能继续进行添加工作，导致开发效率低下。

而工厂模式则是解决这个问题：

**工厂模式**，是指依赖参数传入的不同，返回不同实例化函数的模式。

在工厂模式中，一个抽象静态类之下实现不同函数，仅暴露其接口，其他开发者不需要了解其实现细节，既方便功能拓展，又提高合作效率，与If-else相比更难出现错误。



# 工厂方法

实例：

首先使用 `@property` 来修饰产品函数

```python
Class JSONConnector:
    def __init__(self, filepath):
        self.data = dict()
        with open(filepath, mode='r', encoding='utf-8') as f:
            self.data = json.load(f)
            @property
            def parsed_data(self):
                return self.data
```

```python
Class XMLConnector:
    def __init__(self, filepath):
        self.tree = etree.parse(filepath)
        @property
        def parsed_data(self):
            return self.tree
```

再用工厂方法调用：

```python
def connection_factory(filepath):
    if filepath.endwith('json'):
        connector = JSONConnector
        elif filepath.endwith('xml'):
            connector = XMLConnector
            else:
                raise ValueError('Cannot connect to {}'.format(filepaht))
                return connector(filepath)
```



# 抽象工厂

**抽象工厂**设计模式是工厂模式的归纳。基本上，抽象工厂是一组 (逻辑上的) 工厂方法，这里每个工厂模式都负责生成一种不同的对象。





