# 참조 자료형에 대해서 더 자세히 알아봅시다

## 참조 자료형은 나머지 다에요

기본 자료형 byte, short, int, long, float, double, char, boolean 8개를 제외하고는 모두 참조 자료형이다.

기본 자료형과 참조 자료형의 가장 큰 차이는 new를 사용해서 객체를 생성하는지 여부의 차이이다. new 없이도 객체를 생성할 수 있는 참조 자료형은 오직 String과 배열 뿐이다.

```
String str = "Java";
int[] arr = {1,2,3};
```

각종 연산자들도 대부분 기본 자료형을 위해서 존재하는 것이다. 그 중에서 `+`만 참조 자료형 중에서 String 클래스만 사용 가능하고, 나머지 클래스에서는 사용할 수 없다.
그 이외에 다른 참조 자료형이 사용할 수 있는 연산자는 값을 할당하기 위한 `=` 뿐이다.

## 기본 생성자

참조 자료형은 new를 사용하여 객체를 생성하는데 new 뒤에 나오는 것이 바로 **생성자 constructor**이다.

자바는 생성자를 만들지 않아도 자동으로 만들어지는 기본 생성자가 있다. 아무런 매개 변수가 없는 기본 생성자는 다른 생성자가 없을 경우 기본으로 컴파일할 때 자동으로 만들어지기 때문에, 굳이 선언해줄 필요가 없다.

하지만 다른 생성자가 있으면, 다시 말해서 매개 변수가 있는 다른 생성자가 있으면, 컴파일할 때 자동으로 만들어지지 않기 때문에 그 때는 기본 생성자를 선언해주어야 한다.

## 생성자는 몇 개까지 만들 수 있을까?

자바는 클래스의 객체를 보다 간편하게 만들기 위해서 여러 가지 매개 변수를 갖는 여러 생성자를 가질 수 있다. 생성자의 개수는 몇 개든 상관없다.

DTO는 데이터를 다른 서버로 전달하기 위한 것이 주 목적이고, VO는 데이터를 담아 두기 위한 목적으로 사용된다. 어떻게 보면 DTO가 VO를 포함한다고 볼 수 있기 때문에 대부분 DTO라는 명칭을 선호한다.

- DTO(Data Transfer Object) : 데이터를 전달하기 위한 객체. 어떤 속성을 갖는 클래스를 만들고, 그 속성들을 쉽게 다른 곳으로 전달하기 위해서 만든다.
- VO(Value Object) : 값(데이터) 자체를 표현하기 위한 객체.

DTO를 만들어 놓으면 복합적인 데이터를 리턴 가능하다는 장점이 있다.

```java
public MemberDTO getMemberDTO() {
	MemberDTO dto = new MemberDTO();
	// 중간 생략
	return dto;
}
```

이처럼 메소드의 리턴 타입에 DTO로 선언하고, 그 객체를 리턴해주면 된다.

```java
public class MemberDTO {
    public String name;
    public String phone;
    public String email;

    public MemberDTO() {
        //아무 정보도 모를 때
    }

    public MemberDTO(String name) {
        //이름만 알 때
        this.name = name;
    }

    public MemberDTO(String name, String phone) {
        //이름과 전화번호만 알 때
        this.name = name;
        this.phone = phone;
    }

    public MemberDTO(String name, String phone, String email) {
        //모든 정보를 알고 있을 때
        this.name = name;
        this.phone = phone;
        this.email = email;
    }
}
```

이렇게 자바의 생성자는 매개 변수 개수의 제한도 없고, 몇 개를 만들어도 상관 없다. 하지만 너무 많으면 관리가 힘들어지므로, 꼭 필요에 맞는 생성자만 만드는 습관을 들여야만 한다.

## 이 객체의 변수와 매개 변수를 구분하기 위한 this

`this`는 말 그대로 "이 객체(혹은 해당 객체)"라는 의미로, 객체 자신을 가리키는 참조변수이다.
`this`라는 예약어를 변수에 사용할 때에는, 객체의 변수와 매개 변수의 이름이 동일할 때, 인스턴스의 변수를 구분하기 위해서 사용한다.

```java
public class MemberDTO {
    public String name;
    public String phone;
    public String email;

    public MemberDTO(String name) {
        this.name = name;
    }
}
```

이렇게 `this.name` 이라고 지정해주면, 컴파일러와 개발자가 쉽게 구분할 수 있다.

## 메소드 overloading

클래스의 생성자는 매개 변수들을 서로 다르게 하여 선언할 수 있다.

