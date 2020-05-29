# Java의 중첩 클래스 (Nested Classes)

## 1. 중첩 클래스

자바는 클래스 내부에 클래스를 선언할 수 있다. 클래스 내부에 클래스를 선언하는 이유는  특정한 공간에서만 사용되는 클래스를 그룹핑 하기 위해서다. 만약 특정 클래스가 다른 한 클래스에서만 유용하게 사용되면 그 클래스 안에 특정 클래스를 박아넣는 것이 논리적이나 시각적이나 유지보수 면에서 것이 더 낫다.

또 클래스 안에 클래스를 박아넣으면 그 클래스를 모듈화 하는데에 더 유리하다. 중첩 클래스를 상위 클래스만 접근 가능하게 할 수도 있다.

중첩 클래스를 포함하고 있는 클래스를 컴파일 할 때는 해당 클래스 파일 외에 중첩 클래스에 대한 클래스 파일이 '$' 기호를 구분자로 별도로 생성이 된다. 예를 들면 바로 아래 코드를 컴파일 하였을 때에 대한 결과물은 네 개의 클래스 파일(Main.class, Main$staticNestedClass.class, Main$localInnerClass.class, Main$InnerClass.class)이 생성된다.

## 2. 정적 중첩 클래스와 이너 클래스

클래스 내에 클래스를 선언할 때 해당 클래스를 static으로 선언하면 Static Nested Class (정적 중첩 클래스)이고 non-static으로 선언하면 Inner Class (이너 클래스) 라고 한다.

```java
public class Main
{
	public static void main(String[] args) {
	}
	class localInnerClass {
	    void test() {
	        System.out.println("InnerClass- Local Inner Class");
	    }
	}
    public void memberInnerClassMethod() {
        class innerClass {
            void test() {
                System.out.println("InnerClass- Member Inner Class");
         }
    }
	static class staticNestedClass {
	    static void test() {
	        System.out.println("Static Nested Class");
	    }
	}
}

```

Static 키워드가 붙지 않는 중첩 클래스는 이너클래스 (Inner Class) 라고 한다.

이너 클래스에는 세 가지 하위 분류가 있다.

- Member Inner Class (멤버 이너클래스)
- Local Inner Class (지역 이너클래스)
- Anonymous Class (익명 이너클래스)

세 가지 중 익명 이너클래스는 인터페이스 관련 추가 설명이 필요해 해당 포스트에서는 제외하였다.

Static 키워드가 붙는 중첩 클래스는 **Static Nested Class** 라고 한다.

## 3. 지역 이너 클래스

지역 이너 클래스에 대해 접근하려면 new 생성자를 통해 인스턴스를 생성 후 접근하여야 한다.

```java
public class Main
{
	public static void main(String[] args) {
	    outerClass obj = new outerClass();
	    outerClass.localInnerClass subclass = obj.new localInnerClass();
	    
	    // 1. 외부 클래스(Main)에서 직접 지역 이너 클래스 호출
	    subclass.test();
	    // 2. 내부 클래스(outerclass)에서 다른 메소드를 통해 지엯 이너 클래스 간접 호출
	    obj.calllocalInnerClassMethod();
	}
}
class outerClass {
    int outerDynamicField = 20;
	class localInnerClass {
	    void test() {
	        System.out.println("called by Local Inner Class");
	        // 아우터 클래스의 필드에 접근가능
	        System.out.println(outerDynamicField);
	    }
	}
    public void calllocalInnerClassMethod() {
        localInnerClass obj = new localInnerClass();
        System.out.println("Local Inner Class 호출");
        obj.test();
    }
}
```

다음 예제를 보면 지역 이너클래스를 포함하고 있는 다른 메소드나 외부 클래스에서나 new 생성자를 이용, 인스턴스를 생성 후 접근함을 알 수 있다.

지역 이너 클래스는 아우터 클래스의 필드나 메소드에 별 제약 없이 접근 가능하지만 아우터 클래스가 지역 이너 클래스에 접근하기 위해서는 new 생성자를 이용해 인스턴스 생성 후 접근하여야 한다.

## 4. 멤버 이너 클래스

멤버 이너 클래스는 메소드 내부에 클래스가 정의된 것을 의미한다.

```java
public class Main
{
	public static void main(String[] args) {
	    outerClass obj = new outerClass();
	    obj.localClassTestMethod();
	}
}
class outerClass {
    static int outerStaticField = 10;
    int outerDynamicField = 20;
    public void localClassTestMethod() {
        class localClass {
            void methodOfLocalClass() {
    	        System.out.println("called by Local Inner Class");
    	        // 아우터 클래스의 필드에 접근가능
    	        System.out.println(outerDynamicField);
            }
        }
        localClass i = new localClass(); // 로컬클래스
        i.methodOfLocalClass();
    }
}
```

메소드 내부에 정의된 로컬 클래스는 로컬 클래스가 정의된 메소드 내부에서밖에 사용하지 못한다.

또 아우터 클래스에 필드만 접근 가능하며 JDK 1.8 이전에는 아우터 클래스에 필드에도 접근하지 못하였다.

## 5. 정적 중첩 클래스

정적 중첩 클래스는 기존 정적 클래스와 유사하게 사용 가능하다.

```java
public class Main
{
	public static void main(String[] args) {
	    outerClass.staticNestedClass.methodOfstaticNestedClass();
	}
}
class outerClass {
    static int outerStaticField = 10;
    static class staticNestedClass {
        static void methodOfstaticNestedClass() {
	        System.out.println("called by Static Nested Class");
	        // 아우터 클래스의 정적 필드에 접근가능
	        System.out.println(outerStaticField);
        }
    }
}
```

다음과 같이 클래스명을 통해 정적 중첩 클래스에 접근하여 메소드나 필드에 접근 가능하다.