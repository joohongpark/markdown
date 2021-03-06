# Java 자료형

## 1. Java의 변수

변수라는 단어의 의미는 특정 값으로 고정되지 않고 변화할 수 있는 값을 의미한다. (變數 : 변화할 변, 세다 수)

변수는 프로그램이 실행될 때 프로그램 코드와 함께 메모리 (우리가 흔히 말하는 RAM을 의미함. 컴퓨터 사양의 8GB니 16GB니 하는 것) 내부에 할당이 된다. 컴퓨터 사양을 맞출 때 무거운 프로그램을 많이 돌린다면 보통 RAM 크기를 늘리는데, 여러 가지 이유가 있지만 대개 무거운 프로그램은 내부 변수가 많기 때문에 메모리 할당을 많이 필요로 하기 때문이다.

변수에는 메모리에 할당한 그 자체가 값의 의미를 가지는 단순 숫자나 문자를 나타내는 프리미티브 타입(Primitive Types)과 메모리에 값이 다른 메모리의 위치를 참조하는 레퍼런스 타입(Reference Type)이 있다. 예를 들면 프리미티브 타입의 변수는 값 그 자체를 나타내지만 레퍼런스 타입의 변수는 다른 위치를 참조하는 변수라는 의미이다.

자바의 데이터 타입 중 하기 8가지 프리미티브 타입을 제외하면 다른 모든 타입(배열, 클래스 등)은 메모리의 다른 위치를 참조하는 레퍼런스 타입이다.

## 2. Primitive Type

프리미티브 타입은 해당 변수 값 그 자체를 나타낸다.

```java
int a = 10;
```

다음 코드는 메모리에 정수형 공간을 할당하고 그 공간에  a란 이름을 붙인 후 10이라는 정수 값을 저장하라는 의미이다.

자바에서 프리미티브 타입은 다음과 같은 8가지 타입이 있다.

```java
byte b; // 정수형 1바이트
short s; // 정수형 2바이트
int i; // 정수형 4바이트
long l; // 정수형 8바이트
float f; // 실수형 4바이트 - IEEE 754 부동소수점 표준을 따름
double d; // 실수형 8바이트 - IEEE 754 부동소수점 표준을 따름
boolean t; // 논리형 1비트
char c; // 문자 2바이트
```

자바는 공식적으로 부호 없는 정수를 지원하지 않는다. 예를 들어 byte형은 1바이트 이므로 -128부터 127까지 표기 가능한데 부호 없는 정수를 지원한다면 0부터 255까지 표현 가능할 것이다. 하지만 자바에서는 해당 타입을 지원하지 않는다.

이는 자바를 창시한 사람이 프로그램을 복잡하게 하는 개념이라고 제외하였기 때문이다. 하지만 Java SE 8부터 int와 long 데이터 타입에 대해 Integer 및 Long 클래스의 관련 메소드를 통해 제한적으로 제공한다.

예를 들면 다음과 같다.

```java
int i = Integer.parseUnsignedInt("4294967295");
System.out.println(i); // -1
System.out.println(Integer.toUnsignedString(i)); // 4294967295
```

## 3. 정수, 실수, 논리, 문자 리터럴

리터럴은 고정된 값 그 자체를 의미한다. 예를 들어서 a란 정수형 변수에 10을 집어넣는다 라는 의미인 코드는 다음과 같다. 여기서 숫자 10을 리터럴이라고 한다.

```java
int a = 10;
```

리터럴에는 정수, 실수, 문자 등이 있다.

- 정수 리터럴
  - byte, short, integer
    - 10진수 (0, 1, 10000, ...)
    - 2진수 (0b01, 0b00001111, ...) - 접두사 0b로 시작하는 0과 1로 이루어진 수
    - 16진수 (0xaa, 0xffff, 0xAA, ...) - 접두사 0x로 시작되는 0 ~ F 까지의 수 (대소문자 구분하지 않음)
  - long : 상기와 동일하지만 되도록 숫자 뒤에 리터럴 접미사(대/소문자 L)를 붙여야 함
    - long l = 1000000000L;
- 실수 리터럴
  - float
    - 4바이트 실수형을 나타내는 float 리터럴 접미사(대/소문자 F)를 붙여야 함.
    - 예) 123.4f, 1.23f
  - double
    - 리터럴 접미사를 붙이지 않을 시 기본적으로 double형 리터럴로 인식한다.
    - 예) 123.4
  - 공통 (지수 표기법)
    - 실수는 가수, 밑, 지수로 변형하여 표기 가능하다.
    - 예를 들면, 12.34는 1.234 * 10^2 이며, 0.01234는 1.234 * 10^-2 인데 여기서 1.234가 가수이며 10은 밑, 2 혹은 -2는 지수이다.
    - 해당 표기법에서 밑은 항상 10이므로 가수와 지수만 기재한다.
    - 예제 : 12.34 == 1.234e2 / 0.01234 == 1.234e-2
    - 지수 표기법에 float 리터럴을 붙일 수 있다.
