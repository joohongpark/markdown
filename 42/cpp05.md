# C++ 입문 #5 (C++ Module 05)

## ex00

### 문제 분석

Bureaucrat (Bureaucrat은 "관료/공무원" 이라는 뜻이다.) 라는 클래스에서 문제에서 지정한 예외가 발생하면 std::exception 형식으로 에러를 처리하도록 하여야 한다. 공무원 클래스는 직급(grade) 이 있으며 직급은 1에 가까워질수록 높은 직급이다.

직급을 승진시키거나 강등시키는 멤버 함수가 존재하며 직급 범위 (1~150)를 벗어날 경우 예외를 발생시킨다. 예외는 std::exception 형식의 객체로 throw 시킨다.

예외는 직급을 승진/강등/생성 연산자에서 생길 수 있으며 적당한 Getter를 설정해라.

이번 문제에서는 C++에서의 예외 처리 기법과 std::exception 클래스를 이용하여 예외를 핸들링해본다.

"예외 클래스" 에는 코플리언 폼을 적용하지 않는다.

### 학습해야 하는 내용

- C++ 예외 처리
- Nested Class (중첩 클래스)
- std::exception 클래스

### C++ 예외 처리

프로그래밍에서 예외란 프로그램이 정상적으로 실행되지 않는 조건이나 상황, 상태를 의미한다. 예를 들어 0으로 나누는 연산을 하거나 메모리 할당에 실패하거나와 같은 경우엔 프로그램이 중단되게 된다. 혹은 사용자가 지정하고 싶은 예외 상황도 있을 것이다.

보통 이런 상황이 발생하면 프로그램이 다운되거나 정상적인 동작을 보증하지 못한다. 그래서 적절히 예외를 처리해야 한다.

C에서는 보통 if문으로 예외가 발생할만한 조건을 직접 판단하고 처리해서 if에 걸리는 예외가 발생하면 처리하면 된다. 하지만 프로그램이 커지거나 예외 상황이 많아지거나 예외가 겹겹히 쌓여 있으면 가독성이 떨어진다.

예를 들어 무엇인가를 나누는 연산을 할 때 0으로 나누면 안되기 때문에 그런 연산이 있는 구절마다 일일히 if문으로 처리하기는 힘들 것이다. 또 예외가 발생할 때 서브루틴 콜에서 아예 벗어나야 하는 경우가 있을텐데 기존 조건문으로 이를 구현하기엔 제한된다. (그래서 C에서는 setjmp.h 라는 함수 콜을 벗어날 수 있도록 해주는 라이브러리를 제공한다.)

C++에서는 예외 처리를 언어 차원에서 지원한다. 예외 처리는 다음과 같이 세 가지 키워드로 지원한다.

```c++
try {
  // 예외가 발생할 수 있는 코드를 작성한다.
  if (/*예외 조건 1*/)
    throw (/*예외 관련 객체 1*/);
  if (/*예외 조건 2*/)
    throw (/*예외 관련 객체 2*/);
} catch (/*예외 관련 객체*/) {
  // 예외 관련 객체를 받아 와 처리한다.
}
```

키워드는 `try`, `throw`, `catch` 가 있다. 단어 그대로 이해하면 이해가 편하다. 코드 진행 중 throw 키워드를 만나면 곧바로 catch 문으로 이후의 코드를 실행하지 않고 건너뛰며 throw로 던져진 객체에 따라 에러를 적절히 처리한다.

try 문 내에서는 서브루틴 콜 내에서 throw로 에러를 던져도 그 함수 서브루틴 콜을 현재 catch문이 있는 지점까지 점프하여 던져진다. 특히 클래스와 같은 경우 알아서 파괴자까지 호출해주므로 에러 처리가 간편하다. 하지만 에러 처리에 대한 오버헤드가 있으므로 예외 처리는 상황에 맞게 사용하는 것이 좋다.

예를 들면 단순히 try문으로 감싸는 것에 대해선 오버헤드가 크게 발생하지 않으나 프로그램 용량이 조금 증가하고 catch 문으로 넘어갈 때에는 서브루틴 콜을 벗어나는 동작을 수행하는 데 오버헤드가 발생해 느려지게 된다.

### Nested Class (중첩 클래스)

중첩 클래스는 클래스 내 클래스가 선언된 형태를 의미한다. (자세한 설명 추후 기재 예정)

### std::exception 클래스

문제를 보면 예외 상황이 발생할 때 Bureaucrat::GradeTooHigh/LowException 로 던져지게 되어 있으며 예외는 std::exception 형식인 것을 볼 수 있다. 이를 통해 예외는 std::exception 형식의 객체로 던져져야 하며 Bureaucrat 클래스의 요소인 GradeTooHigh/LowException은 std::exception로부터 상속받은 클래스 형식이어야 함을 유추할 수 있다. 따라서 두 "예외"는 std::exception 클래스로부터 상속받은 형태로 작성해야 하며 문제에 적혀있듯이 std::exception 클래스엔 코플리언 폼을 적용하지 않는다.

