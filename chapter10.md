# 10. 간략한 Standard library (1)

## 10.1. 운영체제(OS) 인터페이스

- ```import os``` 대신 ```from os import *``` 을 사용할 것.
 - 그렇지 않으면 os.open() 함수가 built-in open() 함수를 덮어쓸 수 있다.
- dir() 과 help() 함수를 이용해서 os library에 대한 정보를 얻을 수 있음.
```python
>>> import os
>>> dir(os)
<returns a list of all module functions>
>>> help(os)
```
- shutil : 파일과 디렉토리 관리 기능을 제공
```python
>>> import shutil
>>> shutil.copyfile('data.db', 'archive.db')
'archive.db'
>>> shutil.move('/build/executables', 'installdir')
'installdir'
```

## 10.2. Wildcard 파일 검색

- glob 모듈을 이용하여 디렉토리에서 wildcard 를 이용한 파일 검색을 할 수 있다.
```python
>>> import glob
>>> glob.glob('*.py')
['primes.py', 'random.py', 'quote.py']
```

## 10.3. Command Line 인자(Arguments)

- 대개 유틸리티 스크립트는 Command Line 인자를 처리해야 한다. 이러한 인자값들은 ```sys```모듈의 ```argv``` attribute에 list 형식으로 저장된다. 예를들어, ```python demo.py one two three```의 실행 결과는 아래와 같다.
```python
>>> import sys
>>> print(sys.argv)
['demo.py', 'one', 'two', 'three']
```

## 10.4. 에러 출력 Redirection 과 프로그램 종료

- ```sys``` 모듈에는 ```stdin```, ```stdout```, 그리고 ```stderr``` attribute들도 포함되어 있다. 그 중 마지막 ```stderr```는 경고 혹은 에러 메세지를 표시하는 경우 유용하게 사용되는데, ```stdout```이 redirect된 경우에도 표시할 수 있다.
```python
>>> sys.stderr.write('Warning, log file not found starting a new one\n')
Warning, log file not found starting a new one
```

- 스크립트를 종료하는 가장 직접적인 방법은 ```sys.exit()```을 이용하는 것이다.


## 10.5. 문자열(String) 패턴 비교

- ```re```모듈은 더 나은 문자열 처리를 위해 정규 표현식 도구를 제공한다. 복잡한 문자열 비교와 조작을 위해, 정규 표현식은 간단명료하고 최적화된 해볍을 제공한다:
```python
>>> import re
>>> re.findall(r'\bf[a-z]*', 'which foot or hand fell fastest')
['foot', 'fell', 'fastest']
>>> re.sub(r'(\b[a-z]+) \1', r'\1', 'cat in the the hat')
'cat in the hat'
```

- 만약 간단한 기능만이 필요할 경우, 가독성이 좋고 디버깅이 편리하기 때문에 string 메소드가 더 선호된다.
```python
>>> 'tea for too'.replace('too', 'two')
'tea for two'
```

## 10.6. 수학 연산

- ```math```모듈은 C 라이브러리에서 제공하는 부동소수점 연산 기능을 사용할 수 있도록 돕는다.
```python
>>> import math
>>> math.cos(math.pi / 4)
0.70710678118654757
>>> math.log(1024, 2)
10.0
```

- ```random```모듈은 난수를 생성하는 도구를 제공한다.
```python
>>> import random
>>> random.choice(['apple', 'pear', 'banana'])
'apple'
>>> random.sample(range(100), 10)   # sampling without replacement
[30, 83, 16, 4, 8, 81, 41, 50, 18, 33]
>>> random.random()    # random float
0.17970987693706186
>>> random.randrange(6)    # random integer chosen from range(6)
4
```

- ```statistics```모듈은 숫자 데이터들에 대한 기본적인 통계 값들을 계산해 준다.(평균(mean), 중간값(median), 분산(variance), 등.)
```python
>>> import statistics
>>> data = [2.75, 1.75, 1.25, 0.25, 0.5, 1.25, 3.5]
>>> statistics.mean(data)
1.6071428571428572
>>> statistics.median(data)
1.25
>>> statistics.variance(data)
1.3720238095238095
```

- SciPy 프로젝트 <https://scipy.org> 는 숫자 계산을 위한 많은 모듈들을 제공한다.

## 10.7. 인터넷 접근

- 인터넷에 접근하고 인터넷 프로토콜을 제어하기 위한 많은 모듈들이 존재한다. 그중 가장 단순한 두가지가 URL로 부터 데이터를 받아오기 위한 ```urllib.request```와, 메일을 전송하기 위한 ```smtplib``` 이다.
```python
>>> from urllib.request import urlopen
>>> with urlopen('http://tycho.usno.navy.mil/cgi-bin/timer.pl') as response:
...     for line in response:
...         line = line.decode('utf-8')  # Decoding the binary data to text.
...         if 'EST' in line or 'EDT' in line:  # look for Eastern Time
...             print(line)

<BR>Nov. 25, 09:43:32 PM EST

>>> import smtplib
>>> server = smtplib.SMTP('localhost')
>>> server.sendmail('soothsayer@example.org', 'jcaesar@example.org',
... """To: jcaesar@example.org
... From: soothsayer@example.org
...
... Beware the Ides of March.
... """)
>>> server.quit()
```

## 10.8. 날짜와 시간

