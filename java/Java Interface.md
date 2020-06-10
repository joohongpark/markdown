# Interface와 Abstract Class

## 1. 인터페이스란?

자바를 이용해 협업을 할 때 역할 분담을 해 모듈 별로 구현하게 될 것이다. 특정 모듈을 구현할 때 자바에서는 보통 클래스 단위로 구현하게 될 것이다. 클래스의 메소드나 인수 개수나 명칭에 대해 미리 개발자들끼리 사전 협의를 해 놓아야 추후에 모듈을 합칠 때 원활하게 진행될 것이다.

예를 들어 계산기 프로그램을 만든다 생각해보자. 팀원 A는 UI를 담당하고 팀원 B는 내부 핵심 계산기능을 수행하는 코어를 만든다 가정해보자.

계산기의 기능은 팀원 A와 B 둘 다 알고 있어야 한다. 팀원 A는 계산기의 계산 상세 로직은 알 필요 없지만 입력과 출력, 계산 로직의 명칭은 알고 있어야 한다. 팀원 B는 UI가 어떤식으로 구성되는지 상세히 알 필요는 없지만 UI에서 제공되는 계산 기능은 알고 있어야 한다.

팀원 A와 B가 공통적으로 알아야 할, 구현되어야 할 기능을 Interface를 이용하여 명세하는 것이다.

또는 클래스를 생성할 때 클래스에 어떤 기능이 들어가야 하는지, 어떤 값을 인수로 받아야 하는지를 틀 개념으로 제시하게 될 수도 있다. (하지만 해당 경우에서 일반적으로 상속을 사용한다.)

**<span style="color:red">하기 내용은 JAVA 8 기반으로 작성되었습니다.</span>**

## 2. 인터페이스의 기본적인 정의와 사용 예

인터페이스는 class 키워드 대신 interface 키워드를 사용한다. 또 클래스와 유사하게 인터페이스 간 상속도 지원한다.

```java
public interface circle extends constantField {
    int area(int r);
}
interface constantField {
    // 다음과 같이 필드도 선언 가능하다. 다음과 같이 선언하면
    // public static final 속성을 가진다.
    double PI = 3.141592;
}
```

위의 인터페이스를 사용하는 데 크게 두 가지 방법이 있다.

### 1. 추상 클래스로부터 상속

위 인터페이스로부터 클래스를 생성하는 예제이다.

```java
public class circleCalc implements circle {
    // Override 어노테이션은 선택사항이다.
    @Override
    public double area(int r) {
        return r*r*circle.PI;
    }
}


circleCalc o1 = new circleCalc();
circleCalc o2 = new circleCalc();
circle t1 = (circle) o1;
circle t2 = (circle) o2;
circle t3 = new circleCalc();
System.out.println(t1.area(30));
System.out.println(t2.area(60));
System.out.println(t3.area(90));
```

implements 키워드를 사용하며 클래스의 메소드를 구현하며 Override 어노테이션을 달지 않아도 해당 메소드가 구현되어 있지 않으면 컴파일이 되지 않는다.

또 위와 같이 인터페이스 명은 생성된 클래스/인스턴스의 Type으로 사용할 수 있다.

### 2. 익명 클래스를 이용

인터페이스를 클래스로 별도 구현하지 않고 아닌 익명 클래스를 생성하여 메소드를 오버라이딩 하는 방법이다. (오버라이딩 어노테이션이 있으며 인스턴스 네임도 존재하는데 왜 익명인가? 라는 의문이 들 수 있으나 하기 코드에서 오버라이딩 하는 코드를 정의한 클래스는 없다는 점을 상기하자.)

```java
circle c = new circle() {
    @Override
    public double area(int r) {
        return r*r*circle.PI;
    }
};

System.out.println(c.area(10));
```

위 코드에서 오버라이딩 한 area 메소드는 어느 클래스에서도 정의되지 않았으며 해당 인스턴스에서만 유효하다.

## 3. 디폴트 메소드와 스테틱 메소드