```java
public class ReferenceOverloading {
    public static void main(String args[]) {
        ReferenceOverloading reference = new ReferenceOverloading();
    }

    public void print(int data) {
    }

    public void print(String data) {
    }

    public void print(int intData, String stringData) {
    }

    public void print(String stringData, int intData) {
    }
}
```

매개변수의 개수가 같아도 타입의 순서가 다르면 다른 메소드로 인식된다. 즉, 매개 변수의 타입이 핵심이다.

> 오버로딩(overloading) : 메소드의 이름을 같도록 하고, 매개 변수를 다르게 하는 것

메소드 오버로딩은 "같은 역할을 하는 메소드는 같은 메소드 이름을 가져야 한다"는 목적을 위해 사용한다.
생성자도 매개 변수에 따라서 다르게 인식되므로 이것도 오버로딩의 일종이다.

## 메소드에서 값 넘겨주기

자바에서 메소드가 종료되는 조건은 다음과 같다.

- 메소드의 모든 문장이 실행되었을 때
- return 문장에 도달했을 때
- 예외가 발생했을 때

메소드에서 여러 개의 데이터를 반환하고 싶으면 DTO를 리턴 타입으로 선언하고, 해당 DTO를 리턴하면 된다.

## static 메소드와 일반 메소드의 차이

static 메소드는 객체를 생성하지 않아도 메소드를 호출할 수 있는 메소드이다.
하지만 static 메소드는 클래스 변수만 사용할 수 있다는 단점이 있다.

클래스 변수는 모든 객체에서 하나의 값을 바라보기 때문에, 예상치 못한 상황이 발생할 수 있다.

## static 블록
객체는 여러 개를 생성하지만, 단 한 번만 호출되어야 하는 코드가 있다면 static 블록을 사용하면 된다.
static 블록은 객체가 생성되기 전에 한 번만 호출된다. 여러 개를 선언할 수 있으며 선언된 순서대로 블록들이 차례대로 호출되기 때문에 선언되어 있는 순서는 매우 중요하다.

## Pass by value, Pass by reference

기본 자료형은 Pass by Value로 데이터를 전달하고, 참조 자료형은 Pass by Reference로 데이터를 전달한다.

Pass by Value는 값을 전달하는 작업이고, 호출되기 전과 후에 데이터가 변경되지 않는다.

Pass by Reference는 값이 아니라 객체에 대한 참조가 전달되는 작업이다. 그래서 참조를 전달하여 호출된 메소드 내부에서 값을 변경하면 기존 데이터에도 영향을 미친다.

자바에서는 참조 자료형이 Pass by reference로 동작하는 것처럼 보이지만 진정한 Pass by reference는 아니다. 자바에서 파라미터는 Pass by value로만 동작한다. 원시값이 복사되느냐 주소값이 복사되느냐의 차이가 있을 뿐이다.

C 언어와 다르게 자바에서는 포인터를 철저하게 숨겨 개발자가 직접 메모리 주소에 접근하지 못하게 조치했기 때문이다. 다시 말하면, 자바는 C 언어와 다르게 주소값을 복사를 통해 이용이 가능할뿐 직접 액세스할 수 없기 때문에 Pass by value(Call by value)로만 동작한다.

> 용어 정리
> 포인터 : 메모리 주소를 저장하는 변수
> 포인터를 숨겼다는 의미 : 개발자가 포인터를 직접적으로 다루거나, 포인터 연산을 수행할 수 없도록 언어 설계가 되어 있다는 것을 의미

## 매개 변수를 지정하는 특이한 방법

자바에서는 임의 개수의 매개 변수를 넘겨줄 수 있는 방법을 제공한다.

```java
public class MethodVarargs {
    public static void main(String args[]) {
        MethodVarargs varargs = new MethodVarargs();
        varargs.calculateNumbersWithArray(new int[]{1,2,3,4,5});
        varargs.calculateNumbers(1,2,3,4,5);
    }

    public void calculateNumbersWithArray(int []numbers) {
    }
    
    public void calculateNumbers(int...numbers) {
        int total=0;
        for(int number:numbers) {
            total+=number;
        }
        System.out.println("Total="+total);
    }
}
```

이와 같이 "타입...변수명"으로 선언해주면, numbers는 배열로 인식된다.

```java
public void arbitrary(String message, int...numbers) {
}

// 이렇게 사용하면 안된다
public void arbitrary(int...numbers, String message) {
}
```

하나의 메소드에서는 한 번만 사용 가능하고, 여러 매개 변수가 있다면 가장 마지막에 선언해야만 한다.