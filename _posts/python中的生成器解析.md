通过python中的列表生成式，我们可以快速的生成一个包含多个元素的列表，但是如果这个列表的元素很多呢（比如100万个）？这种情况下，就会大量的占用宝贵的内存空间。因此我们需要一种更有效的方法来解决这个问题。此时，**生成器(generator)**应运而出。接下来我们就看一下，什么是生成器？

如果列表中的元素可以按照某种方式推算出来，那么我们就可以把这种计算方法保存下来。这种一边循环一边计算的机制就叫做生成器。首先介绍一种最简单的生成器。

```python
g = (x*x for x in range(5))
g
<generator object <genexpr> at 0x7ff60ff69990>

L = [x * x for x in range(5)]
L
[0, 1, 4, 9, 16]
```

从上边的例子中可以看出，将列表中的`[]`改为`()`，就可以将一个列表生成式转换为一个生成器。

那么如何打印出这个生成器的结果呢？如果要一个一个的打印出来，我们可以使用`next()`方法来进行。

```python
next(g)
0
next(g)
1
next(g)
4
next(g)
9
next(g)
16
>>> next(g)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

generator保存的是什么？generator保存的是算法，是计算方法！因此，只有每次使用`next()`方法调用时才能得到相应的结果。但是像这样频繁的调用next()方法太变态了，次数多了实在是不能忍。遇到这种情况，我们可以采用循环的方式来得到结果。

```python
for x in g:
    print(x)
0
1
4
9
16
```

从上边的结果中，大家可以发现当采用`for`循环时，不会出现StopIteration的错误。

说了这么多，我们发现上述的例子在与列表生成式相关，那么如果推算的算法很复杂，不能够用列表生成式来表达，那需要怎么办呢？在这种情况下，我们就需要采用函数的方式来解决这个问题。

以著名的斐波那契数列为例进行说明：

```python
def fib(max):
    n = 1
    a = 0
    b = 1
    while n < max:
        a,b = b,a+b
        print(b,end = " ")
        n+=1
    return 'done'
fib(15)
1 2 3 5 8 13 21 34 55 89 144 233 377 610 
done
```

上边的代码是一个正常的生成斐波那契数列的函数。在这里，我们只需要把`print`改成`yield`就变成了一个生成器。

```python
def fib(max):
    n = 1
    a = 0
    b = 1
    while n < max:
        a,b = b,a+b
        yield(b)
        n+=1
    return 'done'
fib(15)
<generator object fib at 0x7ff60ff69a98>
```

在这里有一点需要注意，那就是**生成器和函数的执行流程是不一样的**。函数是顺序执行，在遇到`return`语句或者在在执行完最后一行代码的时候就停止执行。而生成器是在没执行完一次`yield`语句就返回，下次执行时，从上次返回的`yield`处继续执行。

```python
for n in fib(12):
    print(n)
1
2
3
5
8
13
21
34
55
89
144
```

这种时候需要注意的时，当使用for循环来得到一个生成器的值时，是不返回`return`值的。

声明：部分内容总结自[廖雪峰老师的博客](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014317799226173f45ce40636141b6abc8424e12b5fb27000)，非常感谢廖老师的无私奉献！







