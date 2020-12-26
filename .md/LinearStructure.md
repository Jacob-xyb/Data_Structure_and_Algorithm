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

    def push(self, item):
        self.items.append(item)

    def pop(self):
        return self.items.pop()

    def peek(self):
        if not self.isEmpty():
            return self.items[len(self.items) - 1]

    def isEmpty(self):
        return self.items == []

    def size(self):
        return len(self.items)
```

## Stack案例：括号匹配

- 左右括号匹配是否成功。
- 左括号加入栈顶，右括号从栈顶移除。（移除时保证栈是非空的）



```python
def parChecker(symbolString):
    s = Stack()
    balanced = True
    index = 0
    while index < len(symbolString) and balanced:
        symbol = symbolString[index]
        if symbol == "(":
            s.push(symbol)
        else:
            if s.isEmpty():
                balanced = False
            else:
                s.pop()
        index += 1
    if balanced and s.isEmpty():
        return True
    else:
        return False
```


```python
print(parChecker("((()))"))
print(parChecker("()())"))
```

    True
    False
    

### Stack案例：更多种括号匹配

> 其实修改的地方不多

- 增加入栈左括号的类型

- 碰到右括号的时候需要判断是否与栈顶的最括号匹配


```python
def parChecker_x(symbolString):
    s = Stack()
    balanced = True
    index = 0
    while index < len(symbolString) and balanced:
        symbol = symbolString[index]
        if symbol in "([{":
            s.push(symbol)
        else:
            if s.isEmpty():
                balanced = False
            else:
                top = s.pop()
                if not matches(top, symbol):
                    balanced = False
        index += 1
    if balanced and s.isEmpty():
        return True
    else:
        return False

def matches(top, symbol):
    opens = "([{"
    closers = ")]}"
    return opens.index(top) == closers.index(symbol)
```


```python
print(parChecker_x("{[()]}"))
print(parChecker_x("[()]}"))
```

    True
    False
    

## Stack案例：十进制转二进制

- 辗转相除，先算出的余数就是最低位。



```python
def divideBy2(decNumber):
    remstack = Stack()
    while decNumber > 0:
        rem = decNumber % 2  # 求余
        remstack.push(rem)
        decNumber = decNumber // 2  # 取整
    binString = ""
    while not remstack.isEmpty():
        binString = binString + str(remstack.pop())
    return binString
```


```python
print(divideBy2(8))
print(divideBy2(42))
```

    1000
    101010
    

### Stack案例：十进制转十六一下任意进制

- 算法只需要将2进制转换成n就可以了。

- 主要是考虑如何表示十六进制


```python
def baseConverter(decNumber, base):
    digits = "0123456789ABCDEF"
    remstack = Stack()
    while decNumber > 0:
        rem = decNumber % base  # 求余
        remstack.push(rem)
        decNumber = decNumber // base  # 取整
    binString = ""
    while not remstack.isEmpty():
        binString = binString + digits[remstack.pop()]
    return binString
```


```python
print(baseConverter(8,2))
print(baseConverter(25,16))
```

    1000
    19
    

## Stack案例：中缀表达式转换为前缀和后缀形式

- 中缀：A + B * C 对应的后缀：A B C * +

- 中缀：A * B + C * D 对应的后缀： A B * C D * +

中缀转后缀：

>- 可以看出操作数ABC的顺序是没有改变的。
>
>- 操作符的与运算次序一致。

程序设计逻辑：

>- 用栈暂存操作符
>
>- 在遇到第二个操作数时不急着弹出栈顶的操作符，因为要比较操作符的优先顺序
>
>- 如果遇到括号，那么括号优先级最高，遇到匹配的右括号时，立马弹出栈顶操作符，直到碰到对应的左括号。


```python
def infixToPostfix(infixexpr):
    # 记录操作符优先级
    prec = {}  # 用字典记录
    prec["*"] = 3
    prec["/"] = 3
    prec["+"] = 2
    prec["-"] = 2
    prec["("] = 1
    # 记录操作符优先级
    opStack = Stack()  # 创建记录暂存操作符的空栈
    postfixList = []  # 创建记录结果的列表
    tokenList = infixexpr.split()  # 解析表达式到列表中（默认约定输入的表达式由空格隔开）
    # 算法部分
    for token in tokenList:
        if token in "ABCDEFGHIJKLMNOPQRSTUVWXYZ" or token in "0123456789":
            postfixList.append(token)  # 压入操作数
        elif token == "(":
            opStack.push(token)  # 遇到左括号就入栈
        elif token == ")":
            topToken = opStack.pop()  # 遇到右括号就弹出栈顶的操作符并记录下来
            while topToken != "(":
                postfixList.append(topToken)  # 如果弹出的操作符不是"(",就添加到结果列表中
                topToken = opStack.pop()  # 直到弹出的是"("为止
        else:
            while (not opStack.isEmpty()) and \
            (prec[opStack.peek()] >= prec[token]):
                postfixList.append(opStack.pop())
            opStack.push(token)
        
    while not opStack.isEmpty():  # 没有操作符和操作数后，将栈内的操作数全部弹出
        postfixList.append(opStack.pop())
    return " ".join(postfixList)
