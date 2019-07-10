---
layout: post
title: Python中的异常处理
no-post-nav: true
category: it
tags: [it]
keywords: Exception
excerpt: 没有绝对的异常，只有人为的错误。
---
# Python中的异常处理

## 错误

- 语法上的
- 逻辑上的
- 遇到错误后，解释器会引发异常。如果异常对象并未被处理或捕捉，程序就会用所谓的回溯（traceback）终止执行。

## 异常处理语句 try...except...finally

   - `except`语句不是必须的，`finally`语句也不是必须的，但是二者必须要有一个，否则就没有`try`的意义了。

   - `except`语句可以有多个，Python会按`except`语句的顺序依次匹配你指定的异常，如果异常已经处理就不会再进入后面的`except`语句。

   - `except`语句可以以元组形式同时指定多个异常。

   - `except`语句后面如果不指定异常类型，则默认捕获所有异常，你可以通过logging或者sys模块获取当前异常。

     ```python
     import logging
     try:
         do_work()
     except:    
         # get detail from logging module
         logging.exception('Exception caught!')
         
         # get detail from sys.exc_info() method
         error_type, error_value, trace_back = sys.exc_info()
         print(error_value)
         raise
     ```

   - 如果要捕获异常后要重复抛出，请使用`raise`，后面不要带任何参数或信息。

   - 不建议捕获并抛出同一个异常，请考虑重构你的代码。

   - 不建议在不清楚逻辑的情况下捕获所有异常，有可能你隐藏了很严重的问题。

   - 尽量使用内置的异常处理语句来替换`try/except`语句，比如`with`语句，`getattr()`方法。

   ## 抛出异常

   ​使用__raise__关键字可自主抛出一个异常，等同于Java和C#中的throw；

```python
raise NameError("bad name!")
```

`raise`关键字后面可以指定你要抛出的异常实例，一般来说抛出的异常越详细越好，Python在`exceptions`模块内建了很多的异常类型，通过使用`dir()`函数来查看`exceptions`中的异常类型，如下：

```python
import exceptions

print dir(exceptions)
# ['ArithmeticError', 'AssertionError'...]
```

## 自定义异常类型

直接从Exception类继承即可。

##异常处理时常见的问题

###Exception和BaseException

当要捕获一个通用异常时，应该用`Exception`还是`BaseException`？以下是它们之间的继承关系。

```python
BaseException
 +-- SystemExit
 +-- KeyboardInterrupt
 +-- GeneratorExit
 +-- Exception
      +-- StopIteration...
      +-- StandardError...
      +-- Warning...
```

程序在捕获所有异常时更应该使用`Exception`而不是`BaseException`，因为被排除的三个异常属于更高级别的异常，合理的做法应该是交给Python的解释器处理。

### 使用内置的语法范式代替try/except

Python 本身提供了很多的语法范式简化了异常的处理，比如`for`语句就处理了的`StopIteration`异常，让你很流畅地写出一个循环。

`with`语句在打开文件后会自动调用`finally`并关闭文件。我们在写 Python 代码时应该尽量避免在遇到这种情况时还使用try/except/finally的思维来处理。

```
# should not
try:
    f = open(a_file)
    do_something(f)
finally:
    f.close()

# should 
with open(a_file) as f:
    do_something(f)
```

当我们需要访问一个不确定的属性时，有可能你会写出这样的代码：

```python
try:
    test = Test()
    name = test.name  # not sure if we can get its name
except AttributeError:
    name = 'default'
```

其实我们可以使用更简单的`getattr()`来达到目的。

```python
name = getattr(test, 'name', 'default')
```

## 异常速查表

![](https://haoforward.github.io/assets/images/2019/it/Exception_table.jpg)

## 异常处理好习惯

- 只处理你知道的异常，尽量避免捕获所有异常然后吞掉它们。
- 抛出的异常应该说明原因，有时候你知道异常类型也猜不出所以然。
- 不要使用异常来控制流程，那样你的程序会无比难懂和难维护。
- 如果有需要，切记使用`finally`来释放资源。
- 如果有需要，请不要忘记在处理异常后做清理工作或者回滚操作。

注：以上内容摘自betacat 《[总结：Python中的异常处理](https://segmentfault.com/a/1190000007736783)》，内容有些许删改。