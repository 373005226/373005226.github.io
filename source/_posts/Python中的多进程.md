---
title: Python中的多进程（multiprocessing）
author: 匿蟒
summary: 转载Cookiecutter文章，详解了Cookiecutter的安装及使用
date: 2020-4-24 10:42:00
categories:
    - 转载
tags:
    - python
    - 后端文章转载
---

> 本文转载于：https://note.qidong.name/2018/11/python-multiprocessing/
>
> 文章作者：[匿蟒](http://weibo.com/u/2837672545)
>
> 作者博客：https://note.qidong.name/

Python中的多线程、包括协程，由于CPython的[GIL](https://docs.python.org/3/glossary.html#term-global-interpreter-lock)（Global interpreter Lock ，全局解释锁）问题，只能实现并发（Concurrency），不能实现并行（Parallelism）。 因此，在并行计算场景，多进程是Python最简单的选择。

## Python多进程概念

概念，就是`class`。 了解概念，就会了解`class`之间的关系。

### Process

与普通的多进程类似，Python多进程的核心概念是[Process](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.Process)（进程）。 一个简单的进程使用示例如下：

```python
from multiprocessing import Process

def f(name):
    print('hello', name)

if __name__ == '__main__':
    p0 = Process(target=f, args=('alice',))
    p1 = Process(target=f, args=('bob',))
    p0.start()
    p1.start()
    p0.join()
    p1.join()
```

进程使用三部曲：

1. 创建[Process](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.Process)。
2. `start`，开始执行。
3. `join`，等待执行完毕。

### Pipe

[Pipe](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.Pipe)即管道，是Bash中最常见的跨进程通信手段。 `echo hello | tee stdout.log`，中间的`|`就是管道，把前一个进程的stdout传递给下一个进程。

[Pipe](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.Pipe)创建时，返回两个[Connection](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.connection.Connection)，前者负责[send](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.connection.Connection.send)而后者负责[recv](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.connection.Connection.recv)。 两个进程各执一端，就可以实现单向通信。

```python
from multiprocessing import Process, Pipe

def f(conn):
    conn.send([42, None, 'hello'])
    conn.close()

if __name__ == '__main__':
    receiver, sender = Pipe()
    p = Process(target=f, args=(sender,))
    p.start()
    print(receiver.recv())   # prints "[42, None, 'hello']"
    p.join()
```

如果在创建[Pipe](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.Pipe)时，指定`duplex=True`，比如`Pipe(True)`，两个[Connection](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.connection.Connection)即可实现双向通信。 默认`duplex=False`。

### Queue

[Queue](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.Queue)是一个基于标准模块[queue](https://docs.python.org/3/library/queue.html#module-queue)、包装了[Pipe](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.Pipe)的类。 它不仅具有先进先出（FIFO）的特性，还能实现跨进程通信。

```python
from multiprocessing import Process, Queue

def f(q):
    q.put([42, None, 'hello'])

if __name__ == '__main__':
    q = Queue()
    p = Process(target=f, args=(q,))
    p.start()
    print(q.get())    # prints "[42, None, 'hello']"
    p.join()
```

在实际使用中，除了少数简单场景外，都不会直接使用[Process](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.Process)、[Pipe](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.Pipe)、[Queue](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.Queue)来实现多进程。 这种低层级（low level，无贬义）的API，可读性差，容易出错。 常用的是高层级API——进程池。

### Pool

由于[Process](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.Process)创建、销毁有较大开销，并且并行数受机器CPU数量的限制，过多无益。 一个[Pool](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool)（进程池）会统一创建并维持一定数量的[Process](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.Process)，并行地执行Task。 在所有Task执行完毕后，再统一地关闭[Process](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.Process)。

这里Task（任务）的概念，并未被实现为一个`class`，而是一个`callable`，比如下面的`f`、`g`。

```python
#!/usr/bin/env python3
# -*- coding:utf-8 -*-
from multiprocessing.pool import Pool


def f(x):
    return x * x


def g(x, y):
    return x**y


def main():
    with Pool(4) as pool:
        result = pool.map(f, [1, 2, 3, 4, 5])
        print(type(result))
        print(result)

    with Pool(4) as pool:
        result = pool.starmap(g, [(1, 3), (2, 4), (3, 5), (4, 6), (5, 7)])
        print(type(result))
        print(result)


if __name__ == '__main__':
    main()
```

以上代码保存为`multi.py`文件，执行结果如下：

```shell
$ python3 multi.py
<class 'list'>
[1, 4, 9, 16, 25]
<class 'list'>
[1, 16, 243, 4096, 78125]
```

[map](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool.map)的用法类似内置函数`map`，专门处理单个参数的`callable`； 而[starmap](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool.starmap)则是用来处理多个参数的`callable`。

此外，还有利用[Pool](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool)执行单个Task的[apply](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool.apply)。 除非Task本身就是一个个来的，否则使用[apply](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool.apply)的效率不高。

比起[apply](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool.apply)，更值得关注的是[imap](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool.imap)和[imap_unordered](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool.imap_unordered)。 [imap](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool.imap)和[map](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool.map)非常类似，而这个多出来的`i`，则是`Iterable`。 [map](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool.map)使用的是`list`而[imap](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool.imap)则是`Iterable`，前者效率略高，而后者内存消耗显著的小。 在处理结果上，[imap](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool.imap)可以尽快返回一个`Iterable`的结果，而[map](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool.map)则需要等待全部Task执行完毕，返回`list`。 无论[map](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool.map)还是[imap](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool.imap)，都需要按顺序等待Task执行完成，而[imap_unordered](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool.imap_unordered)则不必。 [imap_unordered](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool.imap_unordered)返回的`Iterable`，会优先迭代到先执行完成的Task。 三者各有特点，要按需使用。

### AsyncResult

以上为进程池的同步使用方案。 同步方案会卡在[map](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool.map)或[starmap](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool.starmap)这一行，直到所有任务都执行完毕。 有时，我们会需要一个异步方案，这时就需要用到[map_async](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool.map_async)或[starmap_async](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool.starmap_async)。 它们返回的结果，就是[AsyncResult](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.AsyncResult)。

```python
#!/usr/bin/env python3
# -*- coding:utf-8 -*-
from multiprocessing.pool import Pool


def f(x):
    return x * x


def g(x, y):
    return x**y


def main():
    with Pool(4) as pool:
        result = pool.map_async(f, [1, 2, 3, 4, 5])
        print(type(result))
        print(result.get())

    with Pool(4) as pool:
        result = pool.starmap_async(g, [(1, 3), (2, 4), (3, 5), (4, 6), (5, 7)])
        print(type(result))
        print(result.get())


if __name__ == '__main__':
    main()
```

以上代码保存为`multi_async.py`文件，执行结果如下：

```shell
$ python3 multi_async.py
<class 'multiprocessing.pool.MapResult'>
[1, 4, 9, 16, 25]
<class 'multiprocessing.pool.MapResult'>
[1, 16, 243, 4096, 78125]
```

以上代码中，实际等待位置是`result.get()`那一行。

## Timeout

以上多进程代码，其实是不完善的。 除非Task非常简单，并无IO、网络等资源依赖，否则多进程也好、多线程也好，都有可能执行不完。 为了避免未知原因的挂起，及时止损，通常需要设置`timeout`。 [AsyncResult](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.AsyncResult)在阻塞时，可以用[wait](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.AsyncResult.wait)或[get](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.AsyncResult.get)，设置`timeout`参数。

```python
#!/usr/bin/env python3
# -*- coding:utf-8 -*-

import time
from multiprocessing.pool import Pool, TimeoutError


def sleep(duration):
    time.sleep(duration)
    with open('result.log', 'a') as file:
        file.write(str(duration))
        file.write('\n')
    return duration


def main():
    with Pool(4) as pool:
        result = pool.map_async(sleep, range(8))
        try:
            print(result.get(timeout=5))
        except TimeoutError:
            print(TimeoutError.__name__)


if __name__ == '__main__':
    main()
```

以上代码保存为`timeout.py`文件，执行结果如下：

```shell
$ python3 timeout.py
TimeoutError
$ cat result.log
0
1
2
3
4
```

可以看到，由于`timeout=5`，4秒以前的Task都成功了，而大于（等于）5秒的Task都失败了。

当[get](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.AsyncResult.get)需要等待所有进程结束时，需要在[Pool](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool)关闭以前。 因此，需要在`with`作用域中执行，否则将超时或（没设`timeout`）挂死。 如果使用[wait](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.AsyncResult.wait)，则[get](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.AsyncResult.get)可以在`with`以外获取结果。 因此，更**推荐使用**[wait](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.AsyncResult.wait)配合[get](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.AsyncResult.get)。

```python
def main():
    with Pool(4) as pool:
        result = pool.map_async(sleep, range(8))
        result.wait()
    try:
        print(result.get(9))
    except TimeoutError:
        print(TimeoutError.__name__)
```

替换`main()`，执行结果如下：

```shell
$ python3 timeout.py
[0, 1, 2, 3, 4, 5, 6, 7]
```

## 总结

前面提到Python做并行计算的选择，多进程[multiprocessing](https://docs.python.org/3/library/multiprocessing.html)只是最简单的一个选择。 另外还有两个常见选择： 一是使用其它解释器实现的Python，比如PyPy、Jython等； 二是使用C语言优化需要并行的代码，在Native层绕过GIL的限制； 三是使用协程（或线程）加[subprocess](https://docs.python.org/3/library/subprocess.html)，这也算是多进程的一个方案。 此外，确认代码是否真的会被GIL所影响，是首要工作。 如果代码中真正耗时的计算是在Native层执行——这在Python中非常常见，比如OpenCV——那么用多线程也没问题。

另外，要注意多进程的测试覆盖问题。 在另一个进程执行的代码，是无法被[coverage](https://pypi.org/project/coverage/)确认为已覆盖的。 需要对执行内容进行单独测试，或者在程序中预留未用多进程优化的原始方案。

其实，多进程带来的额外通信、切换开销，有时候也是很明显的。 还有个问题是，主进程被杀掉后，子进程会仍然存活，这在某些场景下会产生未知问题。 所以，在机器不是很强大的场景下，用原始的单线程串行方案，是最经济实用的选择。

## 参考

- [multiprocessing — Process-based parallelism — Python 3.7.1 documentation](https://docs.python.org/3/library/multiprocessing.html)
- [python - multiprocessing.Pool: What's the difference between map_async and imap? - Stack Overflow](https://stackoverflow.com/a/26521507/5588463)
- [谈谈Python协程技术的演进 - 知乎](https://zhuanlan.zhihu.com/p/30275154)