```


```python
print(infixToPostfix("A * B + C * D"))
```

    A B * C D * +
    


```python
print(infixToPostfix("( A + B ) * ( C - D ) - E"))
```

    A B + C D - * E -
    

## Stack案例：后缀表达式求值

- 需要注意：

- 先弹出的是右操作数，后弹出的是左操作数，这个对于```-``` ```/```很重要！



```python
def postfixEval(postfixExpr):
    operandStack = Stack()
    tokenlist = postfixExpr.split()
    for token in tokenlist:
        if token in "0123456789":
            operandStack.push(int(token))
        else:
            operand2 = operandStack.pop()
            operand1 = operandStack.pop()
            result = doMath(token, operand1, operand2)  # 先弹出的是右操作数
            operandStack.push(result)
    return operandStack.pop()

def doMath(op, op1, op2):
    if op == "+":
        return op1 + op2
    elif op == "-":
        return op1 - op2
    elif op == "*":
        return op1 * op2
    elif op == "/":
        return op1 / op2
    elif op == "**":
        return op1 ** op2
    elif op == "%":  # 取余
        return op1 % op2
    elif op == "//": # 取整
        return op1 // op2
```


```python
print(postfixEval("2 5 + 6 4 - * 4 -"))
```

    10
    

# 队列Queue

- 数据的添加总在一端发生（通常称为尾端“rear”）

- 数据的移除总在另一端发生（通常称为首端“front”）

- 这种次序安排的原则通常称为（FIFO：first-in-first-out）

> 例如排队，只有一个入口一个出口，不允许从中间移除数据项。

## Queue 操作定义

- Queue(): 创建一个空队列对象，不包含任何数据项。

- enqueue(item): 将item添加到队尾，无返回值。

- dequeue(): 从队首移除数据项，返回值为队首数据项，队列被修改。

- isEmpty(): 返回栈是否为空队列。

- size(): 返回队列中数据项的个数。

## 用python实现ADT Queue
- 将List首端作为队列尾端，List尾端作为队列首端。

> enqueue()复杂度为O(n);dequeue()复杂度为O(1)

- 如果首尾倒过来，复杂度也会倒过来。


```python
class Queue:
    def __init__(self):
        self.items = []
        
    def enqueue(self, item):
        self.items.insert(0, item)
    
    def dequeue(self):
        return self.items.pop()
    
    def isEmpty(self):
        return self.items == []
    
    def size(size):
        return len(self.items)
```

## Queue案例：约瑟夫问题


```python
# 约瑟夫问题
def hotPotato(namelist, num):
    simqueue = Queue()
    for name in namelist:
        simqueue.enqueue(name)
    while simqueue.size() > 1:
        for i in range(num):
            simqueue.enqueue(simqueue.dequeue())  # 一次传递
        simqueue.dequeue()  # 踢出被点到的人
    return simqueue.dequeue()
```

## Queue案例：打印机问题

- [ ] 是否看懂


```python
import random

class Printer:
    def __init__(self, ppm):
        self.pagerate = ppm  # 打印速度
        self.current_task = None  # 打印任务
        self.time_remaining = 0  # 任务倒计时

    def tick(self):  # 打印1秒
        if self.current_task != None:
            self.time_remaining -= 1
            if self.time_remaining <= 0:
                self.current_task = None

    def busy(self):  # 打印繁忙?
        if self.current_task != None:
            return True
        else:
            return False

    def startNext(self, newtask):  # 打印新作业
        self.current_task = newtask
        self.time_remaining = newtask.getPages() \
                              * 60/self.pagerate
class Task:
    def __init__(self, time):
        self.timestamp = time  # 生成时间戳
        self.pages = random.randrange(1, 21)  # 打印页数

    def getStamp(self):
        return self.timestamp

    def getPages(self):
        return self.pagespages

    def waitTime(self, currnttime):
        return currnttime - self.timestamp


def newPrintTask():  # 1/180 概率生成作业
    num = random.randrange(1, 181)
    if num == 180:
        return True
    else:
        return False


def simulation(numSeconds, pagesPerMinute):  # 主体函数 模拟
    labprinter = Printer(pagesPerMinute)
    printQuene = Queue()
    waitingtimes = []

    for currentSecond in range(numSeconds):  # 时间流逝
        if newPrintTask():
            task = Task(currentSecond)
            printQuene.enqueue(task)
        if (not labprinter.busy()) and \
                (not printQuene.isEmpty()):
            nexttask = printQuene.dequeue()
            waitingtimes.append(
                nexttask.waitTime(currentSecond))
            labprinter.startNext(nexttask)
        labprinter.tick()

    averageWait = sum(waitingtimes) / len(waitingtimes)
    print("Average Wait %6.2f secs %3d tasks remaining."
          % (averageWait, printQuene.size()))
