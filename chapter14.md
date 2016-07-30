# 14. 사용자 입력(Interactive Input Editing)과 이력(History) 대체

- 어떤 버전의 Python interpreter는 Korn shell과 GNU Bash shell에서 처럼, 입력값과 이력을 조작할 수 있다. 이는 다양한 스타일의 조작법을 제공하는 ```GNU Readline``` 라이브러리를 이용하여 구현되어 있다. 이 라이브러리들은 자체적인 문서를 가지고 있는데, 여기에 옮기지는 않는다.
 - [GNU readline library](https://ko.wikipedia.org/wiki/GNU_readline)와 같은 동작을 지원한다는 뜻 같음. GNU readline library는 앞 링크 참조..

## 14.1. 탭(Tab) 완성과 이력 조작

- 변수 명과 모듈 이름의 자동 완성은 인터프리터의 시작시에 자동으로 활성화 되어 있다. 따라서 `Tab` 키를 이용하여 해당 기능을 사용할 수 있다. 이는 Python 명령어 이름과 현재 지역 변수, 사용가능한 모듈 명 등에 사용할 수 있다. `string.a`와 같은 점 표현식(dotted expressions)에서는 마지막 점 `'.'` 전 까지의 표현식을 판단(evaluate)하여, 해당 object의 적절한 어트리뷰트의 자동완성을 제안하여 준다. 참고로, `__getattr__()` 메소드를 포함하는 object의 경우 탭 완성 기능이 어플리케이션 코드를 실행하게 될 수 있다. 또, 사용자의 이력(history)은 기본적으로 사용자 폴더 하위의 `.python_history` 이름의 파일에 저장되도록 설정되어있다. 이 이력은 다음 인터프리터 세션 이용 시에 다시 사용할 수 있다.
 - `__getattr__()` 부분이 잘 이해가 안되는데.. [`__getattr__()`](https://docs.python.org/3/reference/datamodel.html#object.__getattr__) 설명을 보면 조금(아마도) 낫다

## 14.2. 상호작용 인터프리터(Interactive Interpreter) 의 대안

- 지금의 환경(Pythoon interpreter를 말하는 듯)은 초기 버전의 인터프리터에 비해 대단히 발전하였지만, 몇가지 개선되었으면 하는 부분이 있다. 연결된 라인들에 대해 적절한 들여쓰기(indentation)를 제안해 주면 좋을 것 같다(파서는 다음에 들여쓰기가 필요할지 알고 있다). 자동완성 동작은 인터프리터의 심볼 테이블을 이용할 것이다. 괄호나 따옴표 등을 체크(혹은 제안)하는 것 등의 기능 또한 추가된다면 유용하게 쓰일 것이다.
- 꽤 오랜 시간 발전한 Python 인터프리터의 대체재 중 하나는 [`IPython`](https://ipython.org/)인데, 이는 탭 자동완성, 객체 탐색(Object exploration), 그리고 강화된 이력 관리 기능을 제공한다. 이는 또한 완전히 커스터마이즈 되어 다른 어플리케이션에 삽입될 수 있다. [`bpython`](http://www.bpython-interpreter.org/) 또한 이와 유사한 Python 인터프리터의 대체재이다.
