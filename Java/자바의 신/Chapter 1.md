# 1장. 프로그래밍이란 무엇인가?

## 자바 프로그램의 메소드

```java
public boolean checkPassword(String password) {
    // 중간 내용
}
```

- 어떤 값을 주고 결과를 넘겨주는 것을 자바에서는 **메소드 method**라고 한다. `checkPassword()`가 메소드의 이름이다.
- `password`와 같이 소괄호 안에 주어진 값을 **매개 변수 parameter**라고 한다. 이 값은 있어도 되고, 없어도 된다.
- `boolean`과 같이 메서드 이름 앞에 오는 것을 **리턴 타입 return type**이라고 한다.

## 자바의 가장 작은 단위 클래스

자바 프로그램의 메소드는 무소속으로 존재할 수 없고, 반드시 클래스 안에 있어야 한다.

```java
public class ClassName {
    public void methodName(String parameter) {

    }
}
```

클래스 안에는 여러 개의 메소드가 존재할 수 있다.

### 클래스는 상태(state)와 행동(behavior)이 있어야만 한다.

객체지향 언어에서 현실에 있는 사물이나 추상적인 것을 클래스로 표현합니다. 이러한 클래스는 다음의 조건을 만족해야 합니다.

> 클래스는 상태와 행동이 있어야만 한다.

```java
public class ClassName {
    String stateField;

    public void methodName(String parameter) {

    }
}
```

`stateField`와 같은 것을 **변수 variable**라고 한다. 이러한 변수가 클래스의 특성을 결정짓는 "상태"에 해당한다.
그리고 `methodName()`과 같은 메소드가 클래스의 "행동"에 해당한다.

객체지향에서 '클래스는 상태와 행동이 있어야만 한다'고 말하지만, 반드시 있어야 하는 것은 아니다.

## 프로그래밍의 기본

프로그래밍에서는 수학과 달리 오른쪽에서 일어난 일을 왼쪽에 적용한다고 생각하면 된다.

```java
int a;
a = 1 + 2;
```

위 계산식에 따르면 a에는 3이 들어가게 된다.

## 세미콜론과 인덴트

모든 자바 코드의 한 줄이 끝날 때는 반드시 `;`을 적어야 한다.

그리고, 자바에서는 인덴트 indent(들여쓰기)를 신경쓰지 않는다. 하지만 가독성을 위해서 사용하는 것이 좋다.

## 예약어

자바에는 **예약어 reserved word**라는 것이 있다. `public`, `class`, `int`, `new`, `return`과 같은 단어들이다.