- 문자 (열)
  - 문자
    - 외따옴표 하나로 표기
    - 예) 'A', '박'
  - 문자열
    - 쌍따옴표로 문자열을 감싸서 표기
- 기타
  - Java SE7 이후로 리터럴 숫자와 숫자 사이에 언더바(underscore, _)를 넣어서 자리를 구분할 수 있다.
  - 예를 들면 0xffffffff이나 0b11010001 같은것은 자리를 구분하기 위한 가독성이 떨어지므로 각각 0xffff_ffff나 0b1101_0001 와 같이 적을 수 있다.
  - 또한 1_000_000와 같이 적어도 아무 문제 없다. 

## 4. 배열 및 배열 변수

배열은 특정 타입의 값을 메모리 내에 일렬로 나열하여 저장하는 형태이다.

자바에서의 배열 변수는 메모리 영역의 실제 배열 영역을 참조하는 Reference Type이다.

자바에서 기본적인 배열 선언, 초기화, 접근법은 다음과 같다.

```java
// 선언 - 배열 변수 intArr을 선언하며 메모리에 intArr라고 할당된 부분은 정수형 배열의 위치를 저장하는 공간을 의미한다. 해당 변수는 힙 영역에 저장된다.
int[] intArr;
byte[][] multiArr; // 다차원 배열도 선언 가능하다.
int[] ref; // intArr가 가리키는 주소를 복사하기 위한 변수

// 초기화 - 실제 스택 영역에 저장되는 정수형 배열의 크기와 메모리 위치를 동적으로 할당한다.
intArr = new int[2];
multiArr = new byte[2][2];
ref = intArr; // ref와 intArr이 가리키는 메모리는 동일해진다.

// 접근 - 대괄호 연산자의 인덱스를 이용하여 배열 메모리에 접근한다.
intArr[0] = 10;
intArr[1] = 20;
System.out.println(intArr[0] + ", " + intArr[1]);
System.out.println(ref[0] + ", " + ref[1]); // intArr과 ref가 가리키는 메모리 위치는 동일하므로 위 출력값과 동일하다.

multiArr[0][0] = 0;
multiArr[0][1] = 1;
multiArr[1][0] = 2;
multiArr[1][1] = 3;
System.out.printf("%d %d %d %d",
                  multiArr[0][0],
                  multiArr[0][1],
                  multiArr[1][0],
                  multiArr[1][1]);

// 배열 재할당을 통한 메모리 누수 -> Garbage Collactor가 알아서 정리해줌.
intArr = new int[5]; // 동일 주소에 신규할당을 하면 기존 정수형 배열은 garbage가 된다.
```

위에서 선언한 intArr이나 multiArr은 new 연산자를 이용해 할당한 배열 공간의 위치만을 가리키므로 마지막 라인과 같이 신규로 new 연산자를 이용해 신규 할당을 하면 기존 배열에 접근 불가능하다.

## 5. 클래스 자료형

클래스는 객체의 설계도라고 볼 수 있다. 따라서 클래스로부터 객체를 생성할 수 있으며 생성된 객체를  인스턴스라고 한다.

클래스를 참조하여 생성된 인스턴스는 메모리 어딘가에 할당이 되며 이 객체에 접근하기 위해 객체의 위치를 나타내는 자료형이 필요한데 이를 클래스 자료형이라 한다.

자바에서 배열 자료형을 대신해 널리 쓰이는 ArrayList를 예로 들어 보자.

