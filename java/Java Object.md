# Java의 클래스, 인스턴스, 상속, 캡슐화 간단 설명

C와는 다르게 자바에서는 객체란 개념이 존재한다. 코드들이 단순한 명령이 아닌 행동이나 대상인 주체로 보아 프로그래밍을 하는 것을 보통 객체 지향 프로그래밍(OOP, Object Oriented Programming)이라고 한다.

객체는 개체들이 시스템 안에 일정한 소속과 규칙 없이 각각 따로 노는것이 아닌 특정한 규칙으로 그룹화한 것으로 객체 내에 개체들이 존재한다.

예를 들면 자동차나 사람은 각각 객체 (Object)라 볼 수 있다. 자동차가 도로를 달리는 행위나 사람이 밥을 먹는 행위 등은 개체 (Entity, 혹은 개체)라 볼 수 있다.

자동자나 사람은 상세적인 특성은 다르지만 자동차는 자동차끼리, 사람은 사람끼리의 공통점을 지니고 있다. 또 각각에 대하여 설계도가 존재한다. 사람은 DNA가 설계도가 되고 자동차는 자동차의 설계 도면이 된다. 이를 클래스와 인스턴스의 개념에 대입하면 설계도 및 DNA는 클래스가 되고 인스턴스는 자동차 혹은 사람이 된다.

자바에서 객체 지향 프로그래밍을 처음 접하는 사람들은 Object/Class/Instance 용어 간 차이와 관계에 대해서 헷갈리게 되는데 간단하게 요약하면 다음과 같다.

### 객체 (Object)

객체는 상태(state) 와 행동(behavior) (각각 내부 변수와 함수 - 필드와 메소드를 의미한다.)이 정의되어 있는 독립적인 대상의 집합(또는 규칙)을 의미한다. 현실에서도 상태와 행동을 가지고 있는 대상을 객체라고 부를 수 있다. 예를 들면 자동차의 상태는 시동 여부, 기어비, 차량 번호 등이 존재하며 행동은 기어 밟기, 기어 변속하기, 시동걸기 등이 존재한다. 자바 프로그래밍에서 상태는 필드 (클래스 내부 변수)로 나타내며 행동은 메소드 (클래스 내부 함수)로 부른다.

자바에서는 인스턴스를 개체라 부를 수 있다. (상위 분류 관계) 클래스로부터 생성된 인스턴스는 독립적으로 식별 가능하다. 하지만 클래스는 보통 객체라 부르지 않는다. 보통 클래스는 객체의 설계도를 의미하기 때문이다. 자동차 설계도나 빵 레시피를 자동차나 빵이라고 부르지 않는 것과 같다.

### 클래스 (Class)

클래스는 객체를 생성할 때 그 객체의 설계도 혹은 프로토타입을 의미한다. 위에서 말한 객체의 상태나 행동을 클래스를 이용하여 정의한다. 자바에서 생성할 객체가 어떤 형태를 가지고 어떤 기능을 가질지 클래스를 이용하여 정의한다.

필드(field)와 메소드(method)를 이용하여 클래스를 정의하는데 정의한다는 말은 현실 세계에서 설계도를 제작하는 것과 같다고 볼 수 있다.

### 인스턴스 (Instance)

인스턴스는 클래스(설계도)로부터 생성된 고유한 객체를 의미한다. 예를 들어 실제 세계에서 동일한 설계도를 이용해 제품을 제작한다 해도 그 제품의 사용자의 성향이나 제조사의 성향에 따라 다른 제품이 나온다. 같은 클래스로 만들었지만 결국 각각의 객체로써 존재하는 것이다.

신규 인스턴스가 생성될 때 마다 그 인스턴스를 위한 공간을 클래스로부터 생성하여 메모리에 신규로 할당하여 준다.

### 상속 (Inheritance)

