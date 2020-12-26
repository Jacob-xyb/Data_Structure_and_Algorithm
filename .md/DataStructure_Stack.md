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
    
