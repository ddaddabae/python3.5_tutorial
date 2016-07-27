# 11. Brief Tour of the Standard Library - Part II
* 이 부분은 전문적인 개발 요구를 지원해주기 위한 library들을 정리힘

## 11.1 Output Formatting
* replib 모듈은 아주 큰 obj에 대해 줄임표로 출력해줌
```python
>>> import reprlib
>>> reprlib.repr(set('supercalifragilisticexpialidocious'))
"{'a', 'c', 'd', 'e', 'f', 'g', ...}"
```
* pprint 모듈은 읽기 편하게 너비를 지정해서 출력해줌
```python
>>> import pprint
>>> t = [[[['black', 'cyan'], 'white', ['green', 'red']], [['magenta',
...     'yellow'], 'blue']]]
...
>>> pprint.pprint(t, width=30)
[[[['black', 'cyan'],
   'white',
   ['green', 'red']],
  [['magenta', 'yellow'],
   'blue']]]
```
* textwrap 모듈은 문단의 포맷을 지정해 출력해줌
```python
>>> import textwrap
>>> doc = """The wrap() method is just like fill() except that it returns
... a list of strings instead of one big string with newlines to separate
... the wrapped lines."""
...
>>> print(textwrap.fill(doc, width=40))
The wrap() method is just like fill()
except that it returns a list of strings
instead of one big string with newlines
to separate the wrapped lines.
```
* locale 모듈은 특정 문화 포맷(culture specific data) DB에 접근해 그룹 분리자를 이용해 숫자들을 직접 포매팅 시켜줌
```python
>>> import locale
>>> locale.setlocale(locale.LC_ALL, 'English_United States.1252')
'English_United States.1252'
>>> conv = locale.localeconv()          # get a mapping of conventions
>>> x = 1234567.8
>>> locale.format("%d", x, grouping=True)
'1,234,567'
>>> locale.format_string("%s%.*f", (conv['currency_symbol'],
...                      conv['frac_digits'], x), grouping=True)
'$1,234,567.80'
```

## 11.2 Templating
* string 모듈이 포함하는 template class를 이용하면 사용자(end-user)가 구문을 수정하는 기능을 쉽게 만들 수 있음
* 이것을 사용하면 사용자는 application을 customize 가능하게 함
* $는 변수가 되고 $$는 $이 escape 되게 만듦
```python
>>> from string import Template
>>> t = Template('${village}folk send $$10 to $cause.')
>>> t.substitute(village='Nottingham', cause='the ditch fund')
'Nottinghamfolk send $10 to the ditch fund.'
```
* 변수를 해당 문자로 치환해주는 substitute 함수가 모든 변수를 다 치환해주지 못하면 KeyError 발생
* 이를 방지하기 위해서 safe_substitute() 함수 이용
```python
>>> t = Template('Return the $item to $owner.')
>>> d = dict(item='unladen swallow')
>>> t.substitute(d)
Traceback (most recent call last):
  ...
KeyError: 'owner'
>>> t.safe_substitute(d)
'Return the unladen swallow to $owner.'
```
* substitute 함수를 사용하면 아래와 같이 파일 명을 변환해주는 기능도 가능
```python
>>> import time, os.path
>>> photofiles = ['img_1074.jpg', 'img_1076.jpg', 'img_1077.jpg']
>>> class BatchRename(Template):
...     delimiter = '%'
>>> fmt = input('Enter rename style (%d-date %n-seqnum %f-format):  ')
Enter rename style (%d-date %n-seqnum %f-format):  Ashley_%n%f

>>> t = BatchRename(fmt)
>>> date = time.strftime('%d%b%y')
>>> for i, filename in enumerate(photofiles):
...     base, ext = os.path.splitext(filename)
...     newname = t.substitute(d=date, n=i, f=ext)
...     print('{0} --> {1}'.format(filename, newname))

img_1074.jpg --> Ashley_0.jpg
img_1076.jpg --> Ashley_1.jpg
img_1077.jpg --> Ashley_2.jpg
```
* templating은 XML이나 HTML을 이용할 때 유용하게 활용될 수 있음

