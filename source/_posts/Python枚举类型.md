---
title: Python枚举类型
tags: python
comments: true
categories:
  - 技术
date: 2018-07-18 23:09:29
---


枚举类型可以看作是一种标签或是一系列常量的集合，通常用于表示某些特定的有限集合，例如星期、月份、状态等。

Python 的原生类型（Built-in types）里并没有专门的枚举类型，但是我们可以通过很多方法来实现它，例如字典、类等：
```python
MiracleLove = {'MON': '林志玲', 'TUS': '陈意涵', 'WEN': '张柏芝', 'THU': '辛芷蕾', 'FRI': '周冬雨'}

class MiracleLove:
   MON = '林志玲'
   TUS = '陈意涵'
   WEN = '张柏芝'
   THU = '辛芷蕾'
   FRI = '周冬雨'
```

上面两种方法可以看做是简单的枚举类型的实现。

如果只在局部范围内用到了这样的枚举变量是没有问题的。

但问题在于它们都是可变的（mutable），也就是说可以在其它地方被修改从而影响其正常使用：

<!--more-->

```python
MiracleLove['MON'] = MiracleLove['FRI']
print(MiracleLove)
```

通过类定义的枚举甚至可以实例化，变得不伦不类：
```python
ml = MiracleLove()
print(ml.MON)

MiracleLove.MON = 2
print(ml.MON)
```

当然也可以使用不可变类型（immutable），例如元组，但是这样就失去了枚举类型的本意，将标签退化为无意义的变量：
```python
MiracleLove = ('R', 'G', 'B')
print(MiracleLove[0], MiracleLove[1], MiracleLove[2])
```

为了提供更好的解决方案，Python 通过 PEP 435 在 3.4 版本中添加了 enum 标准库，3.4 之前的版本也可以通过 pip install enum 下载兼容支持的库。
enum 提供了 Enum/IntEnum/unique 三个工具，用法也非常简单，可以通过继承 Enum/IntEnum 定义枚举类型，其中 IntEnum 限定枚举成员必须为（或可以转化为）整数类型，而 unique 方法可以作为修饰器限定枚举成员的值不可重复：
```python
from enum import Enum, IntEnum, unique

try:
   @unique
   class MiracleLove(Enum):
       MON = '林志玲'
       TUS = '陈意涵'
       WEN = '张柏芝'
       THU = '辛芷蕾'
       FRI = '周冬雨'
except ValueError as e:
   print(e)
   
# duplicate values found in <enum 'MiracleLove'>: FRI -> MON
```

```python
try:
   class MiracleLove(IntEnum):
       MON = 1
       TUS = 2
       WEN = 3
       THU = 4
       FRI = '周冬雨'
except ValueError as e:
   print(e)

# invalid literal for int() with base 10: '周冬雨'
```

更有趣的是 Enum 的成员均为单例（Singleton），并且不可实例化，不可更改：
```python
class MiracleLove(Enum):
   MON = '林志玲'
   TUS = '陈意涵'
   WEN = '张柏芝'
   THU = '辛芷蕾'
   FRI = '周冬雨'

try:
   MiracleLove.MON = 2
except AttributeError as e:
   print(e)

# Cannot reassign members.
```

虽然不可实例化，但可以将枚举成员赋值给变量：
```python
mon = MiracleLove(0)
tus = MiracleLove(1)
wen = MiracleLove(2)
print(mon, tus, wen)

# MiracleLove.MON 
# MiracleLove.TUS 
# MiracleLove.WEN
```

也可以进行比较判断：
```python
print(mon is MiracleLove.MON)
print(mon == MiracleLove.MON)
print(mon is tus)
print(wen != MiracleLove.TUS)
print(mon == 0) # 不等于任何非本枚举类的值

# True
# True
# False
# True
# False
```

最后一点，由于枚举成员本身也是枚举类型，因此也可以通过枚举成员找到其它成员：
```python
print(mon.TUS)
print(mon.TUS.WEN.MON)

# MiracleLove.TUS
# MiracleLove.MON
```

但是要谨慎使用这一特性，因为可能与成员原有的命名空间中的名称相冲突：
```python
print(mon.name, ':', mon.value)

class Attr(Enum):
   name  = 'NAME'
   value = 'VALUE'

print(Attr.name.value, Attr.value.name)

# R : 0
# NAME value
```

### 总结：
---

enum 模块的用法很简单，功能也很明确，但是其实现方式却非常值得学习。如果你想更深入了解更多 Python 中关于 Class 和 Metaclass 的黑魔法，又不知道如何入手，那么不妨阅读一下 enum 的源码。