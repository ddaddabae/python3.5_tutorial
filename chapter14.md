# 14. 사용자 입력(Interactive Input) 조작과 이력(History) 대체

- 어떤 버전의 Python interpreter는 Korn shell과 GNU Bash shell에서 처럼, 입력값과 이력을 조작할 수 있다. 이는 다양한 스타일의 조작법을 제공하는 ```GNU Readline``` 라이브러리를 이용하여 구현되어 있다. 이 라이브러리들은 자체적인 문서를 가지고 있는데, 여기에 옮기지는 않는다.

## 14.1. 탭(Tab) 완성과 이력 조작

- 변수 명과 모듈 이름의 자동 완성은 인터프리터의 시작시에 자동으로 활성화 되어 있다. 따라서 ```Tab``` 키를 이용하여 해당 기능을 사용할 수 있다. 이는 Python 명령어 이름과 현재 지역 변수, 사용가능한 모듈 명 등에 사용할 수 있다. ```string.a```와 같은 점 표현식(dotted expressions)에서는 마지막 점 ```'.'```을 판별하여 해당 object의 적절한 어트리뷰트의 자동완성을 제안하여 준다.

Completion of variable and module names is automatically enabled at interpreter startup so that the Tab key invokes the completion function; it looks at Python statement names, the current local variables, and the available module names. For dotted expressions such as string.a, it will evaluate the expression up to the final '.' and then suggest completions from the attributes of the resulting object. Note that this may execute application-defined code if an object with a __getattr__() method is part of the expression. The default configuration also saves your history into a file named .python_history in your user directory. The history will be available again during the next interactive interpreter session.

## 14.2. Alternatives to the Interactive Interpreter

This facility is an enormous step forward compared to earlier versions of the interpreter; however, some wishes are left: It would be nice if the proper indentation were suggested on continuation lines (the parser knows if an indent token is required next). The completion mechanism might use the interpreter’s symbol table. A command to check (or even suggest) matching parentheses, quotes, etc., would also be useful.

One alternative enhanced interactive interpreter that has been around for quite some time is IPython, which features tab completion, object exploration and advanced history management. It can also be thoroughly customized and embedded into other applications. Another similar enhanced interactive environment is bpython.
