# 8. Errors and Exceptions 
> 에러와 예외들~

## 8.1. Syntax Errors
* = parsing errors
* Q: 어디가 틀렸을까요~?
```python
>>> while True print('Hello world')
  File "<stdin>", line 1, in ?
    while True print('Hello world')
                   ^
SyntaxError: invalid syntax
```
* 이와 같이 ^ 표시가 되는데 구문이 틀린 곳은 그 정확하게 그 곳 또는 그 앞 부분
* 에러메세지에는 에러가 발견된 파일과 #line 안내됨

## 8.2 Exceptions
* = 실행시 발견되는 오류 (무조건 치명적인 오류는 아님)
```python
>>> 10 * (1/0)
Traceback (most recent call last):
  File "<stdin>", line 1, in ?
ZeroDivisionError: division by zero
>>> 4 + spam*3
Traceback (most recent call last):
  File "<stdin>", line 1, in ?
NameError: name 'spam' is not defined
>>> '2' + 2
Traceback (most recent call last):
  File "<stdin>", line 1, in ?
TypeError: Can't convert 'int' object to str implicitly
```
* 에러메세지로 출력되는 exception 이름들은 built-in exception
  * exception 이름들은 예약어(reserved keyword)는 아님.
* exception 추가 가능
* 이제 이 장의 나머지 부분은 이 exception의 종류와 원인에 대해 설명
* [built-in Exception](https://docs.python.org/3/library/exceptions.html#bltin-exceptions)

## 8.3 Handlig Exceptions
* Q: 어떤 exception이 작동할까요? (힌트: 2개)
```python
>>> while True:
...     try:
...         x = int(input("Please enter a number: "))
...         break
...     except ValueError:
...         print("Oops!  That was no valid number.  Try again...")
```
* python은 기본적으로 개발자가 Control-C로 프로그램을 종료시킬 수 있음
  * [KeyboardInterrupt exception](https://docs.python.org/3/library/exceptions.html#KeyboardInterrupt)
* try 구문은 여느 프로그래밍 언어들과 같은 방식으로 작동
* Q: exception이 여러 개 일때는 어떻게 작성할까?
```python
... except (RuntimeError, TypeError, NameError):
...     pass
```
* 또는, 
```python
import sys

try:
    f = open('myfile.txt')
    s = f.readline()
    i = int(s.strip())
except OSError as err:
    print("OS error: {0}".format(err))
except ValueError:
    print("Could not convert data to an integer.")
except:
    print("Unexpected error:", sys.exc_info()[0])
    raise
```
* 아무 exception 이름을 작성하지 않으면 wildcard와 같이 취급
* 특이하게도 이 try-except 문에는 else 항목이 있음
* 다음 코드는 어떻게 실행될까?
```python
for arg in sys.argv[1:]:
    try:
        f = open(arg, 'r')
    except IOError:
        print('cannot open', arg)
    else:
        print(arg, 'has', len(f.readlines()), 'lines')
        f.close()
```
* 실행 영역
```
try: 
  예외를 유발할 수 있는 구문
except <Error명>:
  예외 처리를 수행하는 구문
else:
  예외가 발생하지 않을 경우 수행할 구문
```
* Exception 발생시 exception argument 라는 exception 값이 있을 수 있음.
  * Built-in Exception의 경우 이 값이 있음
```python
>>> try:
...     raise Exception('spam', 'eggs')
... except Exception as inst:
...     print(type(inst))    # the exception instance
...     print(inst.args)     # arguments stored in .args
...     print(inst)          # __str__ allows args to be printed directly,
...                          # but may be overridden in exception subclasses
...     x, y = inst.args     # unpack args
...     print('x =', x)
...     print('y =', y)
...
<class 'Exception'>
('spam', 'eggs')
('spam', 'eggs')
x = spam
y = eggs
``` 
* __str__ 은 exception 인스턴스를 직접 출력해줌
* Exception 구문은 함수 내부에 대해 간접적으로도 exception을 잡음
```python
>>> def this_fails():
...     x = 1/0
...
>>> try:
...     this_fails()
... except ZeroDivisionError as err:
...     print('Handling run-time error:', err)
...
Handling run-time error: int division or modulo by zero
```

## 8.4 Raising Exceptions
* Raise 구문은 개발자가 exception을 발생하도록 하는 구문
* 문법: raise 예외인스턴스 | 예외 클래스 (데이터)
```python
>>> raise NameError('HiThere')
Traceback (most recent call last):
  File "<stdin>", line 1, in ?
NameError: HiThere
```
* 만약, exception을 발생시키되 이 exception을 처리하지 않길 원한다면 try-except 문에 넣어 exception을 re-raise 시킬 수 있음
```python
>>> try:
...     raise NameError('HiThere')
... except NameError:
...     print('An exception flew by!')
...     raise
...
An exception flew by!
Traceback (most recent call last):
  File "<stdin>", line 2, in ?
NameError: HiThere
```

## 8.5 User-definced Exceptions
* 새로운 exception을 정의하고 싶으면 Exception class를 상속받아 만들면 됨
```python
>>> class MyError(Exception):
...     def __init__(self, value):
...         self.value = value
...     def __str__(self):
...         return repr(self.value)
...
>>> try:
...     raise MyError(2*2)
... except MyError as e:
...     print('My exception occurred, value:', e.value)
...
My exception occurred, value: 4
>>> raise MyError('oops!')
Traceback (most recent call last):
  File "<stdin>", line 1, in ?
__main__.MyError: 'oops!'
```
* 위의 오버라이딩한 __init__ 함수는 arg 생성 대신 value 값 생성을 하게 됨
* Exception class는 보통 간단하게 만듦
  * 일반적으로 base class를 생성하고, 이를 상속받아 원하는 exception class들을 정의
  * class 이름은 보통 Error로 끝나는 exception 명 사용
```python
class Error(Exception):
    """Base class for exceptions in this module."""
    pass

class InputError(Error):
    """Exception raised for errors in the input.

    Attributes:
        expression -- input expression in which the error occurred
        message -- explanation of the error
    """

    def __init__(self, expression, message):
        self.expression = expression
        self.message = message

class TransitionError(Error):
    """Raised when an operation attempts a state transition that's not
    allowed.

    Attributes:
        previous -- state at beginning of transition
        next -- attempted new state
        message -- explanation of why the specific transition is not allowed
    """

    def __init__(self, previous, next, message):
        self.previous = previous
        self.next = next
        self.message = message
```

## 8.6 Defining Clean-up Actions
* Q 저 finally는 언제 실행될까요?
```python
>>> try:
...     raise KeyboardInterrupt
... finally:
...     print('Goodbye, world!')
...
Goodbye, world!
KeyboardInterrupt
Traceback (most recent call last):
  File "<stdin>", line 2, in ?

```
* finally는 try 안의 코드가 exception이 있든 없든 실행되는 영역
* try 영역안에서 break, continue, return 이 호출되고 나서도 finally는 실행됨
```python
>>> def divide(x, y):
...     try:
...         result = x / y
...     except ZeroDivisionError:
...         print("division by zero!")
...     else:
...         print("result is", result)
...     finally:
...         print("executing finally clause")
...
>>> divide(2, 1)
result is 2.0
executing finally clause
>>> divide(2, 0)
division by zero!
executing finally clause
>>> divide("2", "1")
executing finally clause
Traceback (most recent call last):
  File "<stdin>", line 1, in ?
  File "<stdin>", line 3, in divide
TypeError: unsupported operand type(s) for /: 'str' and 'str'
```
* 실제 개발에서 finally는 파일이나 n/w 연결 등을 처리할 때 매우 유용

## 8.7 Predefined Clean-up Actions
* 쓸 수 있다면 안전한 사전에 정의된 clean-up action을 취해 안전하게 clean-up 하라는 것
```python
for line in open("myfile.txt"):
    print(line, end="")
```
* 위 코드를 그 아래처럼!
```python
with open("myfile.txt") as f:
    for line in f:
        print(line, end="")
```

## 정리
* 파이선에 error 는 컴파일 시점에 나타나는 syntax error와 실행 시점에 나타나는 exception error가 있고, 
* exception는 try-except 구문으로 처리할 수 있음
* try-except 문은 else 구문으로 사용해 예외 없이 잘 처리되었을 때만 실행시키고 싶은 코드 작성 가능
* try-except 문은 finally 구문으로 언제든지 실행시키고자 하는 코드를 구분 가능 (clean-up action)
* 개발자는 exception 클라스를 정의할 수 있음
* 사용할 수 있다면 사전에 정의된 clean-up action을 사용하자
