# 인터페이스와 추상클래스, enum

## 메소드 내용이 없는 interface

인터페이스와 abstract 클래스도 `.class` 파일을 만들 수 있다. 인터페이스와 abstract 클래스는 다음과 같은 이유에서 사용한다.

- 설계 시 선언해 두면 개발할 때 기능을 구현하는 데만 집중할 수 있다.
- 메소드의 이름과 매개 변수 선언의 격차를 줄일 수 있다.
- 공통적인 인터페이스와 abstract 클래스를 선언해 놓으면, 선언과 구현을 구분할 수 있다.

## 인터페이스 직접 만들기

실제 코드를 만들지 않더라도 어떤 메소드들이 있어야 하는지 정의할 때 인터페이스를 사용하면 된다.

```java
public interface SampleInterface {
    public boolean sampleMethod(String sampleParameter);
}
```

인터페이스 선언부는 `public interface`로 시작한다. 또한 내부에 선언된 메소드들은 몸통이 있으면 안된다.

> 컴파일하는 방법은 일반 클래스와 동일하다.

인터페이스를 활용할 때는 클래스 선언문에 implements라는 단어 뒤에 인터페이스를 추가한다.
자바에서 인터페이스는 상속이 아니라 **"구현"**이기 때문에 여러 개를 implements 할 수 있다.

```java
public class SampleClassImpl implements SampleInterface1, SampleInterface2 {
    //
}
```

인터페이스를 구현할 경우 반드시 인터페이스에 정의된 메소드들의 몸통을 만들어주어야 한다.

```java
public class SampleClassImpl implements SampleInterface {
    @Override
    public boolean sampleMethod(String sampleParameter) {
        return false;
    }
}
```

## 일부 완성되어 있는 abstract 클래스

abstract는 "추상적인"이라는 의미를 가진다. 그래서 abstract 클래스를 추상 클래스 라고도 한다.
abstract 클래스는 자바에서 마음대로 초기화하고 실행할 수 **없도록** 되어 있다. 그래서 abstract 클래스를 구현해 놓은 클래스로 초기화 및 실행이 가능하다.
abstract 클래스는 선언 시 class라는 예약어 앞에 abstract라는 예약어를 사용한다. 그리고 몸통이 없는 메소드 선언문에 abstract를 명시한다.

```java
public abstract class SampleAbstract {
    public abstract boolean sampleMethod(String sampleParameter);
}
```

abstract 클래스는 abstract로 선언한 메소드가 **하나라도 있을 때** 선언한다. 인터페이스와 달리 구현되어 있는 메소드가 있어도 상관 없다.
abstract 클래스는 클래스이기 때문에 extends를 사용한다. 그리고 extends한 클래스에 abstract 클래스의 메소드를 구현하면 된다.

## final

`final`이라는 예약어는 클래스, 메소드, 변수에 선언할 수 있다.

### 클래스에 선언 시

클래스에 `final`을 선언할 때 접근 제어자와 class 예약어 사이에 추가할 수 있다. 클래스가 final로 선언되어 있으면 상속을 해 줄 수 없다.
더 이상 확장해서는 안되는 클래스, 상속받아서 내용을 변경하면 안되는 클래스를 선언할 때 final로 선언하면 된다.

### 메소드에 선언 시

메소드를 final로 선언하면 더 이상 Overriding 할 수 없다.

### 변수에 선언 시

변수에 final을 선언하는 것은 클래스나 메소드와는 다르다. 변수에 final을 사용하면 "더 이상 바꿀 수 없다."는 의미이다.

final을 붙인 인스턴스 변수나 클래스 변수는 생성과 동시에 초기화를 해야만 컴파일 시 에러가 발생하지 않는다. 생성자나 메소드에서 초기화할 경우 중복되어 변수 값이 선언될 수도 있기 떄문에 final의 기본 의도를 벗어난다.

반면에 매개 변수나 지역 변수에 final을 붙였을 때는 초기화를 하지 않아도 별 문제가 되지 않는다. 단, 넘어온 변수에 값을 다시 할당하는 것은 안된다.

> final로 선언된 클래스의 객체 안에 있는 객체들은 final로 선언된 것이 아니기 때문에 final의 제약에 따르지 않는다.

## enum

어떤 클래스가 상수(constant)만으로 만들어져 있을 경우 반드시 class로 선언할 필요는 없다. **enum** 클래스로 선언하면 "이 객체는 상수의 집합이다"라고 명시적으로 나타낼 수 있다.

```java
public enum SampleEnum {
    SAMPLE_ONE,
    SAMPLE_TWO,
    SAMPLE_THREE;
}
```

enum 클래스에 있는 상수들은 별도로 타입을 지정할 필요도, 값을 지정할 필요도 없다. **상수들의 이름만 콤마(,)로 구분**하여 나열해 주면 된다.

## enum 제대로 사용하기

enum 상수 값을 지정하는 것은 가능하지만, 값을 동적으로 할당하는 것은 불가능하다.

enum 클래스의 생성자는 접근 제어자로 package-private과 private만 사용할 수 있다. 즉, 상수를 enum 클래스 내에서 선언할 때에만 생성자를 사용할 수 있는 것이다.

## enum 클래스의 부모는 java.lang.Enum

enum 클래스는 무조건 `java.lang.Enum` 클래스의 상속을 받는다. Enum 클래스의 부모 클래스는 Object 클래스이기 때문에, Object 클래스의 메소드를 모두 사용할 수 있다. 하지만 그 중 다음 4개의 메소드를 **Overriding 하지 못하게 막아놨다.**

- `clone()`: 객체를 복제하는 메소드
- `finalize()`: GC가 발생할 때 처리하는 메소드
- `hashCode()`: int 타입의 해시 코드 값을 리턴하는 메소드
- `equals()`: 두 개의 객체가 동일한지 확인하는 메소드

Enum 클래스에서 많이 사용되는 메소드는 `compareTo()` 메소드다. Enum이 선언된 순서대로 각 상수들의 순서가 정해진다. `compareTo()` 메소드는 순서가 같은지, 다른지 비교하는 데 사용된다.
순서의 차이는 메소드의 매개 변수로 넘기는 상수 기준으로 앞에 있으면 음수, 뒤에 있으면 양수를 리턴한다.

enum 클래스에는 API 문서에 없는 메소드가 있는데 바로 `values()`라는 메소드다. 이 메소드를 호출하면 enum 클래스에 선언되어 있는 모든 상수를 배열로 리턴한다.