인터페이스를 정해 놓고 클래스를 제작할 때 추후에 기능 변경이나 사양 추가로 인터페이스를 수정해야 할 일이 생기게 된다. 만약 인터페이스에 신규 메소드를 추가 정의하면 해당 인터페이스를 구현하는 모든 클래스들은 그 메소드를 정의해야 하는 문제가 있다.

따라서 해당 상황에는 세 가지 방법이 있다.

- 모든 클래스에 해당 메소드 정의
- 신규 메소드가 추가되는 신규 인터페이스를 기존 인터페이스로부터 상속받아 정의
- default 메소드 형태로 신규 메소드 정의

default 메소드는 해당 메소드의 구현 클래스에서 정의되지 않아도 컴파일 오류를 발생시키지 않으며 신규 메소드에서 정의하지 않아도 해당 메소드를 가져다 사용할 수 있다. 신규 메소드에서 내용 수정이 필요할 경우에는 오버라이딩 하여 사용하여도 된다.

다음과 같이 default 타입의 메소드를 지정한다.

```java
public interface circle {
    double PI = 3.141592;
    double area(int r);
    default double roundDefault(int r) {
        return 2 * PI * r;
    }
}
```

위와 같이 지정하면 다음과 같이 클래스를 구현해서 사용한다.

```java
public class circleCalc implements circle {
    @Override
    public double area(int r) {
        return r*r*circle.PI;
    }
}
circleCalc obj = new circleCalc();
System.out.println(obj.roundDefault(30));
```

Static 메소드를 지정할 수도 있다. Static 메소드를 지정하면 해당 메소드는 클래스에서와 유사하게 인터페이스 메소드로 사용해야 한다.

```java
public interface circle {
    double PI = 3.141592;
    double area(int r);
    static double roundStatic(int r) {
        return 2 * PI * r;
    }
}
```

위와 같이 지정하면 다음과 같이 사용한다.

```java
public class circleCalc implements circle {
    @Override
    public double area(int r) {
        return r*r*circle.PI;
    }
}
System.out.println(circle.roundStatic(30));
```

## 4. 람다 함수

Java SE 8부터 지원된 기능이다. 인터페이스에 정의된 메소드가 하나일 때 사용 가능하다. 람다 함수에 대한 자세한 내용은 1930년대 람다 대수라는 이름으로 처음 소개된 내용이며 상세히 서술하기엔 너무 방대한 양이므로 간략하게 서술하면

람다 추상화 전 함수 : $f(x) = x^2 + 2$

람다 추상화 후 함수 : $\lambda x.x2+2$

와 같이 작성한다.

위 $f(x) = x^2 + 2$ 식을 보면 $f$ 라는 이름을 가진 함수명에 $x^2 + 2$ 라는 연산을 정의한 것이다. 하지만 동일한 의미의 람다 추상화를 한 함수식에는 함수 이름을 나타내는 표현이 없다. 프로그래밍에서 람다 함수는 함수명이 딸린 함수를 별도로 정의하지 않고 익명 함수를 정의하는 데 주로 사용한다. 또 표현이 람다 함수를 사용하는 사람 한정으로 간결하게 보이는 장점이 있다.

함수형 프로그래밍(기존 명령형 프로그래밍에서 프로그램을 함수 자체의 선언이 아닌 수의 계산에 초점을 맞춘 람다 함수의 이론적 배경에서 나온 프로그래밍 패러다임. 이후 상세 작성 예정)에서 1급 객체(First-class citizen/object)란 개념이 있다. 1급 객체 대한 조건은 다음과 같다.

> - 일급 객체의 요소는 함수의 매개변수가 될 수 있다.
> - 일급 객체의 요소는 함수의 반환 값이 될 수 있다.
> - 일급 객체의 요소는 할당 명령문의 대상이 될 수 있다.
> - 일급 객체의 요소는 동일 비교의 대상이 될 수 있다

요약하자면 1급 객체는 변수나 데이터 구조안에 삽입 가능하며 파라미터로 전달 가능해야 한다. 또 반환값 자체로 사용 가능해야 한다.