자바에서는 상속이란 개념이 존재하는데 위에서 클래스가 설계도에 대응되는 개념이라고 했다. 실제 세계에서도 자동차를 설계할 때 기존 자동차의 엔진이나 프레임을 가지고 설계 변경하여 자동차를 만든다. 빵을 굽거나 요리를 할때도 마찬가지이다. 기존 레시피를 수정하는 식으로 요리를 한다. 왜냐하면 처음부터 만들기에는 시간과 비용이 많이 소모되기 때문이다.

자바에서도 상속을 이용하여 클래스를 선언하는 것을 지원한다. 예를 들어 기존에 덧셈과 뺄셈만을 지원하는 계산기를 클래스를 이용하여 선언하였다 가정하고 이후로 사칙연산 전부를 지원하는 계산기를 만든다고 하면 기존 계산기에 곱셈과 나눗셈만 추가하면 될 것이다.

상속 관계에 있는 클래스는 Superclass와 Subclass가 있다. Superclass는 상속을 해 주는 클래스로 베이스 클래스, 부모 클래스로도 불린다. Subclass는 상속을 받는 클래스로 확장 클래스, 자식 클래스로도 불린다.

참고로 자바에서 사용되는 모든 클래스는 Object라는 클래스를 상속받아 사용한다. 따라서 Object 클래스는 다른 모든 클래스에 대해 Superclass이며 다른 모든 클래스는 Subclass이다.

### 캡슐화 (encapsulation)

캡슐화는 필드와 메소드를 하나의 객체로 묶는 것을 말하며 해당 작업을 하며 내부 구현 내용 (필드나 메소드)에 접근 제한을 둔다. 생성자로 인스턴스를 생성하거나 기존 클래스로부터 상속을 할 때 등에 접근 제한을 둘 수 있는데 몰라야 하는 내용이나 몰라도 되는 내용에 접근하지 못하게 막는 것이다. 예를 들어 허가되지 않은 사용자가 객체에 접근하여 데이터를 변조할 수 있는 상황은 방지해야 한다. 현실 세계에 예를 든다면 집 비밀번호를 본인 혹은 가족만 알고 있고 타인은 모르는 것과 같다.

## 1. 클래스 선언 및 사용

자바 클래스의 단순한 사용 예제와 객체 변수 사용 예제이다.

```java
public class Main
{
	public static void main(String[] args) {
        // 클래스 타입 자료형의 객체 변수 선언 및 초기화
		calc c1 = new calc();
		calc c2 = new calc();
        
        // 클래스 타입 자료형의 객체 변수를 선언하여 다른 객체를 대입할 때
		calc c3;
		c3 = c1;
        
        // 프리미티브 자료형도 Wrapper Class를 이용해 사용할 수 있다.
        Integer i = new Integer(10);
        
		c1.x = 10;
		c1.y = 20;
		c1.add();
		c2.x = 100;
		c2.y = 200;
		c2.add();
		System.out.println(c1.result);
		System.out.println(c2.result);
		System.out.println(c3.result);
	}
}

class calc {
	int x, y, result;
	void add() {
	    this.test();
		result = this.x + this.y;
	}
	void test() {
		System.out.println("#TEST");
	}
}
```

calc라는 덧셈을 해주는 클래스를 이용해 new 연산자로 calc 클래스로부터 인스턴스를 생성하여 c1, c2 객체 변수에 할당하였다. c3은 선언만 수행한 후 기존 c1을 대입하였다.

기본적으로 c1, c2, c3의 객체 변수는 실제로 그 객체를 가지고 있는 것이 아니다. 정확히 말하면 c1, c2, c3은 메모리의 특정 영역(스택 영역)에서 다른 메모리를 가르키는 위치만을 저장하는 변수이며, new 연산자를 이용하여 인스턴스가 생성되면 생성된 인스턴스의 위치(힙 영역)를 가르키게 된다.

 따라서 c3 = c1 과 같은 코드를 수행할 때에는 새롭게 메모리를 할당하여 c1의 내용을 통째로 복사하는 것이 아니라 c1이 가리키고 있는 위치를 c3에도 대입하는 것일 뿐이다. c1과 c2는 같은 클래스 기반으로 만들었지만 각각 서로에 대해 독립성을 가진 객체이다. c1과 c3은 이름이 다르지만 본질적으로 같은 인스턴스이다. 따라서 연산 결과를 보면 c1과 c3이 같으며 c2가 다름을 확인할 수 있다.

