# 4个最简单但功能强大的结构
- 栈Stack
- 队列Queue
- 双端队列Deque
- 列表List

# 栈Stack
- 一种有次序的数据项集合，在栈中，数据项的加入和移除都仅发生在同一端

> 一端叫做栈顶“top”，另一端叫栈底“base”

- 距离栈底越近的数据项，留在栈中的时间就越长

> 而最新加入栈的数据项会被最先移除

- 这种次序通常称为"后进先出LIFO"：Last in First out

## Stack 操作定义

- Stack(): 创建一个空栈，不包含任何数据项。
- push(item): 将item加入栈顶，无返回值。
- pop(): 将栈顶数据项移除，并返回，栈会修改。
- peek(): “窥视”栈顶数据项，返回栈顶的数据项但不移除，栈不被修改。
- isEmpty(): 返回栈是否为空栈。
- size(): 返回栈中有多少个数据项。

## 用python实现ADT Stack
- 将List的一端（index=0）设置为栈底，另一端(index=-1)设置为栈顶，push/pop 复杂度为O(1)。

- 如果(index=0)设置为栈顶的话， push/pop 复杂度为O(n)。


```python
class Stack:
    """模拟栈"""
    def __init__(self):
        self.items = []

    def isEmpty(self):
        return self.items == []

    def push(self, item):
        self.items.append(item)

    def pop(self):
        return self.items.pop()

    def peek(self):
        if not self.isEmpty():
            return self.items[len(self.items) - 1]

    def size(self):
        return len(self.items)
```