- ```datetime```모듈은 날짜와 시간을 다루기 위한 간단한 방법과 복잡한 방법을 모두 제공한다. 날짜와 시간의 산술적 연산도 제공되지만, 모듈의 주요 기능은 날짜와 시간 값을 적절한 형식에 맞추어 바꾸어 출력하는 것에 맞추어져 있다. 이 모듈은 timezone을 지원하는 object도 포함하고 있다.
```python
>>> # dates are easily constructed and formatted
>>> from datetime import date
>>> now = date.today()
>>> now
datetime.date(2003, 12, 2)
>>> now.strftime("%m-%d-%y. %d %b %Y is a %A on the %d day of %B.")
'12-02-03. 02 Dec 2003 is a Tuesday on the 02 day of December.'

>>> # dates support calendar arithmetic
>>> birthday = date(1964, 7, 31)
>>> age = now - birthday
>>> age.days
14368
```

## 10.9. 데이터 압축

- 데이터 압축과 아카이빙(archiving)을 위해 ```zlib```, ```gzip```, ```bz2```, ```lzma```, ```zipfile```, ```tarfile``` 등의 모듈이 제공된다.
```python
>>> import zlib
>>> s = b'witch which has which witches wrist watch'
>>> len(s)
41
>>> t = zlib.compress(s)
>>> len(t)
37
>>> zlib.decompress(t)
b'witch which has which witches wrist watch'
>>> zlib.crc32(s)
226805979
```

## 10.10. 성능 측정

- 어떤 Python 사용자들은 같은 문제를 해결하는 서로 다른 접근 방법에 대한 성능을 비교하는 것에 관심을 가지고 있다. Python은 이를 위해 측정 도구를 제공한다.
- 예를들어, 인자값(argument) 바꾸기(swapping)을 위해, 전통적인 방법 대신에 튜플(tuple)의 패킹과 언패킹 기능을 사용하고 싶을 수 있다. ```timeit``` 모듈은 빠르게 성능을 비교할 수 있도록 도와준다.
```python
>>> from timeit import Timer
>>> Timer('t=a; a=b; b=t', 'a=1; b=2').timeit()
0.57535828626024577
>>> Timer('a,b = b,a', 'a=1; b=2').timeit()
0.54962537085770791
```

- ```timeit```과는 반대로, ```provile```과 ```pstats``` 모듈은 큰 코드 블록에 대한 성능 측정 기능을 제공한다.

## 10.11. 품질 관리

- 좋은 품질의 소프트웨어를 작성하는 방법 중 하나는 각각의 기능에 대해 테스트를 작성하고, 개발 과정 중에 자주 이 테스트를 수행하는 것이다.
- ```doctest``` 모듈은 모듈을 읽어들여, docstrings에 포함된 테스트를 찾아 수행해 준다. 테스트 작성은 docstring에 함수의 일반적인 호출을 복사-붙여넣기 하고, 그 결과값을 함께 적으면 될 정도로 간단하다. 이는 사용자에게 코드의 사용 예제를 제시해 줌으로써 코드에 대한 문서를 개선시켜 줌과 동시에, 문서의 내용이 참이라는 것을 검증해 준다.
- ```unittest``` 모듈은 ```doctest``` 모듈처럼 작성하기 쉽지는 않지만, 여러 파일에 있는 테스트들을 관리하기 위한 더 종합적인 방법을 제공한다.
```python
import unittest

class TestStatisticalFunctions(unittest.TestCase):

    def test_average(self):
        self.assertEqual(average([20, 30, 70]), 40.0)
        self.assertEqual(round(average([1, 5, 7]), 1), 4.3)
        with self.assertRaises(ZeroDivisionError):
            average([])
        with self.assertRaises(TypeError):
            average(20, 30, 70)

unittest.main()  # Calling from the command line invokes all tests
```

## 10.12. 배터리 포함(Batteries Included)

- Python은 "batteries included" 라는 철학을 가지고 이는 큰 패키지들의 정교하고 강력한 기능을 통해 확인할 수 있다. 예를들어:
 - ```xmlrpc.client```와 ```xmlrpc.server``` 모듈은 원격코드호출(remote procegure calls)을 간단한 방법으로 제공한다. 모듈 이름과는 다르게, XML에 대한 이해는 필요하지 않다.
 - ```email``` 패키지는 이메일 메시지를 위한 라이브러리인데, MIME과 다른 RFC 2822 기반의 메세지 형식을 포함한다. 실제로 메시지를 주고받는 ```smtplib```과 ```poplib```과는 다르게, 복잡한 메시지 구조를 만들고 decoding 하기위한 도구들과, 인터넷 encoding과 헤더 프로토콜들을 처리해 주는 도구들을 모두 포함하고 있다.
 - ```json``` 패키지는 파싱을 위한 강력한 기능을 지원한다. ```csv``` 모듈은 주로 데이터베이스나 spreadsheets에서 사용하는 콤마-구분 파일 포멧(Comma-Separated Value format)을 파일로부터 직접 읽고, 쓸 수 있는 기능을 제공한다. XML 처리는 ```xml.etree.ElementTree```, ```xml.dom```, ```xml.sax``` 패키지에서 제공한다. 
 - ```sqlite3``` 모듈은 약간의 비 표준 SQL 문법을 이용한 데이터베이스 접근 / 수정 기능을 제공하는 SQLite 데이터베이스 라이브러리에 대한 wrapper이다. 
 - ```gettext```, ```locale```, ```codecs```와 같은 모듈들을 통해 세계화를 지원할 수 있다.