```java
// 선언 - ArrayList라는 클래스 형태의 인스턴스를 사용하기 위해 클래스 자료형 선언
ArrayList<Integer> arrList;
ArrayList<Integer> arrList2;
ArrayList<Integer> objCpy;

// 초기화 - new 연산자를 이용해 ArrayList 클래스로부터 인스턴스를 생성하여 할당.
arrList = new ArrayList<>();
arrList2 = new ArrayList<>();
objCpy = arrList;

// 접근 - 선언된 arrList는 프리미티브 자료형이 아닌 객체이므로 내부 메소드를 이용하여 접근한다.
arrList.add(1);
arrList2.add(1);
System.out.println(arrList.get(0));

// 비교 - 프리미티브 타입이 아닌 자료형의 비교 연산
System.out.println(arrList == arrList2);
// >> 각각의 new 연산자를 이용하여 다른 영역에 할당되었으므로 false
System.out.println(arrList == objCpy);
// >> objCpy가 가리키는 곳은 arrList이므로 true
System.out.println(arrList2.equals(arrList));
System.out.println(arrList2.equals(objCpy));
// >> 실제 객체가 같거나 달라도 내부 값이 동일하므로 true
```
arrList, arrList2, objCpy 는 스택에 할당되어 있다. 이후 new 연산자를 사용하여 객체를 실제로 할당할 때 생성된 객체는 힙 영역에 저장되며 arrList, arrList2, objCpy 는 각각의 힙 영역의 위치를 가르킨다.

레퍼런스 타입을 비교할 때 흔히 격는 실수가  "==" 연산자를 이용하여 값 자체를 비교하고자 하는 일이다. 레퍼런스 타입에서 "==" 비교 연산자는 비교 대상이 동일한 위치를 레퍼런스하는지 판단하는 연산이며 일반적으로 레퍼런스 타입을 비교하게 될 때에는 equals 메소드를 이용한다.

## 6. 클래스 자료형 - 문자열

문자열  변수에 리터럴 문자열을 대입하여 선언할 때 보통 new 연산자를 사용하지 않고 쌍따옴표를 이용하여 선언하므로 프리미티브 타입으로 오래할 수 있는데, 자바에서 문자열 리터럴은 모두 문자열 클래스의 인스턴스로서 생성되는 클래스 자료형이다.

```java
// 선언 - new 생성자를 통하여 문자열을 입력해 생생하거나 문자 배열로부터 생성 가능.
char chrArr[] = {'문', '자', '열'};
String str1 = new String("문자열");
String str2 = new String("문자열");
String str3 = "문자열";
String str4 = "문자열";
String str5 = new String(chrArr);
String str6;

// new 생성자를 사용하지 않으며 동일한 리터럴을 통해 생성한 문자열은 가리키는 위치가 동일하다.
System.out.println("str1과 str2가 가리키는 위치는 같은가? : " + (str1 == str2));
// >> false

// 하지만 new 생성자를 사용할 때 동일한 리터럴을 사용하면 각자 다른 위치에 생성된다.
System.out.println("str3과 str4가 가리키는 위치는 같은가? : " + (str3 == str4));
// >> true

str6 = str4;
System.out.println("str6과 str4가 가리키는 위치는 같은가? : " + (str6 == str4));
// >> true
str4 += str4; // 해당 부분에서 신규로 힙 영역을 할당하게 됨.
System.out.println("합침 이후로도 동일한 위치를 참조하는가? : " + (str6 == str4));
// >> false
System.out.println(str6);
// >> 문자열
System.out.println(str4);
// >> 문자열문자열
```

문자열을 할당할 때 문자열 값은 힙 영역에 저장되므로 str1 ~ str5 모두 동일한 문자열 값을 힙 영역에 저장하고 있다. ("문자열" 이라는 문자열이 4개 연속 중복으로 나와있지만 실제 내부 String Pool 내부에 하나만 저장되어 있다.)

new 생성자를 이용하여 문자열을 할당한 str1과 str2는 각각 다른 위치의 힙에 공간을 할당한다. 하지만 new 생성자를 이용하지 않고 객체를 생성한 str2, str3은 String Pool이라는 별도 힙 영역에 저장된다.

new 연산자를 사용하지 않고 할당한 문자열은 동일한 문자열이 중복으로 다른 문자열 변수에 할당할 때 같은 위치를 가리키고 있다. (연산자 "==" 는 비교 대상이 프리미티브 타입이 아닐 경우 비교 대상의 위치-주소를 비교하는 연산이 된다.)

또한 문자열 클래스(String Class) 의 특징 중 하나는 값 자체가 변하지 않는 것이다. 위에 str6은 str4(str3)의 리터럴을 참조하도록 대입한 후 참조 위치를 비교하면 동일함을 확인할 수 있다. 그 다음 코드에서 str4의 자기 자신의 문자열을 이어붙이는 연산을 시행하고 나면 새로운 영역에 할당이 되어 그 문자열을 참조하게 된다. 따라서 해당 연산 이후로 str4와 str6이 참조하는 위치는 변하게 된다.

따라서 과도하게 문자열을 이어붙이거나 가공하는 연산이 들어갈때마다 새로운 String 객체가 생성되어 메모리를 낭비하게 된다.