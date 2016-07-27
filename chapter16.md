# 16. 부록

## 16.1. 상호작용 모드(Interactive Mode)

### 16.1.1. 예외 처리

- 예외가 발생하면, 인터프리터는 오류 메세지와 스택 추적 결과를 출력한다. 상호작용 모드에서는 이후 초기 프롬프트를 출력하는데, 만약 인풋의 형태가 파일이었을 경우, 스택 추적 결과를 출력한 뒤 0이 아닌 종료 코드를 반환하며 종료된다.(try 구문의 except 절에서 처리되는 예외는 이 경우 오류가 아니다.) 어떤 오류들은 절대적으로 치명적이면서 0이 아닌 종료 코드를 반환하며 종료된다. 이는 내부적 불일치를 초래하고, 일부의 경우 OOM을 발생시킨다. 보통의 출력은 표준 출력(standard output)에 쓰여지는 것에 비해, 모든 오류 메세지는 표준 오류 출력(standard error stream)에 쓰여진다.
- 첫 번째 혹은 두 번째 프롬프트에 인터럽트 문자(보통 Control-C 혹은 Delete)를 입력하면, 입력을 취소하고 첫 번째 프롬프트로 돌아가게 된다. 명령어가 실행되는 동안 인터럽트 문자를 입력하면 `KeyboardInterrupt` 예외를 발생시키고, 이는 try 구문에 의해 처리될 수 있다.

### 16.1.2. 실행가능한 Python 스크립트

- BSD 계열 유닉스 시스템에서, Python 스크립트는 쉘 스크립트와 같이 스크립트 파일의 처음에 아래 라인을 포함하고, 파일을 실행가능 모드로 설정하여 직접 실행 가능하다.
```
#!/usr/bin/env python3.5
```
(인터프리터는 사용자의 PATH경로에 있다고 가정하자) `#!`은 반드시 파일의 첫 두 글자이어야 한다. 어떤 플랫폼에서는, 첫 줄은 반드시 유닉스 스타일의 개행문자(`'\n'`)로 끝나야 하고, 윈도우 스타일의 개행문자(`'\r\n'`)으로 끝나서는 안된다. 해쉬나 pound, character, '#' 은 Python에서 주석을 시작하기 위해 이용된다.

- 스크립트는 `chmod` 명령어를 이용하여 사용권한(permission)이나 실행가능 모드를 설정할 수 있다.
```
$ chmod +x myscript.py
```
윈도우 시스템에서, "실행가능 모드"라는 용어는 없다. Python 설치파일은 자동으로 `.py` 파일들을 `python.exe`에 연결해 주는데, 그로 인해 Python 파일을 더블클릭 함으로써 스크립트를 실행할 수 있게 된다. 확장자는 `.pyw`가 될 수도 있으며, 이 경우 보통 나타나는 콘솔 윈도우는 나타나지 않는다.(suppressed 된다. 나타나지 않는 것인지는 windows 사용자가 확인 바래요)

### 16.1.3. 상호작용하는(Interactive) 시작(Startup) 파일

- 파이썬을 상호작용 모드로 사용할 때, 인터프리터가 시작할 때 마다 몇몇 표준 명령들이 자동으로 실행되도록 하는 것은 종종 유용하게 사용된다. 당신은 `PYTHONSTARTUP`이라는 이름의 환경변수에 당신의 시작시 실행할 커맨드가 포함된 파일의 이름을 설정함으로써 이런 시작파일 기능을 이용할 수 있다. 이는 유닉스 쉘의 `.profile` 기능과 유사하다.
- 이 파일은 상호작용 세션에서만 읽혀지며, Python이 스크립트 파일을 실행하는 경우는 읽혀지지 않는다. 또한 `/dev/tty`이 시작 파일로 지정된 경우에도 실행되지 않는다. 시작 파일은 상호작용 명령어가 실행되는 것과 같은 namespace에서 실행된다. 따라서 상호작용 세션에서 제한없이 object를 정의하고 import할 수 있다. 또한 프롬프트의 `sys.ps1`과 `sys.ps2`를 파일에서 변경할 수 있다.
- 만약 현재 경로에서 추가적인 시작 파일을 읽어오고 싶다면, 글로벌 시작 파일을 `if os.path.isfile('.pythonrc.py'): exec(open('.pythonrc.py').read())` 와 같은 코드를 이용하여 설정할 수 있다. 만약 스크립트 파일에서 시작 파일을 이용하고 싶다면, 아래 코드를 스크립트 파일에 포함시켜야 한다.
```python
import os
filename = os.environ.get('PYTHONSTARTUP')
if filename and os.path.isfile(filename):
    with open(filename) as fobj:
        startup_file = fobj.read()
    exec(startup_file)
```

### 16.1.4. 모듈 커스터마이즈

- Python은 커스터마이즈를 위해 `sitecustomize`와 `usercustomize`라는 두개의 훅(hook)을 제공한다. 이들의 동작을 확인하기 위해, 먼저 당신의 `site-packages` 경로를 찾아야 한다. Python을 실행하여 아래 코드를 실행시켜 보라:
```python
>>> import site
>>> site.getusersitepackages()
'/home/user/.local/lib/python3.5/site-packages'
```

- 이제, `usercustomize.py`라는 이름의 파일을 해당 경로에 만들어서 당신이 원하는 어느것이든 거기에 붙일 수 있다. 이는 모든 Python의 실행에 영향을 미치는데, `-s` 옵션을 이용하여 자동으로 import 되는 것을 막기 전까지 계속 영향을 끼친다.
- `sitecustomize` 또한 같은 방식으로 동작하지만, 대게 컴퓨터의 administrator에 의해 글로벌 `site-packages` 경로에 생성된다. 그리고 이는 `usercustomize` 전에 import된다. 더 자세한 내용을 알기 위해서는 `site module`에 대한 문서를 읽어보길 바란다.