코드를 보면 인스턴스의 메소드가 인스턴스의 내부 필드(변수)에 접근할 때 this 키워드를 사용하는 것을 볼 수 있는데 여기서 this는 현재 실행되고 있는 객체(생성된 인스턴스 그 자체)를 의미한다. 메인 인스턴스에서 생성된 calc 인스턴스의 내부 필드에 접근할 때 dot notation (.)을 이용해 접근하는데 해당 인스턴스 내 자신의 내부 필드나 메소드에 접근하거나 호출할 때 this를 사용한다. (필수로 this를 사용할 필요는 없다. 필드명이 중복될 때 주로 사용한다.)

Wrapper Class를 이용해 정수형 인스턴스를 생성한 i는 10이라는 값을 가지고 있는 인스턴스이다. 따라서 기존 프리미티브 자료형으로 선언한 변수는 단순히 숫자이지만 i는 객체이다. 따라서 보다 더 확장된 기능을 사용할 수 있다.

## 2. 간단한 상속 예제

다른 클래스로부터 상속을 받기 위해 extend라는 키워드를 사용한다.

```java
class calc {
	int x, y, result;
	void add() {
		result = this.x + this.y;
	}
}

class calc_plus extends calc {
	void sub() {
		result = this.x - this.y;
	}
}
/* 하기 클래스와 위 클래스는 기능적으로 동일함. */
class calc_plus_noExtend {
	int x, y, result;
	void add() {
		result = this.x + this.y;
	}
	void sub() {
		result = this.x - this.y;
	}
}
```

다음과 같이 선언할 경우 calc_plus는 슈퍼클래스 calc의 필드와 메소드를 상속받아 sub이라는 메소드를 추가한 클래스가 된다. 상속을 이용하면 번거롭게 동일한 필드와 메소드를 사용할 필요 없다.

super 키워드는 서브클래스에서 슈퍼클래스를 호출할 때 사용한다. 서브클래스 내에서 슈퍼클래스의 메소드나 필드에 직접 접근할 필요가 있을 때 사용한다. 간단하게 요약해서 설명하면

```java
class calc_plus extends calc {
	// super : calc 클래스를 의미
  // this : calc_plus 클래스를 의미
}
```

라고 이해하면 된다.

## 3. 클래스 멤버 접근 단계 (접근 제어자)

클래스를 작성할 때 캡슐화에 대하여 고려하여야 한다. 코드 설계 목적 등에 따라 접근 제한의 정도는 달라져야 한다. 자바에서는 클래스의 필드나 메소드에 접근할 수 있는 권한을 접근 제어자를 이용하여 설정할 수 있다. 외부에 노출되면 안되거나 노출시킬 필요가 없을 때 혹은 클래스 패키지의 관계에 따라 임의로 접근 단계를 설정한다.

접근 제어자는 클래스, 내부 메소드, 필드에 붙일 수 있다.

### 1. public

public이 붙은 요소는 해당 클래스와 상속 받은 클래스, 같은 패키지로 묶여 있는 클래스, 해당 클래스가 존재하는 패키지를 다른 코드에서 호출할 때 접근 가능하다.

subpkg라는 다른 패키지를 import 하여 그 패키지 내부에 있는 클래스 내부 필드나 객체에 접근이 가능하며, 상속도 가능하다.

