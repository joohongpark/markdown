# Java의 다형성과 오버로딩/오버라이딩

OOP(객체지향 프로그래밍)의 주요 개념 중 다형성(polymorphism) 이라는 개념이 있다. polymorphism의 사전적 의미는 한 집단 내에서 여러개의 형질이 구별되는 것을 의미한다. 보통 OOP에서의 다형성은 서로 다른 데이터형에 대해 동일한 형태로 인터페이스를 제공할 수 있는 능력을 의미한다.

실제 상황에서의 예를 들면 스마프폰을 이용해 음악을 듣는다 생각해보자. 음악을 들을 수 있는 단자는 보통 3.5파이 이어폰 잭, 블루투스, 충전 단자 3가지를 통해서 음악을 들을 수 있다. 3가지의 형질이 구별되는 대상에 대해 동일한 스마프폰으로 동일한 음원을 출력해줄 수 있는 것이다.

## 1. 다형성

자바에서 다음과 같은 함수(메소드)를 이용해 콘솔에 문자 혹은 숫자를 출력한다.



### 생성자 기본 예제

```java
public class mainClass {
	public static void main(String[] args) {
	    // 생성자를 통한 내부 필드 Initialize
	    constructor_ex e1 = new constructor_ex(20);
		System.out.println(e1.field); // 20
	}

}
class constructor_ex {
    public int field;
    constructor_ex(int field) {
        this.field = field;
    }
}
```

생성자 메소드는 반환값이 없다. 따라서 생성자에 자료형 키워드를 붙이지 않는다.

또 생성자의 이름은 클래스 이름과 동일하게 짓는다.

인수에는 초기화할 변수를 집어 넣어 초기화를 진행한다.

### 기본 생성자 / 생성자가 없을 시

아무런 인수도 없는 생성자 메소드를 기본 생성자라 한다. new 생성자를 통해 인스턴스를 생성할 때 아무런 인수도 넣지 않을 시 기본 생성자가 호출된다. 기본 생성자가 호출될 때 내부 필드들을 기본값으로 초기화를 수행한다. 

```java
public class Main
{
	public static void main(String[] args) {
	    constructor_ex1 e1 = new constructor_ex1(); // >> 해당 라인에서 "기본 생성자 호출" 출력
        constructor_ex2 e2 = new constructor_ex2();  // 기본 생성자는 없지만 내부필드 초기화
		System.out.println(e1.field);
	}
}

class constructor_ex1 {
    public int i;
    constructor_ex() {
        System.out.println("기본 생성자 호출");
    }
}

class constructor_ex2 { // 기본 생성자가 없을 때
    public int i;
    // 기본 생성자를 명시해놓지 않아도 컴파일 단계에서 기본 생성자를 호출하여 내부 필드를 초기화 한다.
    /*
    아래 코드를 작성한 것과 같은 동일한 효과를 발휘한다.
    constructor_ex() {
    	super(); // 부모 클래스의 생성자 기본 호출
    	i = 0; // 본인 클래스 필드 기본값으로 초기화
    }
    */
}

class constructor_ex3 { // 기본 생성자가 없을 때 3
    public int i;
    constructor_ex( int i ) {
    	this.i = 0;
    }
    // 위와 같은 경우에는 기본 생성자가 자동으로 생성되지 않기 때문에 기본 생성자를 작성하여야 한다.
    constructor_ex() {}
}
```

기본 생성자가 없을 때에는 자바 컴파일러가 암시적으로 기본 생성자를 호출하기 때문에 기본 생성자를 작성할 필요는 없지만 인수를 받는 생성자를 작성할 때에는 작성해야 한다.

### 다수의 생성자

내부 필드가 여러개일 때 선택적으로 원하는 값으로 초기화를 하고 싶을 것이며 그런 상황에 대해 생성자를 인수 개수나 형식에 따라 여러 개 생성 가능하다.

```java
*******************************************************************************/
public class Main
{
	public static void main(String[] args) {
	    constructor_ex e1 = new constructor_ex();
	    constructor_ex e2 = new constructor_ex(20);
	    constructor_ex e3 = new constructor_ex("String");
	    constructor_ex e4 = new constructor_ex(20, "String");
	    
		System.out.println(e1.i);
		System.out.println(e2.i);
		System.out.println(e3.s);
		System.out.println(e4.s);
	}
}

class constructor_ex {
    public int i;
    public String s;
    constructor_ex() { }
    constructor_ex(int i) {
        this.i = i;
    }
    constructor_ex(String s) {
        this.s = s;
    }
    constructor_ex(int i, String s) {
        this.i = i;
        this.s = s;
    }
    constructor_ex(String s, int i) {
        this.i = i;
        this.s = s;
        // 아래 코드로 대체가능
        // this(i, s);
    }
}
```

인스턴스 4개를 생성할 때 매개변수가 없으면 매개변수가 없는 생성자가 호출되며 정수 하나만 입력할 시 정수를 입력으로 받는 생성자가 호출된다. 문자열 하나만 입력할 시 문자열을 입력으로 받는 생성자가 호출된다.