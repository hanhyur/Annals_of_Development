# 자바는 상속이라는 것이 있어요

## 상속이란?

하나의 클래스를 잘 만들어 놓으면 그 클래스를 상속 받아서 확장할 수 있다. 그래서 상속을 받을 때 `extends`라는 예약어를 사용한다.

그렇다면 이 상속을 왜 사용할까? 예를 들어 모든 클래스에 사용되는 메소드가 있다고 가정할 때, 이 메소드를 수정해야 될 경우 모든 클래스를 방문해야 한다.
하지만 상속을 사용할 경우 부모 클래스의 메소드만 수정하면 된다.

### 상속 시 부모의 모든 변수, 메소드 사용이 가능한가?

부모 클래스에 선언되어 있는 **public 및 protected로 선언되어 있는 모든 인스턴스 및 클래스 변수와 메소드**를 사용할 수 있다.
다른 패키지에 있는 부모 클래스의 **접근 제어자가 없거나, private으로 선언된 것들은 사용할 수 없다.**

## 상속과 생성자

> 부모 클래스에서는 기본 생성자를 만들어 놓는 것 이외에는 상속을 위해서 아무런 작업을 할 필요는 없다.

```java
public class Parent {  
//    public Parent() {  
//        System.out.println("Parent Constructor");  
//    }  
  
    public void printName() {  
        System.out.println("Parent printName()");  
    }  
}

public class InheritancePrint {  
    public static void main(String[] args) {  
        Child child = new Child();  
        child.printName();  
    }  
}

/* 결과 :
Child Constructor
Parent printName()
*/
```

Parent 클래스에 기본 생성자가 없더라도 **매개 변수가 있는 생성자가 없어 컴파일할 때 기본 생성자가 자동으로 생성되기 때문**에 아무런 문제가 없다.
하지만 매개 변수를 받는 생성자가 존재한다면 기본 생성자가 자동으로 생성되지 않기 때문에 에러가 발생하게 된다.

```java
public class ParentArg {
    public ParentArg(String name) {
        System.out.println("ParentArg("+name+") Constructor");
    }

    public void printName() {
        System.out.println("printName() - ParentArg");
    }
}

public class ChildArg extends ParentArg {
	// compile error
    public ChildArg() {
        System.out.println("Child Constructor");
    }
}
```

이걸 해결하는 방법은 두 가지가 있다.

1. 부모 클래스에 기본 생성자를 만든다.
2. 자식 클래스에서 부모 클래스의 생성자를 명시적으로 지정하는 `super()`를 사용한다.

메소드처럼 `super()`를 사용하면 부모 클래스의 생성자를 호출한다는 것을 의미한다.

`super()`를 사용하여 생성자를 호출할 때는 모호하게 `null`을 넘기는 것보다는 호출하고자 하는 생성자의 기본 타입을 넘겨주는 것이 좋다.

자바는 **부모의 매개 변수가 없는 기본 생성자를 찾는 것이 기본**이므로 부모 클래스에 매개 변수가 있는 생성자만 있을 경우에는 `super()`를 이용해서 부모 생성자를 꼭 호출해줘야 한다.

> 자식 클래스의 생성자에서 `super()`를 명시적으로 지정하지 않으면, 컴파일 시 자동으로 추가된다. 그리고, 부모 클래스의 생성자를 호출하는 `super()`는 반드시 자식 클래스의 생성자에서 가장 첫 줄에 선언되어야 한다.

## 메소드 Overriding

자식 클래스에 부모 클래스에 있는 메소드와 동일하게 선언하는 것을 **메소드 Overriding**이라고 한다.

> "동일하게 선언되어 있다"를 "동일한 시그니처를 가진다"라고 표현할 수 있다. 이 때 시그니처는 메소드 이름, 매개 변수의 타입 및 개수를 의미한다.

Overriding된 메소드를 호출할 경우 자식 클래스의 메소드만 실행된다.

만약 Overriding한 메소드의 리턴 타입을 변경할 경우 어떻게 될까? 컴파일 에러가 발생하게 된다. 부모 클래스에 동일한 이름으로 메소드가 있는 이상 자식은 리턴 타입을 마음대로 바꿀 수 없다.

접근 제어자의 경우 확대는 가능하지만 축소하는 것은 안된다.

> public > protected > package-private > private

## 참조 자료형의 형 변환

상속 관계가 성립되면 다음와 같이 생성할 수도 있다.

```java
Parent obj = new Child();
```

하지만 다음과 같이 생성하는건 안 된다.

```java
Child obj = new Parent();
```

자식 클래스에서는 부모 클래스에 있는 메소드와 변수들을 사용할 수 있지만, 그 반대는 불가능하다. 만약 자식 클래스에서 추가된 메소드나 변수가 없으면 가능할 수도 있다.

기본 자료형의 형 변환에서 `int → long` 의 형 변환 작업은 안 해도 되지만 `long → int`는 동일한 값이 된다는 보장이 없기 때문에 명시적으로 형 변환을 해야 한다. longValue가 int의 범위를 넘어서면 값이 바뀌기때문이다.

같은 맥락에서 **자식 → 부모**의 형 변환은 자식 클래스에서 부모 클래스의 메소드와 변수 모두 사용할 수 있기 때문에 명시적 형 변환이 필요 없지만 반대의 경우는 불가능한 것이다.

일반적으로 여러 개의 값을 처리하거나, 매개 변수로 값을 전달할 때는 보통 부모 클래스의 타입으로 보낸다. 이렇게 하지 않으면 배열과 같이 여러 값을 한번에 보낼 때 타입별로 구분해서 메소드를 만들어야 하기 때문이다.

### instanceof

```java
객체 instanceof 클래스(타입)
```

객체의 타입을 구분하는 예약어이다.

instanceof를 사용할 때 가장 하위에 있는 자식 타입부터 확인 해야한다.

## Polymorphism

다형성이란 "형태가 다양하다"라는 의미로, 하나의 객체나 메소드가 여러 가지 형태를 가질 수 있는 것을 의미한다.

```java
public class Parent {
    public Parent() {
        System.out.println("Parent Constructor()");
    }

    public void printName() {
        System.out.println("Parent printName()");
    }
}
```

```java
public class Child extends Parent {
    public Child() {
        System.out.println("Child Constructor()");
    }
}
```

Parent를 상속받은 또 다른 자식 ChildOther이 생겼다.

```java
public class ChildOther extends Parent {
    public ChildOther() {
    }

    @Override
    public void printName() {
        System.out.println("ChildOther - printName()");
    }
}
```

아래 메서드를 실행하면?

```java
private void callPrintNames() {
    Parent parent1 = new Parent();
    Parent parent2 = new Child();
    Parent parent3 = new ChildOther();

    parent1.printName();
    parent2.printName();
    parent3.printName();
}
```

실행 결과

```java
Parent Constructor()
Parent Constructor()
Child Constructor()
Parent Constructor()
ChildOther Constructor()
Parent printName()
Parent printName()
ChildOther printName()
```

각 객체의 타입은 모두 Parent타입으로 선언되었는데도 불구하고 `printName()` 메서드의 결과는 상이하다. 선언시에는 모두 Parent타입으로 선언했지만, 실제로 호출된 메서드는 생성자를 사용한 클래스에 있는 것이 호출되었다. 왜냐하면 각 객체의 실제 타입은 다르기 때문이다. 이와 같이 형 변환을 하더라도, 실제 호출되는 것은 원래 객체에 있는 메서드가 호출된다는 것이 바로 다형성이다.