```java
/*===================== mainpkg/mainClass.java =====================*/
package mainpkg;

import subpkg.*;

public class mainClass	{
	public static void main(String[] args) {
	    subClass sub = new subClass();
		otherClass other = new otherClass();

		// public - 내부(mainpkg)/외부(subpkg)패키지, 슈퍼/서브클래스 어디서든 접근가능
		System.out.println(sub.a); // 1
		System.out.println(other.a); // 1
	}
}

/*===================== mainpkg/subClass.java =====================*/
package mainpkg;

public class subClass extends subClass_origin {
}

class subClass_origin {
	public int a = 1;
}

/*===================== subpkg/otherClass.java =====================*/
package subpkg;

public class otherClass extends otherClass_origin {
}

class otherClass_origin {
	public int a = 1;
}
```

### 2. protected

protected이 붙은 요소는 해당 클래스와 상속 받은 클래스, 같은 패키지로 묶여 있는 클래스에서 사용 가능하며, 해당 클래스가 존재하는 패키지를 다른 패키지의 코드에서 호출할 때에는 접근이 불가능하다. 하지만 상속받아서 접근할 수는 있다.

public과의 차이는 다른 패키지를 import 하여 사용할 때 인스턴스를 통한 접근은 불가능하지만 상속을 통한 접근은 가능하다는 차이가 있다.

```java
/*===================== mainpkg/mainClass.java =====================*/
package mainpkg;

import subpkg.*;

public class mainClass {
	public static void main(String[] args) {
	    subClass sub = new subClass();
		otherClass other = new otherClass();
		mainclass_1 other1 = new mainclass_1();

		System.out.println(sub.a); // 1
		System.out.println(other.a); // err
		System.out.println(other1.geta()); // 1
	}
}

class mainclass_1 extends otherClass { // 상속받아서 해당 변수 접근 가능
	int geta() {
		return this.a;
	}
}

/*===================== mainpkg/subClass.java =====================*/
package mainpkg;

public class subClass extends subClass_origin {
}

class subClass_origin {
	protected int a = 1;
}

/*===================== subpkg/otherClass.java =====================*/
package subpkg;

public class otherClass extends otherClass_origin {
}

class otherClass_origin {
	protected int a = 1;
}
```

### 3. default

아무것도 붙지 않는 요소는 defalult 라고 부른다. 해당 클래스와 상속 받은 클래스, 같은 패키지로 묶여 있는 클래스에서만 접근 가능하다. 다른 패키지에서는 상속 및 접근이 불가능하다.

```java
/*===================== mainpkg/mainClass.java =====================*/
package mainpkg;

import subpkg.*;

public class mainClass {
	public static void main(String[] args) {
	    subClass sub = new subClass();
		otherClass other = new otherClass();
		mainclass_1 other1 = new mainclass_1();

        // default 요소는 동일한 클래스 및 패키지에서만 접근 가능하다.
		System.out.println(sub.a); // 1
		System.out.println(other.a); // err
		System.out.println(other1.geta()); // err
	}
}

class mainclass_1 extends otherClass { // 상속받아서 해당 변수 접근 가능
	int geta() {
		return this.a;
	}
}

/*===================== mainpkg/subClass.java =====================*/
package mainpkg;

public class subClass extends subClass_origin {
}

class subClass_origin {
	int a = 1;
}

/*===================== subpkg/otherClass.java =====================*/
package subpkg;

public class otherClass extends otherClass_origin {
}

class otherClass_origin {
	int a = 1;
}
```

### 4. private

protected이 붙은 요소는 해당 클래스에서만 사용 가능하다. 서브 클래스로 상속도 되지 않는다. 따라서 해당 필드에 반드시 접근해야 할 때 보통 getter / setter 기법을 활용한다.

```java
/*===================== mainpkg/mainClass.java =====================*/
package mainpkg;

public class mainClass {
	public static void main(String[] args) {
		subClass sub = new subClass();
		System.out.println(sub.b); // 1
		System.out.println(sub.a); // err
		sub.seta(10); // setter method
		System.out.println(sub.geta()); // getter method
	}
}

/*===================== mainpkg/subClass.java =====================*/
package mainpkg;

class subClass extends subClass_origin {
}

class subClass_origin {
	private int a;
	int b;
	int geta() {
		return a;
	}
	void seta(int a) {
		this.a = a;
	}
}
```