## 11.3 Working with binary data record layouts
* struct 모듈은 binary record 포맷의 변수 길이를 다룰 때 사용하는 pack과 unpack 함수를 제공
* '<'은 standard size이며 little-endian byte order(작은 단위가 앞에 나오는 바이트 순서)라는 것을 의미.
  * [little-endian 참고](https://ko.wikipedia.org/wiki/%EC%97%94%EB%94%94%EC%96%B8)
* 아래 코드는 zipfile 모듈을 사용하지 않고 zip 파일의 압축을 푸는 것을 보여줌
  * 아래 코드에서, I는 unsigned int, H는 unsigned short 를 의미
```python
import struct

with open('myfile.zip', 'rb') as f:
    data = f.read()

start = 0
for i in range(3):                      # show the first 3 file headers
    start += 14
    fields = struct.unpack('<IIIHH', data[start:start+16])
    crc32, comp_size, uncomp_size, filenamesize, extra_size = fields

    start += 16
    filename = data[start:start+filenamesize]
    start += filenamesize
    extra = data[start:start+extra_size]
    print(filename, hex(crc32), comp_size, uncomp_size)

    start += extra_size + comp_size     # skip to the next header
```

## 11.4 Multi-threading
* 쓰레딩은 순차적으로 의존적이지 않은 task들은 decoupling 하는 기법
* 아래 코드는 어떻게 highlevel 쓰레딩 모듈이 task를 background에서 실행시키는지를 보여줌
```python
import threading, zipfile

class AsyncZip(threading.Thread):
    def __init__(self, infile, outfile):
        threading.Thread.__init__(self)
        self.infile = infile
        self.outfile = outfile

    def run(self):
        f = zipfile.ZipFile(self.outfile, 'w', zipfile.ZIP_DEFLATED)
        f.write(self.infile)
        f.close()
        print('Finished background zip of:', self.infile)

background = AsyncZip('mydata.txt', 'myarchive.zip')
background.start()
print('The main program continues to run in foreground.')

background.join()    # Wait for the background task to finish
print('Main program waited until background was done.')
```
* 쓰레딩에서 중요한 것은 어떻게 리소스들을 공유할 것인가임
* 이를 위해, threading 모듈은 synchronization primitives 들을 제공
  * 예) locks, events, condition variables, semaphores

## 11.5 Logging
* 로깅 시스템
```python
import logging
logging.debug('Debugging information')
logging.info('Informational message')
logging.warning('Warning:config file %s not found', 'server.conf')
logging.error('Error occurred')
logging.critical('Critical error -- shutting down')
```
* 결과, 
```python
WARNING:root:Warning:config file server.conf not found
ERROR:root:Error occurred
CRITICAL:root:Critical error -- shutting down
```
* default로 informational과 debugging 메제시들은 나타나지 않고 standard error에 대해서만 프린트 됨
* 다른 출력 옵션들에는 email, datagram, socker, http server 을 통해 메세지를 전달하는 방식이 있음
* 메세지의 우선순위: DEBUG, INFO, WARNING, ERROR, and CRITICAL
* logging 시스템은 개발자가 수정할 수 있음

## 11.6 Weak References
* 파이선은 메모리 관리를 자동적으로 함
  * reference가 대부분의 obj들의 수를 세고 cycle들을 없애기 위해 garbage collection을 하는 방식
* 이 방식은 대부분의 경우 잘 작동되지만 특정 사용에서는 obj들을 traking 해야 할 수 있음
* weakref 모듈이 바로 이런 경우를 위한 라이브러리
* 이는 모든 obj들을 traking 하면서 reference를 생성하고 obj가 필요 없어지면 자동적으로 weakref 테이블을 삭제하는 방식
  * 이 때 callback이 trigger 됨
