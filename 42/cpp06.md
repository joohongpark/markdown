# C++ 입문 #6 (C++ Module 06)

C++의 캐스팅에 대하여 다루는 과제이다. 이번 과제에선 모든 캐스팅이 C++의 4가지 특수 캐스팅을 이용해서 수행되어야 한다.

## 학습해야 하는 내용

- C++ 캐스트 연산자

## C++ 캐스트 연산자

캐스팅을 안하는것이 최고지만 다형성을 위해서나 void 포인터를 쓸때나 숫자의 타입을 바꾸거나 할때는 캐스트 연산자를 쓸 일이 있다. C++에서는 캐스트 연산자를 세분화 해 놓아 상황에 맞는 혹은 안전하게 캐스팅을 할 수 있도록 도와준다.

기본적으로 4가지 캐스트 연산자를 지원해 주는데 각각 dynamic_cast, static_cast, const_cast, reinterpret_cast이다.

캐스트 연산자의 사용은 모두 `캐스트 연산자<변경할 타입>(대상)` 과 같은 형태로 한다.

### static_cast

이 키워드는 논리적으로 변환이 되는 타입으로만 변경을 허용해 준다. 예는 다음과 같다.

```c++
#include <iostream>

int	main(void)
{
	// int <-> long <-> float 형변환은 자연스럽다. 암시적으로도 형변환이 되지만 static_cast로도 가능하다.
	std::cout << static_cast<int>(10L) << std::endl;
	std::cout << static_cast<int>(10.23f) << std::endl;
	std::cout << static_cast<long>(10) << std::endl;
	std::cout << static_cast<long>(10.23f) << std::endl;
	std::cout << static_cast<float>(1000000000L) << std::endl;
	std::cout << static_cast<float>(-1) << std::endl;

	// 문자를 숫자(아스키)로 바꾸는것도 자연스럽다. 역도 자연스럽다.
	std::cout << static_cast<int>('a') << std::endl;
	std::cout << static_cast<char>(97) << std::endl;

	// 하지만 포인터를 다른 포인터 형식으로 변환하는 것은 안전하지 않으므로 허용하지 않는다. 컴파일러가 컴파일 시에 검사한다. (그래서 static cast임)
	int varInt = 10;
	float varFloat = 12.34;
	long varLong = 10L;
	char *varString = "text"; 
	std::cout << *(static_cast<float*>(&varInt)) << std::endl;
	std::cout << *(static_cast<int*>(&varFloat)) << std::endl;
	std::cout << *(static_cast<float*>(&varLong)) << std::endl;
	std::cout << *(static_cast<int*>(&varString)) << std::endl;

	return (0);
}
```

이 타입 캐스트 연산의 의의는 일반적으로 허용되지 않는 캐스트 연산을 컴파일 시에 발견하게 해준다.

### dynamic_cast

이 키워드는 클래스로부터 생성된 객체 간의 포인터 혹은 레퍼런스 간 형변환만 지원한다.

그래서 보통 부모-자식 간 형변환에 사용하는데 부모에서 자식으로 형변환 할 때 제약이 좀 있다.

- 클래스가 다형성을 가지고 있어야 한다 (Virtual 함수가 존재해야 한다.)
- 형변환을 하는 대상이 부모 클래스 포인터라 해도 원본은 자식을 가리키고 있어야 한다.

두가지가 지켜지지 않으면 오류가 발생한다. (혹은 캐스팅을 하지 않고 NULL을 반환한다.)

프로그램 실행 중 (런타임)에 타입 정보를 검사하여 동적으로 형변환한다. 이를 RTTI라 한다. 그래서 타입 캐스팅 에러를 잡을 수 있다.

```c++
#include <iostream>

class Parent {
	public:
		int parentsInt;
		Parent(int parentsint) : parentsInt(parentsint) {};
		virtual void doSomething(void) {std::cout << "do something" << std::endl;}
};

class Child : public Parent {
	public:
		int childInt;
		Child(int parentsint, int childint) : Parent(parentsint), childInt(childint) {};
		virtual void doSomething(void) {std::cout << "do something" << std::endl;}
};


int	main(void)
{
	Parent p(10);
	Child c(20, 30);

	Parent* pp;
	Child* cp;

	pp = dynamic_cast<Parent*>(&c);
	std::cout << pp->parentsInt << std::endl;
	cp = dynamic_cast<Child*>(pp);
	std::cout << cp->childInt << std::endl;
	cp = dynamic_cast<Child*>(p); // 오류
	std::cout << cp->childInt << std::endl;

	return (0);
}
```

### const_cast

const_cast는 특정 변수나 객체를 가리키는 포인터가 상수 속성을 가지고 있을 때 상수 속성을 없앨 수 있도록 해주는 키워드이다.

