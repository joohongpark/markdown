# Java의 다형성과 오버로딩/오버라이딩

OOP(객체지향 프로그래밍)의 주요 개념 중 다형성(polymorphism) 이라는 개념이 있다. polymorphism의 사전적 의미는 한 집단 내에서 여러개의 형질이 구별되는 것을 의미한다. 보통 OOP에서의 다형성은 서로 다른 데이터형에 대해 동일한 형태로 인터페이스를 제공할 수 있는 능력을 의미한다.

실제 상황에서의 예를 들면 스마프폰을 이용해 음악을 듣는다 생각해보자. 음악을 들을 수 있는 단자는 보통 3.5파이 이어폰 잭, 블루투스, 충전 단자 3가지를 통해서 음악을 들을 수 있다. 3가지의 형질이 구별되는 대상에 대해 동일한 스마프폰으로 동일한 음원을 출력해줄 수 있는 것이다.

## 1. 다형성의 예

자바에서 다음과 같은 함수(메소드)를 이용해 콘솔에 문자 혹은 숫자를 출력한다.

```java
System.out.println( "String" ); // String
System.out.println( 123 ); // integer
System.out.println( 123.34 ); // double
```

println이라는 메소드는 위에서 문자열, 정수, 실수형 가리지 않고 해당 내용을 콘솔로 출력해준다. 만약에 다형성 체계를 지원하지 않는다면 각각의 인수에 따라 별도로 메소드 이름을 지어야 할 것이다. (println_String이나 println_integer 와 같은 식으로)

프로그래밍에서 다형성 중 가장 큰 부분은 위와 같은 사례로 비슷한 형태로 함수의 성질이 다른 입출력 인터페이스나 객체 접근 등을 가능하게 해 주는 것을 가능하게 하는 것을 의미한다.

자바에서는 오버로딩, 오버라이딩, 객체의 업/다운캐스팅 등으로 다형성을 지원한다.

### 2. 객체의 업캐스팅과 다운캐스팅

```java
package Exercise;
public class Exercise7 {
	public static void main(String[] args) {
		chicken a = new chicken("호식이", "양념", 2);
		chicken b = new chicken("교촌", "굽네", 1);
		ovenChicken c = new ovenChicken("굽네", "땡초", 2, true);
		a.print();
		b.print();
		c.print();
		// 1. Upcasting 암시적 형변환 (생성자를 직접 대입하거나 기존 객체를 대입할 수 있음)
		chicken d = new ovenChicken("지코바", "소금", 3, true);
		// 2. Downcasting 묵시적 형변환 (생정자를 직접 대입하거나 기존 객체를 대입할 수 있음)
		ovenChicken e = (ovenChicken) c;
		d.print();
		e.print();
	}
}

class chicken {
	public String chicken;
	public String source;
	public int qty;
	chicken(String chicken, String source, int qty) {
		this.chicken = chicken;
		this.source = source;
		this.qty = qty;
	}
	void print() {
		System.out.printf("%s 소스를 곁들인 %s 치킨 %d마리\n", source, chicken, qty);
	}
}
class ovenChicken extends chicken {
	public boolean isOven;
	ovenChicken(String chicken, String Source, int qty, boolean isOven) {
		super(chicken, Source, qty);
		this.isOven = isOven;
	}
	void print() {
		System.out.printf("%s 소스를 곁들인 오븐에 %s %s 치킨 %d마리\n", source, isOven ? "구운" : "굽지 않은", chicken, qty);
	}
}
```

위 코드는 객체의 업캐스팅과 다운캐스팅을 한 예제이다. 치킨의 종류를 브랜드, 소스, 마리 수로 지정해놓은게 chicken 클래스이며 이후 치킨을 구웠는지의 여부를 추가한 클래스가 ovenChicken이다.

기존 치킨 클래스가 오븐치킨 클래스와 다르다고 하여도 둘 다 치킨이기 때문에 기존 치킨이라는 공통된 성질을 다룰 때 업캐스팅을 주로 사용한다. 신규 오븐치킨 클래스는 기존 슈퍼클래스인 내부 요소를 다 갖추고 있기 때문에 오븐치킨 클래스를 치킨 클래스로 변환한다고 생각하면 쉽다.

