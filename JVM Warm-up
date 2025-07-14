# JVM Warm-up feat. 스프링 첫 요청이 오래 걸리는 이유

Spring 애플리케이션으르 실행하고 요청을 보내면 유독 첫 요청이 오래 걸린다. 이러한 현상이 발생하는 이유를 이해하기 위해 자바 프로그램이 실행되는 방식을 살펴볼 필요가 있다.

C, C++과 같은 컴파일 언어는 소스 코드를 기계어로 직접 변환하여 최적화된 성능을 제공하지만 빌드 환경에 종속적이라는 단점이 있다. 플랫폼이 바뀔 때 마다 컴파일을 다시 해야 한다.

자바는 이런 플랫폼 종속적인 문제를 해결하기 위해 JVM을 도입했다. 컴파일 시 바이트 코드로 변환되어 실행 시 JVM이 이를 기계어로 변환해 실행한다. 이러한 구조 덕분에 Java는 플랫폼에 종속되지 않지만, 변환 과정이 추가되므로 성능에 영향을 미칠 수 있다.

### 클래스 로더

JVM에서 **클래스 로더(Class Loader)**는 바이트 코드를 메모리에 로드하는 역할을 한다. 이 때 클래스 로더는 일반적으로 Lazy Loading 방식을 채택한다.
Lazy Loading이란 애플리케이션 실행 시 모든 클래스를 즉시 로드하지 않고, 특정 클래스가 필요해질 때 이를 로드하는 방식이다.

이 Lazy Loading이 첫번째 이유이다. **배포 직후에는 대부분의 클래스들이 사용된 적이 없기 때문에 메모리에 적재되지 않은 상태**이다. 따라서 첫 요청이 발생하면 그제서야 클래스 로더가 해당 클래스들을 메모리에 로드하는 작업을 수행하게 되고, 이 과정에서 지연이 발생하게 된다.

### JIT 컴파일러

바이트 코드를 기계어로 변환하는 작업은 JVM 성능에 중요한 영향을 미친다. JVM은 이를 해결하고자 **JIT(Just-In-Time) 컴파일러**를 도입했다.

기존 인터프리터 방식을 통해 코드를 한 줄씩 해석해서 속도가 느리다는 단점이 있었다.

반면에 JIT 컴파일러는 실행 중 프로파일링을 수행하여 코드의 실행 빈도, 메서드 호출 횟수, 루프 반복 횟수 등을 분석한다. 이를 통해 자주 사용되는(핫스팟) 코드를 식별하고 이를 기계어로 변환한 후 **코드 캐시(Code Cache)**에 저장한다. 이후 동일한 코드 실행 시 캐시를 활용하여 성능을 극대화할 수 있다.

## JVM Warm-up

JIT 컴파일러를 사용하더라도 애플리케이션이 처음 실행되었을 때는 캐시에 아무것도 존재하지 않는다. 이 때 요청이 한 번에 서버에 들어온다면 어떨까? 애플리케이션 초기 상태에서는 클래스 로딩이 완료되지 않았고, JIT 컴파일러에 의해 기계어로 변환된 코드도 없다. 이러한 상황에서 대량의 요청이 애플리케이션 서버로 들어오면 처리 속도가 급격히 느려진다.
이를 **Cold-Start**라고 한다.

Cold-Start를 어떻게 해결하면 될까? **자주 호출될 것으로 예상되는** 로직을 강제로 호출하여 캐싱하는 작업을 하면 된다. 이를 **Warm-up**이라고 한다.

### Warm-up 수행 방법

트래픽이 많은 서비스라면 warm-up 작업은 반드시 고려되어야 한다. 그렇다면 warm-up은 어떻게 할 수 있을까? 애플리케이션이 실행된 후 핵심 로직들을 강제로 호출하면 된다.

#### 주요 로직을 미리 호출

Spring Boot에서는 `ApplicationRunner` 인터페이스를 활용해 애플리케이션 초기화 시 특정 로직을 강제로 호출할 수 있다. 자주 사용될 메서드를 미리 실행해 warm-up을 유도할 수 있다.

```java
@Component
public class WarmupTest implements ApplicationRunner {

    @Override
    public void run(final ApplicationArguments args) throws Exception {
        try {
            // 메서드 호출
        } catch (Exception e) {
            // ..
        }
    }
}
```

#### Java Microbenchmark Harness(JMH) 활용

보다 정교한 warm-up이 필요한 경우, **JMH**를 활용할 수 있다. JMH는 JVM warm-up을 자동화하는 도구 중 하나로, 워밍업 상태를 모니터링하면서 특정 코드를 반복적으로 실행하여 최적화 주기를 유도할 수 있다.

```groovy
plugins {
	id 'java'
    id "me.champeau.jmh" version "0.7.1"
}

dependencies {
    jmh 'org.openjdk.jmh:jmh-core:0.9'
    jmh 'org.openjdk.jmh:jmh-generator-annprocess:0.9'
}

jmh{
    fork = 1
    warmupIterations = 1
    iterations = 1
}
```

의존성을 추가하고 JMH 메소드를 입력할 수 있다. 자세한 사항은 [Github](https://github.com/melix/jmh-gradle-plugin)을 참조하면 된다.

```java
@Benchmark
public void warmupBenchmark() {
    // 반복 실행할 코드
}

```

## Warm-up을 수행할 때 고려해 볼 트레이드 오프

Warm-up 작업은 애플리케이션 성능 최적화에 유용하지만, 몇 가지 트레이드 오프를 동반한다.

1. warm-up 작업 중 오류 발생 시 실제 기능은 정상적으로 작동하더라도 배포 중단의 위험이 있다.
2. 외부 API 호출을 포함한 warm-up 작업은 외부 시스템에 대량의 요청을 발생시키기 때문에 DDos 공격과 비슷한 현상이 발생할 수 있다.
3. 새로운 버전 배포와 같은 상황에 애플리케이션 구동 시간이 늘어날 수 있다.

---
참고
- [[Spring] 스프링 첫 요청이 처리되는데 오래 걸리는 이유(서블릿 초기화와 JIT 컴파일러)와 해결 방법(웜업, Warm-Up)](https://mangkyu.tistory.com/232)
- [스프링 애플리케이션 배포 직후 발생하는 Latency의 원인과 이를 해결하기 위한 JVM Warm-up](https://hudi.blog/jvm-warm-up/)
- [[Java] JMH(Java Microbenchmark Harness) 로 성능 벤치마킹](https://velog.io/@adduci/Java-JMHJava-Microbenchmark-Harness-%EB%A1%9C-%EC%84%B1%EB%8A%A5-%EB%B2%A4%EC%B9%98%EB%A7%88%ED%82%B9)
- [How to Warm Up the JVM](https://www.baeldung.com/java-jvm-warmup)