## 5. Static

인스턴스가 new 연산자를 통해서 생성될 때는 메모리에 인스턴스를 별도로 할당하여 생성되는 것이다. 하지만 내부 필드 중 여러 인스턴스에서 공유해 사용할 필요가 있거나, 메모리를 인스턴스 수만큼 할당하고 싶지 않을 때 Static 키워드를 사용한다.

Static 키워드가 붙은 대상은 프로그램이 실행될 때부터 끝날 때까지 유효하며 프로그램 실행 중간에 신규로 할당되거나 할당 해제되지 않는다.

### 1. Static field

해당 키워드가 붙은 필드는 클래스 변수 혹은 Static field라 부른다. 프로그램이 실행될 때 클래스 자체도 메모리에 올라가며 해당 클래스에 직접 접근하기 때문에 클래스 변수라고도 부를 수 있는 것이다.

```java
public class Main
{
	public static void main(String[] args) {
	    Sub o1 = new Sub();
	    Sub o2 = new Sub();
		System.out.println(o1.i);
		System.out.println(o2.i);
		System.out.println(Sub.i);
	    o1.inc();
		System.out.println(o1.i);
		System.out.println(o2.i);
		System.out.println(Sub.i);
	}
}

class Sub {
    public static int i;
    void inc() {
        i++;
    }
}
```

다음 코드를 보면 Sub 클래스로부터 인스턴스를 두개 생성하였다. Sub 클래스는 정수형 Static 변수 i와 그 변수의 값을 1 증가시키는 메소드로 이루어져 있다.

i는 static 키워드가 붙었기 때문에 인스턴스를 생성한다 하여도 i에 대한 메모리가 신규로 할당되지 않는다. 생성된 객체 o1, o2의 i 필드는 동일한 메모리 주소를 가리킨다.

따라서 o1.i와 o2.i, Sub.i는 동일한 메모리 주소를 가리킨다.

정적 필드에 접근할 때에는 보통 클래스명에 dot 접근자를 이용해 접근한다. 생성된 인스턴스 객체에 접근하여도 되지만 Static에 의미에 맞게 클래스에 접근하는 것이 권장된다.

클래스 변수는 클래스에서 가져다 쓸 상수 표기용으로도 유용하다. (상수 형태로 사용하려면 final 키워드를 붙인다. 필드에 final 키워드가 붙을 시 해당 키워드가 붙은 필드는 단 한번만 초기화 혹은 변경이 가능하다. 추후 상세 설명)

### 2. Static method

메소드는 static이 붙으면 정적 메소드가 되어 클래스로부터 메소드를 호출해야 한다.

메소드에 Static이 붙을 시 해당 메소드를 사용하기 위해 인스턴스를 생성할 필요가 없다.

정적 메소드를 호출할 때에는 필드와 마찬가지로 보통 클래스명에 dot 접근자를 이용해 접근한다. 생성된 인스턴스 객체에 접근하여도 되지만 Static에 의미에 맞게 클래스에 접근하는 것이 권장된다.

정적 메소드에서 비 정적 메소드나 필드에 접근하거나 호출할 수 없다. 프로그램이 실행될 때부터 메모리에 할당되는 대상이 언제 할당되는지 불명확한 대상을 포함하고 있으면 안되기 때문이다.

```java
public class Main
{
	public static void main(String[] args) {
	    Sub o = new Sub();
		System.out.println(o.add(1,2));
		System.out.println(Sub.add(3,4));
	}
}

class Sub {
    private int i = 0; // 임의의 비 정적 필드
    void none() { // 임의의 비 정적 메소드
    }
    public static int add(int a, int b) {
        //none(); - 에러
        //return a + b + i; - 에러
        return a + b;
    }
}
```

