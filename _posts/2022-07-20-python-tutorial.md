---
layout: post
title: '[Python] 튜토리얼!'
comments: true
categories: [Python]
tags: [python, programming, tutorial]
---

# Python
python 사용이 잦아지며 Tutorial 내용을 작성한다.   

파이썬(Python)은 1991년 네덜란드계 프로그래머인 귀도 반 로섬이 발표한 고급 프로그래밍 언어로, 플랫폼에 독립적이며 인터프리터식, 객체지향적, 동적 타이핑(dynamically typed) 대화형 언어이다. 파이썬이라는 이름은 귀도가 좋아하는 코미디인〈Monty Python's Flying Circus〉에서 따온 것이다. 이름에서 고대신화에 나오는 커다란 뱀을 연상하는 경우도 있겠지만, 이와는 무관하다. 다만 로고에는 뱀 두마리가 형상화되어 있다.   
파이썬은 비영리의 파이썬 소프트웨어 재단이 관리하는 개방형, 공동체 기반 개발 모델을 가지고 있다. C언어로 구현된 사이썬 구현이 사실상의 표준이다.   
파이썬 3.9 버전 이상부터는 64비트로 바뀌면서 32비트 컴퓨터에서 사용할 수 없다. 파이썬 3.9 버전 이상을 사용하려면 64비트 컴퓨터여야 한다.   

__주요 특징__
*   실행 시간에 자료형을 검사하는 동적 타이핑(dynamic typing)을 지원합니다.
*   객체의 멤버에 무제한으로 접근할 수 있습니다.
*   모듈, 클래스, 객체와 같은 언어의 요소가 내부에서 접근할 수 있습니다.
*   리플렉션을 이용한 기술을 쓸 수 있습니다.

__해석 프로그램의 종류__
*   C파이썬 : C로 작성된 인터프리터.
*   스택리스 파이썬 : C 스택을 사용하지 않는 인터프리터.
*   자이썬( 제이파이썬 : JPython) : 자바 가상 머신 용 인터프리터.
*   IronPython : .NET 플랫폼 용 인터프리터.
*   PyPy : 파이썬으로 작성된 파이썬 인터프리터.

>-- <https://ko.wikipedia.org/wiki/%ED%8C%8C%EC%9D%B4%EC%8D%AC>{:target="_blank"}   

---
## Python style guide
PEP8는 python style guide가 작성된 문서이다.
개인적인 생각으로는 style guide는 반드시 필요하며 이유는 아래와 같다.

- 가독성
  - 개발자는 코드를 작성하는 시간보다 읽는 시간이 많다. 
  - 각 파일별 혹은 모듈별 또는 구역별 코드의 style이 일괄적이지 않다면 불필요한 리소스를 사용하게 된다.
- 일관성
  - e.g. cpp
  - `int32_t m_variable_name;`
  - `int32_t member_variable_name_;`
  - `int32_t MemberVariableName;`
  - 협업에 있어 일관적이지 못한 코드는 리소스 낭비를 일으킨다.

*결론은 style guide를 지키는 것이 같이 일하는 __동료__ 그리고 **나** 를 위한 일이다.*

>-- <https://peps.python.org/pep-0008>{:target="_blank"}   

---
## 주석
프로그램에서 주석이란? 프로그램의 실행에는 전혀 관여하지않으면서 프로그램 코드 중간 중간에 코드에 대한 자세한 **설명**을 첨부하는것을 말합니다.   

---
### 행 단위 주석
```python
""" 주석 내용을 작성하세요 """

" 주석 내용을 작성하세요
주석 내용을 작성하세요
주석 내용을 작성하세요
주석 내용을 작성하세요
주석 내용을 작성하세요
"
```

### 블록 단위 주석
```python
# 주석 내용을 작성하세요
# 주석 내용을 작성하세요
# 주석 내용을 작성하세요
```

---
## 입출력

---
### 표준 출력
Python 에선 표준 출력 함수인 `print(*objects, sep=' ', end='\n', file=sys.stdout, flush=False)`를 제공한다.

```python
print('one', 'two', 'three', '1', '2', '3')
print('one', 'two', 'three', '1', '2', '3', sep='-')
print('end value test,', end='--->')
print('end next value')
```
```
one two three 1 2 3   
one-two-three-1-2-3   
end value test,--->end next value   
```

### 표준 입력
Python 에선 표준 출력 함수인 `input([prompt])`를 제공한다.

```python
print('이름을 입력하세요', end="")
name = input();
print("이름 : {0}, type : {1}".format(name,type(name)))
name = input('이름을 입력하세요 ');
print("이름 : {0}, type : {1}".format(name,type(name)))
name = input('아무것도 입력하지 말고 EOF(Ctrl+D 또는 Ctrl+Z+Enter)를 입력해보세요');
```
```
이름을 입력하세요wonu   
이름 : wonu, type : <class 'str'>   
이름을 입력하세요 wonu   
이름 : wonu, type : <class 'str'>   
아무것도 입력하지 말고 EOF(Ctrl+D 또는 Ctrl+Z+Enter)를 입력해보세요Traceback (most recent call last):   
File "test2.py", line 57, in <module>   
    name = input('아무것도 입력하지 말고 EOF(Ctrl+D 또는 Ctrl+Z+Enter)를 입력해보세요');   
EOFError   
```

---
## Tips

---
### Underscore(_)로 시작하는 파이썬 클래스명

#### PEP8
PEP8에 명시적으로 언더스코어가 붙은 클래스가 정의되어 있지는 않지만, 문서의 일부에서 언더스코어로 시작하는 클래스에 대한 언급이 있습니다.

> Even with __all__ set appropriately, internal interfaces (packages, modules, classes, functions, attributes or other names) should still be prefixed with a single leading underscore.   
>-- <https://www.python.org/dev/peps/pep-0008/#public-and-internal-interfaces>

#### 파이썬 표준라이브러리 코드
파이썬 표준라이브러의 코드에서 이 방식으로 클래스를 사용하고 있습니다.
```python
class _HashedSeq(list):
    ...
```
>-- <https://github.com/python/cpython/blob/master/Lib/functools.py#L439>

#### !?
언더스코어로 시작하는 변수나 클래스는 from <module> import *과 같은 Asterisk를 사용하는 import문에 자동으로 불러와지지 않는다고 합니다.

---
# 참고
>-- <https://ko.wikipedia.org/wiki/%ED%8C%8C%EC%9D%B4%EC%8D%AC>   
>-- <https://wikidocs.net/book/2070>   
>-- <https://www.lhy.kr/python-class-name-startwith-underscore>