기존 자바에서는 함수(메소드)를 일급 객체로 사용할 수 없었다. 이후 Java SE 8부터 해당 단점을 보완하기 위해 Functional Interface를 지원하기 시작했다.

**Functional Interface란 자바 인터페이스가 구현해야 할 추상 메소드가 단 하나만 정의된 인터페이스를 의미**하며, 해당 인터페이스는 하나의 기능만을 수행하는 것을 표현한다. (하나의 인터페이스는 하나의 메소드만 지원해 하나의 함수 기능을 수행한다.)

이 인터페이스가 functional Interface인 지 명확하게 하기 위해 `@FunctionalInterface` 어노테이션을 지원한다.

자바에서의 람다식 사용은 해당 형식의 인터페이스를 기반으로 사용하는데 일반적인 사용 예제는 다음과 같다. (내용이 너무 방대하므로 추후에 상세작성 예정)

```java
// lambdaInterface.java
@FunctionalInterface
public interface lambdaInterface {
    void func();
}

// main class
lambdaInterface l = () -> {System.out.println("lambda test");};
l.func(); // lambda test
```

사용 예제는 위 익명클래스와 유사하게 즉석에서 함수(인스턴스) 내용을 정의하여 사용하는 것이 유사하다. 하지만 람다식은 기존 레거시 코드(Java SE 8 이전)와 호환되지도 않으며 기존 자바 문법과도 다르다. 또 특정 패턴에서는 바이트코드로 변환 시 더 느려지는 경우도 존재하므로 함수형 프로그래밍에 익숙해지기 전까지 사용하는 것을 지양하는 것이 좋다.

## 5. 추상 클래스

추상 클래스는 추상 메소드 (abstract 키워드가 붙어 메소드 선언만 되어있는 형태)를 포함하고 있는 메소드를 추상 클래스라고 한다. 추상 클래스는 abstract 키워드가 붙어야 한다.

추상 클래스는 상속 또는 생성자를 통한 메소드 구현을 강제한다. 따라서 사용되는 환경에 
따라 메소드 내용이 수시로 변경되어야 하는 클래스에 적합하다. (사실 인터페이스와 추상 클래스는 메소드의 구현을 강제함으로 서로 유사한 기능을 제공한다.)

선언 예제는 다음과 같다.

```java
public abstract class RegularPolygon { // 정다각형
    protected double angle; // 내각
    protected double sideLength; // 변
    public abstract double area(); // 넓이 - 다각형의 쉐입에 따라 달라짐.

    public RegularPolygon() {}
    public RegularPolygon(int angle, int sideLength) {
        this.angle = angle;
        this.sideLength = sideLength;
    }
}
```

위 추상클래스는 정다각형 클래스를 정의한 것이다. 정다각형의 넓이 구하는 공식은 다각형의 형태에 따라 달라지게 된다.

### 1. 추상 클래스로부터 상속

위 추상 클래스로부터 정사각형 클래스를 생성하는 예제이다.

```java
class Square extends RegularPolygon {
	Square(int angle, int sideLength) {
		super(angle, sideLength);
	}
	@Override
	public double area() { // 정사각형의 넓이
		return sideLength * sideLength;
	}
}


Square s = new Square(90, 180);
System.out.println(s.area()); // 32400
```

위 인터페이스 예제와 유사하다. 

### 2. 익명 클래스를 이용

추상 클래스로부터 상속받아 새로운 클래스를 생성하는 것이 아닌 익명 클래스를 생성하여 메소드를 오버라이딩 하는 방법이다.

```java
RegularPolygon triangle = new RegularPolygon(45, 20) { // 정삼각형
    @Override
    public double area() {
        return (sideLength * sideLength) * (Math.sqrt(3) / 4);
    }
};
System.out.println(triangle.area()); // 173.~~~~
```

인터페이스 예제와 마찬가지로 위 코드에서 오버라이딩 한 area 메소드는 어느 클래스에서도 정의되지 않았으며 해당 인스턴스에서만 유효하다.