```c++
#include <iostream>

int	main(void)
{
	char str[] = "hello";
	int i = 10;

	const char* pstr = str;
	const int* pi = &i;

/*
	// 이렇게 하면 에러난다
	pstr[0] = 'k';
	*pi = 0;
	pstr_l[0] = 'k';
*/

	char* cpstr = const_cast<char*>(pstr);
	int* cpi = const_cast<int*>(pi);

	cpstr[0] = 'k';
	*cpi = 0;

	std::cout << cpstr << std::endl;
	std::cout << *cpi << std::endl;

	return (0);
}
```

### reinterpret_cast

reinterpret_cast는 형식이 아예 다른 포인터를 변환할 수 있도록 해주는 키워드이다. 예를 들면 이런 경우에 쓸 수 있다.

```c++
#include <iostream>

int	main(void)
{
	long l = 1234567890;
	float f = 12.34;
	uint8_t* c;

	c = reinterpret_cast<uint8_t*>(&l);
	for (size_t i = 0; i < sizeof(long); i++)
		std::printf("%02X\n", *(c + i));
	c = reinterpret_cast<uint8_t*>(&f);
	for (size_t i = 0; i < sizeof(float); i++)
		std::printf("%02X\n", *(c + i));
	return (0);
}
```

예를 들면 long랑 float의 실제 메모리에 바이트 단위로 접근하기 위해선 타입이 다른 포인터를 캐스팅해야 한다. C에선 위처럼 곧바로 캐스팅하지 못하지만 C++에선 가능하다.

## ex00

Makefile과 여러 코드들을 조합해 프로그램을 빌드할 수 있도록 만들어야 한다.

프로그램은 파라메터로 수의 리터럴 값을 받아 char, int, float, double으로 변환한다. 파라미터로 char, int, float, double 타입만 들어오며 10진수로만 입력된다.

char의 경우 디스플레이에 표기가 불가능한 경우 "Non displayable" 을 출력하며 그 이외에는 문자를 출력한다.

int의 경우 inf/-inf/nan 값은 "impossible" 으로 표기하며 그 이외에는 정상적으로 수를 출력한다.

float의 경우 float을 나타내는 f를 끝에 붙여 출력한다.

double의 경우 f가 붙지 않는다.

프로그램은 리터럴의 타입을 감지할 수 있어야 하며 문자열이 아닌 옳게 된 타입인지 검사한다. 그리고 "명시적으로" 타입들을 변환해야 한다.

오버플로우가 나면 "impossible" 으로 표기한다.

허용되는 함수는 문자열을 int/float/double으로 변환하는 함수가 허용된다.

char, int, float, double 중에서 double이 나머지 3가지 수도 포함해서 기록할 수 있다. (int - 4바이트, double - 8바이트) 그래서 문자열을 double으로 형변환 한 다음 각각의 숫자로 명시적 형변환을 수행하면 된다.

float - double 간 관계는 지수와 가수에 할당된 비트 길이가 모두 double이 더 길어 형변환해도 전혀 문제가 되지 않는다.

주의해야 할 점이 있는데 double이나 float의 min 값은 음수 방향의 최소값이 아니다. 부동 소수점 특성상 min/max 값은 그 비트 수료 표현 가능한 0에 가장 가까운 수/먼 수이기 때문에 float와 double을 비교할 때 오차 범위를 이용해야 한다.

문제에선 소수점 1째까지 표기하도록 간접적으로 표기되어 있어 오차 범위는 0.1로 잡고 float-double 간 형변환할 때 손실되는 수치가 0.1 이상이면 fail로 처리하였다.

## ex01

### 직렬화 (serialization)

여러 언어들엔 각자 내장된 특유의 자료형이 있다. IEEE 754 표준의 부동 소수점이나 8바이트의 길이를 가진 숫자나 이런 것들을 엮어둔 구조체나 클래스들도 있을 것이다.

이런 자료들을 파일로 저장하거나 다른 프로그램으로 IPC를 통해 보내거나 하려면 (보통)바이트 단위로 잘라서 접근해야 한다. 이를 직렬화라고 한다.

바이트 단위로 수신한 단순한 데이터들을 다시 의미를 가진 자료형으로 변환하는 행위는 역직렬화라고 한다.

C나 C++은 자료형을 다른 자료형으로 캐스팅하는 식으로 간접적으로 지원하며 자바에선 Serializable 인터페이스 형태로 지원한다.

직렬화 하는 함수는 구조체를 직렬화 하는데 구조체는 순차적으로 랜덤한 문자열의 포인터, 랜덤한 숫자, 랜덤한 문자열의 포인터이다.

팁) typeinfo 헤더 쓰면 안됨! 그리고 c/c++에서 구조체에 padding이 붙는 것을 고려해서 시리얼라이즈 할 때 void* 데이터에 패딩이 들어가 있으면 안됨! (구조체 포인터를 직접 형변환할 필요가 있을까?)