```python
>>> import weakref, gc
>>> class A:
...     def __init__(self, value):
...         self.value = value
...     def __repr__(self):
...         return str(self.value)
...
>>> a = A(10)                   # create a reference
>>> d = weakref.WeakValueDictionary()
>>> d['primary'] = a            # does not create a reference
>>> d['primary']                # fetch the object if it is still alive
10
>>> del a                       # remove the one reference
>>> gc.collect()                # run garbage collection right away
0
>>> d['primary']                # entry was automatically removed
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
    d['primary']                # entry was automatically removed
  File "C:/python35/lib/weakref.py", line 46, in __getitem__
    o = self.data[key]()
KeyError: 'primary'
```

## 11.7 Tools for working with lists
* 많은 자료구조들은 built-in list 형을 필요로 함
* 하지만 다른 성능 trade-off로 인해 다른 구현을 필요로함
* array 모듈은 유일한 데이터많은 저장하는 array() obj를 제공함
* 아래 예제는 16byte 대신 2byte unsigned 이진 수를 저장하는 (typecode "H") list obj의 사용을 보여줌
```python
>>> from array import array
>>> a = array('H', [4000, 10, 700, 22222])
>>> sum(a)
26932
>>> a[1:3]
array('H', [10, 700])
```
* collection 모듈은 deque() obj를 제공
  * 왼쪽의 append와 pop을 더 빠르게 함
  * 가운데에서 append하고 pop하는 것은 더 느림
```python
>>> from collections import deque
>>> d = deque(["task1", "task2", "task3"])
>>> d.append("task4")
>>> print("Handling", d.popleft())
Handling task1
```
```python
unsearched = deque([starting_node])
def breadth_first_search(unsearched):
    node = unsearched.popleft()
    for m in gen_moves(node):
        if is_goal(m):
            return m
        unsearched.append(m)
```
* bisect 모듈은 정렬에 대한 기능들을 제공
```python
>>> import bisect
>>> scores = [(100, 'perl'), (200, 'tcl'), (400, 'lua'), (500, 'python')]
>>> bisect.insort(scores, (300, 'ruby'))
>>> scores
[(100, 'perl'), (200, 'tcl'), (300, 'ruby'), (400, 'lua'), (500, 'python')]
```
* heapq 모듈은 list에 heap 기반의 구현 기능을 가짐
```python
>>> from heapq import heapify, heappop, heappush
>>> data = [1, 3, 5, 7, 9, 2, 4, 6, 8, 0]
>>> heapify(data)                      # rearrange the list into heap order
>>> heappush(data, -5)                 # add a new entry
>>> [heappop(data) for i in range(3)]  # fetch the three smallest entries
[-5, 0, 1]
```

## 11.8 Decimal Floating point arighmetic
* decimal 모듈은 소수 형의 연산을 제공
* built-in float과 비교해 아래와 같은 상황에서 더 유용함
  * 금융 application
  * over precision 제어
  * 법이나 규제 요건에 맞도록 over rounding 제어 
  * 기타 등등.. ㅎㅎ
* 아래 예제는 70cent의 전화 요금에 대해 5% 세금을 계산하는 코드
  * 반올림 했을 때의 결과 차이를 보임
```python
>>> from decimal import *
>>> round(Decimal('0.70') * Decimal('1.05'), 2)
Decimal('0.74')
>>> round(.70 * 1.05, 2)
0.73
```
* Decimal은 결과의 원하는 소수 점 아래를 계속 0으로 유지
* 따라서 정확한 표현은 Decimal 클래스로 modulo 계산을 할 수 있게 해줌
```python
>>> Decimal('1.00') % Decimal('.10')
Decimal('0.00')
>>> 1.00 % 0.10
0.09999999999999995

>>> sum([Decimal('0.1')]*10) == Decimal('1.0')
True
>>> sum([0.1]*10) == 1.0
False
```
* Decimal 모듈은 더 높은 정확도에 대한 계산을 제공
```python
>>> getcontext().prec = 36
>>> Decimal(1) / Decimal(7)
Decimal('0.142857142857142857142857142857142857')
```
