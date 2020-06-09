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

람다식/람다함수는 익명함수를 지칭하는 함수/용어임. 자세한 내용에 대해서 파고 들면 끝이 없음. (분량이 너무 많으며 프로그래밍 기술적, 수학적 내용이 너무 방대함. 애초에 유래가 수학에서 옴)

람다 추상화 후 함수 : $\lambda x.x2+2$

와 같이 작성한다.

위 $f(x) = x^2 + 2$ 식을 보면 $f$ 라는 이름을 가진 함수명에 $x^2 + 2$ 라는 연산을 정의한 것이다. 하지만 동일한 의미의 람다 추상화를 한 함수식에는 함수 이름을 나타내는 표현이 없다. 프로그래밍에서 람다 함수는 함수명이 딸린 함수를 별도로 정의하지 않고 익명 함수를 정의하는 데 주로 사용한다. 또 표현이 람다 함수를 사용하는 사람 한정으로 간결하게 보이는 장점이 있다.

Java SE 8부터 Functional Interface 지원.

java.util.function 패키지엔 여러 인터페이스들이 등록되어 있는데,

일단 Functional Interface는 함수를 일급 객체로 쓸수 없어 도입됨. 구현해야 할 추상 메소드가 하나만 정의된 인터페이스의 경우 펑셔널 인터페이스라 부른다. (오브젝트 클래스의 메소드 제외함)

그래서 펑셔널인터페이스란 어노테이션이 새로 제공되는데 이 인터페이스가 펑셔널 인터페이스인지 확인함.





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