## ex01

양식(Form) 클래스를 만들어라. 이 클래스는 이름(name), 서명(signed) 을 가지고 있고 signed 초기값은 false이다. sign 하기 위한 직급(grade)를 가지고 있고 execute 하기 위한 직급(grade)도 가지고 있다.

이름과 grede들은 상수이고 모든 어트리뷰트는 private다. grade들은 Bureaucrat 에서와 동일한 제약을 받으며 예외 발생 시 Form::GradeTooHighException and Form::GradeTooLowException 으로 던져진다.

beSigned 함수도 추가해야 하며 bureaucrat의 점수가 충분히 높으면 sign 한다. 그리고 항상 순위는 1>2 이며 grade가 너무 낮으면 Form::GradeTooLowException 예외로 던진다.

그리고 Bureaucrat 클래스에 signForm 함수도 추가한다. 만약 사인이 잘 되면 <bureaucrat> signs <form> 을 출력하고 안되면 <bureaucrat> cannot sign <form> because <reason> 을 출력한다.

결과적으로 양식 "Form"이 공무원 "Bureaucrat"에게 사인 받는 형태로 만들면 된다.

## ex02

Form을 기반으로 "관목림 조성 양식", "로보토미(Robotomy) 요청 양식", "대통령 사면 양식"을 만들어야 한다. 각각 요구하는 직급(허가 권한, 실행 권한)이 있다.

관목림 조성 양식 (ShrubberyCreationForm) 의 허가 권한은 145부터이며 실행 권한은 137부터이다. <target>_shrubbery  이라는 파일을 생성하여 파일에 아스키 코드로 나무 형태의 텍스트를 저장해라.

로보토미(Lobotomy - 전두엽 절제술에서 온 말인듯) 요청 양식 (RobotomyRequestForm) 의 허가 권한은 72부터이며 실행 권한은 45부터이다. 드릴 소리가 발생하며 50%의 확률로 시술이 성공하거나 실패한다.

대통령 사면 양식 (PresidentialPardonForm) 의 허가 권한은 25부터이며 실행 권한은 5부터이다. <target>가 Zafod Beeblebrox로부터 사면받았음을 알려라.

위 클래스들은 생성 시 클래스들을 대표하는 하나의 파라메터만 받아야 한다. (권한 값은 정의되있으므로 이름을 말하는듯) 예를 들어 "Home"이라는 이름에 대해 "관목림 조성 양식"을 적용할 경우 집에다 관목림을 꾸미는 걸 의미하는 식으로 한다. Form의 멤버 변수는 private 되어있는 점을 잘 상기해라.

form 클래스에 `execute(Bureaucrat const & executor) const` 를 추가해야 한다. 각 구상 클래스에 상세 동작을 구현해야 한다. form이 서명되어있는지 확인해야 하며 공무원이 권한이 있는지도 확인해야 한다. 권한이 없으면 예외를 발생시켜야 한다. 각각의 구상 클래스에서 이러한 것들을 체크하거나 Base 클래스에서 미리 검사를 하는 로직을 쓰거나는 본인 마음이다. 하지만 둘 중 한가지 방법이 보기에 더 나을 것이다. 하지만 어떤 경우에도 (당연하지만) Base 클래스는 추상 클래스이어야 한다.

bureaucrat 클래스에는 `executeForm(Form const & form)` 함수를 추가하며 form을 실행시킨다. 만약 실행에 성공하면 "<bureaucrat> executes <form>" 을 출력하며 실패하면 에러 메시지를 출력한다.

## ex03

양식을 작성하는 것은 귀찮으니 인턴 클래스한테 시키자.
인턴 클래스를 만드는데 (인턴이라) 이름, 직급은 없다.
인턴은 makeForm 멤버함수를 가지고 있다. 이 함수는 두 개의 문자열을 받는데 각각 폼의 이름과 폼의 대상을 의미한다.
이 함수는 포인터를 리턴하며 첫번째 인수(폼의 이름)로 표현되고 두번쨰 인수(폼의 대상)으로 초기화 된 구상 폼의 포인터를 반환한다.
그리고 표준 출력에 "Intern creates <form>" 를 출력한다.
"if-elseif-else"와 같은 않은 방법을 사용하지 말라. 폼이 존재하지 않을 경우 명시적인 오류 메시지를 출력한다.

팁) 함수 포인터와 배열을 사용하고 for문과 if문을 쓰면 쉽게 해결가능!