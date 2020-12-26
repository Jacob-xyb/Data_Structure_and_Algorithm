# time 模块
- 从1970年1月1日0点0分开始按秒计时


```python
import time
```


```python
time.time() # 看看我们差距了多少秒呢
```




    1606744952.1809595



## 累计求和程序的运行时间检测
- 用time检测总运行时间


```python
def sum0fN2(n):
    start = time.time()  # 记录开始的时间
    theSum = 0
    for i in range(1, n+1):
        theSum += i
    end = time.time()  # 记录结束的时间
    return theSum, end - start

for i in range(5):  # 多次运行减少误差
    print("Sum is %d required %10.7f seconds"
         % sum0fN2(100000))  # 10W
```

    Sum is 5000050000 required  0.0034397 seconds
    Sum is 5000050000 required  0.0038462 seconds
    Sum is 5000050000 required  0.0039926 seconds
    Sum is 5000050000 required  0.0043566 seconds
    Sum is 5000050000 required  0.0049913 seconds
    


```python
# 增加十倍
for i in range(5):  # 多次运行减少误差
    print("Sum is %d required %10.7f seconds"
         % sum0fN2(1000000))  # 100W
```

    Sum is 500000500000 required  0.0384393 seconds
    Sum is 500000500000 required  0.0369604 seconds
    Sum is 500000500000 required  0.0391562 seconds
    Sum is 500000500000 required  0.0375578 seconds
    Sum is 500000500000 required  0.0401514 seconds
    


```python
# 再增加十倍
for i in range(5):  # 多次运行减少误差
    print("Sum is %d required %10.7f seconds"
         % sum0fN2(10000000))  # 1000W
```

    Sum is 50000005000000 required  0.3605776 seconds
    Sum is 50000005000000 required  0.3606791 seconds
    Sum is 50000005000000 required  0.3599532 seconds
    Sum is 50000005000000 required  0.3603742 seconds
    Sum is 50000005000000 required  0.3536611 seconds
    

- 用迭代的算法 数据量增加十倍，那么所花的时间大约增加十倍


```python
def sum0fN3(n):
    start = time.time()  # 记录开始的时间
    theSum = (n*(n+1))/2
    end = time.time()  # 记录结束的时间
    return theSum, end - start
for i in range(1,6):
    print("Sum is %d required %10.7f seconds"
             % sum0fN3(10000*(10**i)))
```

    Sum is 5000050000 required  0.0000000 seconds
    Sum is 500000500000 required  0.0000000 seconds
    Sum is 50000005000000 required  0.0000000 seconds
    Sum is 5000000050000000 required  0.0000000 seconds
    Sum is 500000000500000000 required  0.0000000 seconds
    

- 新算法运行时间几乎与数据量无关