```

# 双端队列Deque

- 自定义Deque类型时需要注意约定好哪一端是头，哪一端是尾。

## Deque 操作定义

- Deque(): 创建一个空双端队列对象，不包含任何数据项。

- addFront(item): 将item添加到队首，无返回值。

- addRear(item): 将item添加到队尾，无返回值。

- removeFront(): 从队首移除数据项，返回值为队首数据项，队列被修改。

- removeRear(): 从队尾移除数据项，返回值为队首数据项，队列被修改。

- isEmpty(): 返回deque是否为空。

- size(): 返回deque中数据项的个数。

## 用python实现ADT Deque
- 将List首端[0]作为队列尾端，List尾端[-1]作为队列首端。

> addFront/removeFront复杂度为O(1);addRear/removeRear复杂度为O(n)

- 如果首尾倒过来，复杂度也会倒过来。


```python
class Deque:
    """
    List下标0作为deque的尾端
    list下标-1作为deque的首端
    """
    def __init__(self):
        self.items = []

    def addFront(self, item):
        self.items.append(item)

    def addRear(self, item):
        self.items.insert(0, item)

    def removeFront(self):
        return self.items.pop()

    def removeRear(self):
        return self.items.pop(0)

    def iSEmpty(self):
        return self.items == []

    def size(self):
        return len(self.items)
```

## Deque案例：“回文词”判定


```python
def palchecker(aString):
    """
    先将需要判定的词从队首加入deque
    再从两端同时移除字符判定是否相同，
    直到deque中剩下0个或1个字符
    """
    chardeque = Deque()
    for ch in aString:
        chardeque.addFront(ch)
    stillEqual = True
    while chardeque.size() > 1 and stillEqual:
        first = chardeque.removeRear()
        last = chardeque.removeFront()
        if first != last:
            stillEqual = False
    return stillEqual
```


```python
print(palchecker("上海自来水来自海上"))
print(palchecker("山东落花生花落东山"))
```

    True
    True
    

# 列表List

- 其实被称为“无序表unordered list”，其中数据项只按照存放位置来索引。

## 链表实现：节点Node

- 每个节点至少要包含2个信息：**数据项**本身，以及指向下一个节点的**引用**信息。

- 注意：next为None的意义是没有下一个节点了。


```python
class Node:
    def __init__(self, initdata):
        self.data = initdata
        self.next = None

    def getData(self):
        return self.data

    def getNext(self):
        return self.next

    def setData(self, newdata):
        self.data = newdata

    def setNext(self, newnext):
        self.next = newnext
```

## 用python实现unordered list


```python
class Unorderedlist:

    def __init__(self):
        self.head = None

    def isEmpty(self):
        return self.head == None

    def add(self, item):  # 这里注意顺序一定不能乱，先设置暂存temp的next节点，再将表头指向temp
        temp = Node(item)
        temp.setNext(self.head)
        self.head = temp

    def size(self):
        """
        链表实现:size
        :return:
        """
        current = self.head
        count = 0
        while current != None:
            count += 1
            current = current.getNext()
        return count

    def search(self, item):
        """链表实现：查找"""
        current = self.head
        found = False
        while current != None and not found:
            if current.getData() == item:
                found = True
            else:
                current = current.getNext()
        return found

    def remove(self, item):
        current = self.head
        previous = None
        found = False
        while not found:
            if current.getData() == item:
                found = True
            else:
                previous = current
                current = current.getNext()
        if previous == None:
            self.head = current.getNext()
        else:
            previous.setNext(current.getNext())
```

## 用python实现OrderedList
- OrderedList(): 创建一个空的有序表
- add(item): 在表中添加一个数据项，并保持整体顺序，此项原不存在
- search(item): 在有序表中查找数据项，返回是否存在
- isEmpty(): 是否空表
- index(item): 返回数据项在表中的位置，此项应存在
- pop(): 移除并返回有序表中最后一项，表中应至少存在一项
- pop(pos): 移除并返回有序表中指定位置的数据项，此位置应存在


```python
class OrderedList:
    def __init__(self):
        self.head = None

    def iSEmpty(self):
        return self.head == None

    def size(self):  # 返回表中数据项的个数
        current = self.head
        count = 0
        while current != None:
            count += 1
            current = current.getNext()
        return count

    def remove(self, item):
        # remove(item): 从有序表中移除一个数据项，此项应存在，在有序表被修改
        current = self.head
        previous = None
        found = False
        while not found:
            if current.getData() == item:
                found = True
            else:
                previous = current
                current = current.getNext()
        if previous == None:
            self.head = current.getNext()
        else:
            previous.setNext(current.getNext())

    def search(self, item):
        current = self.head
        found = False
        stop = False
        while current != None and not found and not stop:
            if current.getData() == item:
                found = True
            else:
                if current.getNext() > item:
                    stop = Ture
                else:
                    current = current.getNext()
        return found

    def add(self, item):
        current = self.head
        previous = None
        stop = False
        while current != None and not stop:
            if current.getData() > item:
                stop = True
            else:
                previous = current
                current = current.getNext()

        temp = Node(item)
        if previous == None:
            temp.setNext(self.head)
            self.head = temp
        else:
            temp.setNext(current)
            previous.setNext(temp)
```
