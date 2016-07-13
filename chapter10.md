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

## 10.3. Command Line 인자(Arguments)

## 10.4. 문자열(String) 패턴 비교

## 10.6. 수학

## 10.7. 인터넷 접근

## 10.8. 날짜와 시간

## 10.9. 데이터 압축

## 10.10. 성능 측정

## 10.11. 품질 관리

## 10.12. 배터리 포함(Batteries Included)

1. OS - interacting with operating system
 * shutil : provides high level interface
2. glob - get file list with wildcard search
3. sys - command line arguments, stdin, stdout, stderr
4. re - regular expression
5. math - floating point math (c library functions)
6. random - random
7. statistics - statistics
8. urllib - internet access ```urllib.request```
9. smtplib - mail sending
10. datetime - date and time
11. zlib, gzip, bz2, lzma, zipfile, tarfile - compression
12. timeit - performance (check code running time)
13. doctest - testing tool working with docstring
14. unittest - testing tool

"batteries included" philosophy
