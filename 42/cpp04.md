# C++ 입문 #4 (C++ Module 04)

이 과제는 추상 클래스, 인터페이스, 다형성을 다룬다.

## ex00

총 3개의 클래스를 만든다.

- Sorcerer
  - std::string name
  - std::string title
  - 생성자 (NAME, TITLE, is born!)
  - 소멸자 (NAME, TITLE, is dead. Consequences will never be the same!)
  - ostream의 `<<` 연산자 오버로딩 (I am NAME, TITLE, and I like ponies!)
  - void polymorph(Victim const &) const 멤버함수 추가
    - getPolymorphed() 호출
  - void polymorph(Peon const &) const 멤버함수 추가
    - getPolymorphed() 호출
- Victim
  - std::string name
  - 생성자 (Some random victim called NAME just appeared!)
  - 소멸자 (Victim NAME just died for no apparent reason!)
  - ostream의 `<<` 연산자 오버로딩 (I'm NAME and I like otters!)
  - void getPolymorphed() const
    - "NAME has been turned into a cute little sheep!" 출력
- Peon
  - A Peon is a Victim (상속하란 말)
  - 생성자 ()
  - 소멸자 ()
  - void getPolymorphed() const
    - "NAME has been turned into a pink pony!" 출력
- 예제 main 코드대로 동작되도록 해야 함.

## ex01

"A Weapon" 클래스를 만든다. 이름, 타격 데미지 점수, AP 비용 멤버변수를 가지고 있고 캐노니컬 폼을 위한 멤버함수와 멤버변수에 대한 getter가 있으며 attack이라는 가상함수를 가지고 있다. attack 함수는 자식 클래스에서 동작된다.

그리고 `Concrete Class` 인 PlasmaRifle과 PowerFist 클래스를 "A Weapon" 클래스로부터 만든다.

이어서 "Enemy" 클래스도 만든다. "HP"와 문자열 타입의 "타입"을 멤버변수를 가지고 있고 캐노니컬 폼을 위한 멤버함수와 멤버변수에 대한 getter가 있고 가상함수 "takeDamage" 를 가지고 있다. HP는 0 밑으로 떨어지면 안된다.

그리고 SuperMutant, RadScorpion를 만든다.

Character 클래스도 만든다. 이름, AP 속성을 가지고 캐노니컬 폼을 갖추고 있고 recoverAP와 인수로 AWeapon을 받는 equip과 Enemy를 인수로 받는 attack이 있다. name에 대한 getter도 있다.

AP는 생성 시 40을 가지고 있으며 무기를 사용할 때 소유하고 있는 무기에 따라 AP가 감소된다.  recoverAP는 10AP씩 복구가 되며 최대 40AP까지만 복구된다.

attack 호출 시 "NAME attacks ENEMEN_TYPE with a WEAN_NAME"가 출력된다. 그리고 현재 등록된 무기의 공격 멤버함수가 호출된다. 무기가 등록되어 있지 않으면 아무것도 하지 않는다. 적의 HP가 0이면 그 적은 "삭제" 된다.

equip 함수는 무기의 포인터만을 저장한다.

ostream의 << 연산자에 Character가 동작되도록 해야 하며 상황에 따라 무기가 있으면 "NAME has AP_NUMBER AP and wields a WEAPON_NAME" 라고 출력되게 하고 무기가 없으면 "NAME has AP_NUMBER AP and is unarmed" 가 출력되게 한다.

- AWeapon
  - std::string name;
  - int apcost;
  - int damage;
  - AWeapon(std::string const & name, int apcost, int damage);
  - std::string getName() const;
  - int getAPCost() const;
  - int getDamage() const;
  - virtual void attack() const = 0;
- PlasmaRifle
  - name : Palsma Rifle
  - damage : 21
  - AP Cost : 5
  - Output of attack(): "* piouuu piouuu piouuu *"
- PowerFist
  - name : Power Fist
  - damage : 50
  - AP Cost : 8
  - Output of attack(): "* pschhh... SBAM! *"
- Enemy
  - int hp;
  - std::string type;
  - std::string [...] getType() const;
  - int getHP() const;
  - virtual void takeDamage(int);
- SuperMutant
  - HP : 170
  - Type : Super Mutant
  - 태어날 때 : "Gaaah. Me want smash heads!"
  - 죽을 때 : "Aaargh..."
  - takeDamage : 입력된 데미지에 -3 빼서 적용
- RadScorpion
  - HP : 80
  - Type : RadScorpion
  - 태어날 때 : "* click click click *"
  - 죽을 때 : "* SPROTCH *"
- Character
  - int ap;
  - std::string name;
  - Character(std::string const & name);
  - void recoverAP();
  - void equip(AWeapon*);
  - void attack(Enemy*);
  - std::string [...] getName() const;

### 학습해야 하는 내용

- 추상 클래스와 구상 클래스
- 업캐스팅/다운캐스팅
- 가상 함수
- 순수 가상 함수

### 추상 클래스와 구상 클래스

Abstract Class가 추상 클래스이며 Concrete Class가 구상 클래스이다. OOP에선 보통 공통적으로 존재하는 개념인데 추상 클래스는 상속을 이용해서 사용할 수 밖에 없도록 강제하는 클래스이다. 상속을 받을 때에만 사용가능하므로 추상 클래스는 인스턴스화가 불가능하다.

상속을 강제하는 이유는 클래스 내의 특정 기능 구현을 자식 클래스에게 강제하는 것이다. 이 클래스로부터 상속되는 클래스를 보통 구상 클래스라고 부른다. 구상 클래스는 인스턴스화가 가능하다.

### 업캐스팅과 다운캐스팅

부모클래스로부터 생성된 인스턴스와 자식클래스로부터 생성된 인스턴스가 있을 때 상호간 변환이 가능할까? 혹은 부모클래스를 가리키는 포인터와 자식클래스를 가리키는 포인터는 서로를 가리킬 수 있을까?

자식 클래스는 부모 클래스가 가지고 있는 요소를 다 가지고 있으니 이론적으로는 자식 클래스 형식으로부터 부모 클래스 형식으로 형변환이 가능할 것이다. 실제로 가능하고 이는 암시적으로 형변환이 가능하다.

예를 들면 다음과 같다.

```c++
#include <iostream>

class Parent {
	protected:
		int data;
	public:
		int getData(void);
		void setData(int i);
		void print(void);
};

int Parent::getData(void) {return(data);}

void Parent::setData(int i) {data = i;}

void Parent::print(void) {std::cout << "부모의 멤버 함수" << std::endl;}

class Child : public Parent {
	protected:
		int data1;
	public:
		int getData1(void);
		void setData1(int i);
		void print(void);
};

int Child::getData1(void) {return(data1);}

void Child::setData1(int i) {data1 = i;}

void Child::print(void) {std::cout << "자식의 멤버 함수" << std::endl;}

int main(void) {
	Child c;

	c.setData(10);
	c.setData1(20);

	Parent p1 = c; // 대입
	Parent *p2 = &c; // 포인터 참조
	Parent& p3 = c; // 레퍼런스 참조

	std::cout << c.getData() << std::endl; // 10
	std::cout << p1.getData() << std::endl; // 10
	std::cout << p2->getData() << std::endl; // 10
	std::cout << p3.getData() << std::endl; // 10

	c.print(); // 자식의 멤버 함수
	p1.print(); // 부모의 멤버 함수
	p2->print(); // 부모의 멤버 함수
	p3.print(); // 부모의 멤버 함수

	return (0);
}
```

parent 클래스와 child 클래스를 선언했다. 오버라이딩 되는 함수 print도 있다. child 클래스로부터 생성된 인스턴스를 여러 형태로 부모클래스 형으로 암시적 형변환을 시키는 예제이다.

업캐스팅(Upcasting) 이란 위 예제처럼 자식 클래스의 포인터나 객체가 부모 클래스의 포인터로 형변환 되는 것을 의미한다. 자식 객체가 가지고 있는 요소들은 부모 객체가 다 가지고 있으므로 자연스럽게 형변환이 일어나게 된다.

다운캐스팅은 업캐스팅의 역으로 캐스팅을 하는 것인데 이런 경우 자식 객체가 가지고 있지 않은 요소에 대해 대입이 되지 않는 문제가 있으므로 암시적 형변환이 금지된다. 직접 명시적으로 캐스팅을 하여야 한다.

중요한 것은 이 문제로 돌아가 보면 캐릭터 클래스에서 equip(AWeapon*) 함수의 인자가 무기들의 부모 클래스형으로 받는다는 것이다. 이렇게 받는 이유는 무기마다 서로 다른 클래스로 만들어야 할 필요가 있는데 무기 하나하나마다 오버로딩을 위한 함수들을 일일히 만들어주면 비효율적이며 다형성을 위해 저 함수로 부모로부터 파생된 자식들을 한 함수에서 관리할 수 있도록 한다.

그래서 OOP에선 부모는 자식을 가리키거나 대입하게 할 수 있도록 허용된다.

### 가상 함수

그런데 위 예제를 잘 보면 오버라이딩 된 print 함수가 객체냐 포인터(혹은 레퍼런스) 모두 동일하게 부모의 함수를 호출한다. 이것은 컴파일러가 컴파일을 할 때 객체 혹은 포인터의 타입을 파악하고 적절하게 멤버 함수(코드가 위치한 주소)를 가리키도록 한다. 이를 바인딩이라고 한다.

p1 객체는 부모 클래스로부터 생성되었기 때문에 부모 함수를 호출하는 것이 자연스럽다. 하지만 객체나 레퍼런스같은 참조형은 가리키고 있는 대상이 자식 클래스이기 때문에 오버라이딩 된 함수를 호출하는 것을 원할 수 있다. 하지만 컴파일러는 정적으로 포인터의 타입을 보고 호출될 함수를 정해버린다. 이런 것을 정적 바인딩이라고 한다.

이런 상황을 막기 위해 포인터나 레퍼런스가 가리키는 함수를 런타임에 동적으로 정할 수 있도록 해주는 함수가 가상 함수이다. 포인터 타입이 아니라 포인터가 가리키고 있는 객체의 타입에 따라 함수를 자동으로 고르게 해준다.

위 코드에서 부모 클래스의 오버라이딩 될 함수에 `virtual` 키워드를 붙이면 가상 함수가 된다. 클래스 선언 외부에서 동작을 선언하는 함수에는 키워드를 붙이지 않는다. 자식 클래스의 오버라이딩 하는 함수에는 붙일 필요는 없지만 구분을 위해서 붙이는 것이 좋다.

가상 함수의 정의와 동작은 C++ 표준에 정의가 되어 있지만 실제 동작 방식은 정의해놓지 않았다. 그래서 컴파일러마다 동작 방법이 다를 수 있지만 보통 클래스에 대해 가상 함수들의 주소 목록(가상 함수 테이블)을 내부적으로 작성해서 함수 호출시에 적절한 함수가 호출되도록 한다. C++에서는 가상 함수를 통해 동적 바인딩을 지원한다.

참고로 자바에서는 이런 경우 자동으로 오버라이딩 된 메소드를 호출해 준다. 알아서 동적 바인딩을 해준다.

#### 가상함수를 사용해야 하는 경우 - 파괴자

부모 클래스의 생성자에 동적 할당을 하는 코드가 있고 파괴자에 할당을 해제하는 코드가 있다고 가정하자. 또 자식 클래스에서 부모 클래스와는 다르게 새로운 동적 할당 요소를 추가하고 자식 클래스의 파괴자에 그 요소를 해제하는 코드가 있다고 해보자.

만약 객체로써 생성되면 기존에 알고 있던대로 부모 생성자 - 자식 생성자 - 자식 파괴자 - 부모 파괴자 순서로 호출된다. 하지만 포인터나 레퍼런스 형태로 생성되면 자식의 파괴자가 호출되지 않을 수 있다.

이런 경우 메모리에 누수가 발생하며 파괴자에 다른 중요한, 클래스가 파괴될 때 반드시 실행되어야 하는 코드가 있을 때 문제가 발생할 수 있다. 이런 경우를 방지하기 위해 부모 - 자식 클래스 관계를 가지고 파괴자를 별도로 정의하는 클래스일 경우 가상 함수 형태로 선언해야 안전하다.

### 순수 가상 함수

순수 가상 함수는 부모 클래스에서 "자식 클래스는 이러이러한 함수를 가지고 있어야 한다" 라고 정의만 하는 함수이다. 그래서 순수 가상 함수를 포함한 클래스로부터 인스턴스를 생성할 수 없고 순수 가상 함수를 포함한 클래스로부터 생성되는 자식 클래스는 그 함수에 대한 구현체가 있어야 한다.

순수 가상 함수는 클래스의 정의에서 함수 선언부에 `=0` 을 붙인다.

자바에서도 비슷한 기능을 하는 것이 있는데 클래스와 별도로 인터페이스라는 것으로로 나눠놓았다.

## ex02

ISquad 클래스가 있다. 이 클래스는 인터페이스 역할만 한다. (C++에서는 직접적으로 "인터페이스" 라는 용어를 사용하지 않는다. 참고로 자바엔 클래스와 인터페이스가 존재한다. 참고로 자바에서 인터페이스는 클래스가 구현해야 하는 동작을 지정하는 데 사용하는 자료형이다.)

예시에 나온대로 파괴자와 순수가상함수로 이루어진 ISquad 클래스 헤더파일을 만든다.

ISquad는 분대를 구성하기 위해 구현해야 하는 것을 미리 정의해 둔 것이다.

getCount() 함수는 분대 대원수를 리턴하며 getUnit() 함수는 인수로 주어진 순서의 대원의 포인터를 반환한다. push() 함수는 인수로 주어진 대원을 현재 대원의 끝에 삽입한다.

이어서 "대원"을 구현하기 위한 인터페이스가 있다. ISpaceMarine인데 파괴자와 순수가상함수 4개로 이루어져 있다. clone() 함수는 이 객체의 복제본의 포인터를 반환하며 battleCry는 특정 메시지를 표준 출력에 출력한다. rangedAttack도 특정 메시지를 표준 출력에 출력하며 meleeAttack도 마찬가지이다.

이 인터페이스로 구상 클래스를 만들 때 생성자와 소멸자에 대해서도 특정 메시지가 표준에 출력되어야 한다.

이 문제에서 Squad 객체를 만들 때 List를 구현해야 한다. 선형으로 Array List를 구현하거나 Linked List를 구현하거나 하는 방법이 있겠지만 index로 접근하는 경우와 자료를 추가하는 경우만 있어 자료 접근 시 자료가 선형으로 저장되어 있는 배열이 효율적이라 판단해 나는 Array List 기반으로 구현하였다.

## ex03

주어진 AMateria 클래스를 적절하게 완성해라.

다음엔 Ice와 Cure 클래스를 AMateria 클래스를 바탕으로 만들어라. type은 클래스 명칭의 소문자 형식으로 짓는다.

### 학습해야 할 것

- 클래스의 프로토타입

### 클래스의 프로토타입

클래스 A와 B가 있다. A의 요소 중 B를 참조할 수 있다. 또 B의 요소 중 A를 참조할 수 있다. 이런 경우 보통 클래스 A를 선언할 때 헤더에 클래스 B를 사용한다고 일종의 선언을 한다. 클래스 명칭만 적어 두고 이 클래스가 사용된다 라는 식으로 선언만 해 두는 것이다.

