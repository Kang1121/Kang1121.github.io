---
title: sorted 对 class 元素排序
date: 2020-02-12 19:56:02
tags:
categories:
- Practical Codes
---

### Example

```python
class unit(object):
    def __init__(self, value, label):
        self.value = value
        self.label = label
        
        
a = [unit(None, None) for i in range(1000)]

# 对a赋值后
# reverse = True 降序, 默认为 False 升序
a = sorted(a, key=lambda student: student.value, reverse = True)
```

### &emsp;&emsp;student 可随便换其他名字