예를 들어 구운 메뉴가 없는 치킨과 구운 메뉴가 있는 치킨에 대해 별도의 인스턴스를 생성할 때 공통 속성에 대해 접근할 일이 있을 수 있다. 업캐스팅을 이용하면 쉽게 공통 요소에 접근 가능하다.

업캐스팅을 할 때에는 암시적 형변환이 되기 때문에 캐스팅을 위한 키워드를 붙일 필요가 없다.

다운캐스팅은 업캐스팅 된 객체를 역으로 원상복구 하는데 사용한다. 다운캐스팅을 할 때에는 형변환을 위한 키워드를 붙여야 한다. (명시적 형변환)

업캐스팅과 다운캐스팅을 잘 활용하면 타입이 다른 객체에 대해 별도로 객체의 성질을 판별하여 처리하는 조건문을 삽입하지 않아도 되기 때문에 자바의 다형성의 좋은 예 중 하나이다.

## 2. 오버로딩 (Overloading)

동일한 클래스 내에서 동일한 이름을 가지고 있지만 인수 형태나 인수 개수 (이를 메소드의 시그니쳐라 한다.) 가 서로 다른 메소드를 만드는 것을 오버로딩이라 한다.

```java
typecheck t = new typecheck();
t.check('a');
t.check(12);
t.check("문자열");

class typecheck {
    void check(String str) {
        System.out.println("문자열입니다");
    }
    void check(int i) {
        System.out.println("정수입니다");
    }
    void check(char c) {
        System.out.println("문자입니다");
    }
    // error
    /*
    int check(int i) {
        System.out.println("정수입니다");
        return i;
    }
     */
}
```

check라는 메소드를 인수형에 따라 오버로딩하여 위와 같이 동일한 메소드명인 check를 사용하여도 인수 자료형에 따라 호출되는 메소드가 달라진다.

## 3. 오버라이딩 (Overriding)

클래스를 상속받을 때 슈퍼 클래스에서 정의되어 있는 메소드를 자식 클래스에서 재 정의 하는 것을 오버라이딩이라 한다. 오버라이딩을 하게 되면 자식 클래스에서 인스턴스가 생성되어 오버라이딩 된 메소드를 실행할 때 슈퍼클래스의 메소드가 아닌 자식클래스의 메소드가 호출된다.

```java
class typecheck {
    void check(String str) {
        System.out.println("문자열입니다");
    }
    void check(int i) {
        System.out.println("정수입니다");
    }
    void check(char c) {
        System.out.println("문자입니다");
    }
}
class typecheckDevelop extends typecheck {

    void check(char c) {
        System.out.print(c + "는 ");
        super.check(c);
    }

    @Override
    void check(String str) {
        System.out.print(str + "는 ");
        super.check(str);
    }

    @Override
    void check(int i) {
        super.check(i);
    }
}
```

다음은 위 typecheck 클래스를 발전시키기 위해 typecheckDevelop이라는 자식 클래스를 선언한 것이다. 기존에 존재하는 메소드에 입력된 인수를 출력해주는 기능을 추가한 것이다. 자식 메소드에 시그니쳐가 동일한 메소드를 선언하면 된다.

재정의  된 메소드에 "@Override" 라는 어노테이션이 붙어 있는 것이 있고, 안 붙어 있는 것이 있는데 해당 어노테이션은 컴파일러에게 "이 메소드는 슈퍼 클래스로부터 상속받아 오버라이딩 하는것이다." 라는 것을 명시한 것이다. 프로그래머가 슈퍼클래스의 메소드 명이나 시그니쳐를 다르게 적으면 오버라이드 되지 않기 때문에 컴파일러에게 명시를 하는 것을 의미한다. 어노테이션이 붙어 있는데 오버라이드가 되지 않으면 컴파일러는 오류를 출력한다.