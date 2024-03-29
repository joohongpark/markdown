# C++ 입문 #7 (C++ Module 07)

이번 모듈에서는 C++에서 제네릭 프로그래밍을 지원하기 위한 탬플릿을 다룬다.

## 학습해야 할 내용

- 제네릭 프로그래밍
- 탬플릿
  - 함수 탬플릿 (ex00, ex01)
  - 클래스 탬플릿 (ex02)
  - 컨테이너 (ex02)

### 제네릭 프로그래밍

제네릭 프로그래밍은 구조적/OOP와 다른 프로그래밍 패러다임이다. 데이터 타입에 의존적인 코드에 대해 데이터 타입에 대한 의존을 없애는 방식이다. (OOP에 속한 개념이 아니며 독립적인 프로그래밍 방식이다.)

OOP에선 오버로딩을 지원하므로 제네릭과 비슷하게 코딩이 가능하지만 자료형에 대한 함수를 일일히 다 써주어야 하는 단점이 있다.

제네릭 프로그래밍을 지원하면 함수를 하나만 쓰면 int 형이든 float 형이든 다른 타입이든 함수를 하나만 작성하면 공통적으로 적용이 가능하다. (참고로 자바는 특정 버전부터 제네릭 프로그래밍을 지원한다.)

### 탬플릿

C++에서는 제네릭 프로그래밍을 "탬플릿" 이라는 형태로 지원한다. 탬플릿은 C++의 표준 라이브러리에서 폭넓게 사용된다.

특정한 타입에 대해 처리하는 함수나 클래스를 하나만 작성해 놓으면 컴파일 시에 컴파일러가 코드에서 요구하는 만큼 타입에 맞는 함수들을 타입만 바꾼 식으로 여러개 찍어내는 식으로 동작한다.

## ex00

### 학습해야 하는 내용

- 함수 탬플릿

### 함수 탬플릿

함수의 입/출력에 대해 삽입되는 타입을 임의로 정할 수 있다. 예제는 다음과 같다.

```c++
template <typename T, ...>
T func(T var1 ...);
```

이렇게 선언해 놓고 func 함수를 사용하면 컴파일러가 알아서 상황에 맞게 템플릿 함수들을 생성한다. (암시적 구체화)

T라는 타입을 임의로 지정해야 할 경우가 있다. 이런 경우를 명시적 구체화 라고 하며 `func<type>()` 와 같이 사용하면 type이라는 타입이 대입된 함수를 생성한다.

함수 탬플릿은 .hpp 내부에 선언한다.

## ex01

3개의 파라미터를 받는 iter라는 함수 탬플릿을 만든다.

## ex02

### 학습해야 하는 내용

- 클래스 탬플릿
- 컨테이너

### 클래스 탬플릿

함수 탬플릿과 유사하게 클래스를 선언할 때 바로 위에 탬플릿을 지정하면 된다.

```c++
template <typename T, ...>
class classsexample {
  ...
  ...
}
```

함수 탬플릿도 마찬가지지만 클래스 탬플릿도 실제 클래스나 함수가 아니기 때문에 헤더에 정의하는 것을 원칙으로 한다. 개념적으로도 그렇지만 C++의 탬플릿은 컴파일러가 탬플릿을 알고 있고 소스코드에서 탬블릿을 사용할 때 선언된 탬플릿을 바탕으로 함수나 클래스를 구체화 하는 방식이라 헤더에 정의가 되어야 한다. 이는 소스코드 단위로 오브젝트 파일을 만드는 방식이기 때문에 그런데 왜 그런지 한번 생각해 보자.

### 컨테이너

컨테이너란 용어가 리눅스 컨테이너랑 혼동될 수 있는데 별도로 C++에는 컨테이너라는 개념이 있다.

C++에서 컨테이너(Container)라는 개념은 동일한 타입인 객체들의 집합을 다루는 객체를 의미한다. 컨테이너라는 개념은 정의에 따르면 템플릿에만 한정적인 것이 아니다. 하지만 템플릿을 이용해서 컨테이너를 풍부하게 활용할 수 있기 때문에 주로 템플릿으로 컨테이너를 만든다.

C++에서 많이 나오는 용어인 "STL"이랑 무관하지 않는데 C++에서 STL은 Standatd Template Library 의 약자이며 표준 템플릿 라이브러리 라는 의미이다. STL은 이름에 써있다시피 템플릿 라이브러리이며 STL이 제공하는 것 중 하나가 "컨테이너" 이다.

STL은 vector, deque, list 등 자료구조가 구현된 형태를 사용할 수 있도록 해 주며 자료구조 내에 어떤 데이터 타입이 들어갈지 모르니 제너릭하게 만들어져 있다. 그래서 템플릿으로 구현되어 있다.

복잡하게 생각할 것 없이 C++에서 자료구조를 보통 컨테이너라고 부른다. 특히 STL의 자료구조를 컨테이너라고 부른다.

STL 컨테이너는 cpp module 08부터 다룬다.

### 문제

Array 클래스 탬플릿(일종의 컨테이너)을 만든다. (클래스로부터 생성된 인스턴스가 배열인거처럼 동작되게) [] 연산자도 오버로딩하고 존재하지 않는 인덱스 값 참조할 때 에러 throw도 넣어봐라.