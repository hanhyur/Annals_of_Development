# Scheduler와 Batch

## Scheduler

스케줄링 어노테이션은 애플리케이션이 간단하고 선언적인 방식으로 주기적 작업을 설정할 수 있도록 해주며, 필요에 따라 TaskScheduler를 통해 스케줄러의 동작을 세밀하게 커스터마이징이 가능하다. 이를 통해 데이터 정리, 모니터링, 배치 작업 트리거 등 다양한 반복 작업을 안정적으로 구현할 수 있다.

- 주기적 데이터 정리
    예를 들어, 임시 데이터나 로그 파일을 주기적으로 정리하는 작업을 스케줄링하여 시스템 성능 저하를 방지할 수 있다.
- 모니터링 및 상태 점검
    서버의 상태나 서비스의 동작 여부를 주기적으로 체크하여, 문제가 발생하면 경고를 발송하거나 로그를 남기는 작업에 활용할 수 있다.
- 정기적인 배치 작업 트리거
    Spring Batch와 함께 사용될 경우, @Scheduled 어노테이션을 이용해 배치 작업을 정해진 시간마다 실행할 수 있다. 이 경우, 배치 작업은 스케줄러에 의해 주기적으로 트리거되어 실행되며, 배치 처리의 시작 시점을 제어할 수 있다.

### 동작 원리

- Spring Context가 올라올 때 Scheduler가 등록됩니다.
- 지정한 주기(cron, fixedRate, fixedDelay 등)에 따라 메서드가 실행됩니다.
- 단순 실행이며, 병렬 처리, 상태 저장, 실패 재시작 등은 수동 구현해야 합니다.

```java
@Component
public class SimpleScheduler {

    @Scheduled(fixedRate = 5000) // 5초마다 실행
    public void runTask() {
        System.out.println("스케줄러 실행됨: " + LocalDateTime.now());
    }
}
```

## Batch

Spring Batch는 대량의 레코드를 처리하는 데 필수적인 재사용 가능한 기능들을 제공한다. 예를 들어 로깅 및 트레이싱, 트랜잭션 관리, 작업 처리 통계, 작업 재시작, 스킵, 그리고 리소스 관리 기능 등이 포함되며 이외에도, 최적화 및 파티셔닝 기법을 통해 매우 높은 처리량과 성능을 요구하는 배치 작업을 지원하는 고급 기술 서비스 및 기능들을 제공한다.

**Spring Batch는 스케줄링 프레임워크가 아니다.** Spring Batch는 스케줄러를 대체하기보다는 스케줄러와 협력하여 사용되도록 설계되었다.

### Spring Batch의 핵심 구성 요소

- Job
    배치 처리의 최상위 단위로, 하나 이상의 Step으로 구성. Job은 실행 순서, 실패 시 재시작 정책 등 전체 흐름을 정의.
- Step
    Job을 구성하는 단위 작업. 일반적으로 한 개의 Step은 하나의 단위 작업(예: 파일 읽기, 데이터 처리, 기록 등)을 수행하며, 내부에 Chunk 기반 처리 패턴을 포함 가능.
- JobRepository
    배치 작업의 실행 정보를 영속화하는 저장소. Job 및 Step의 실행 상태, 메타데이터를 저장하여 재시작이나 모니터링 기능 지원.
- JobLauncher
    Job 실행을 트리거하는 컴포넌트로, Job을 시작하고 실행 상태를 관리.
 
### 배치 처리 전략

배치 작업 설계를 시작할 때, 비즈니스 로직은 아래 standard building blocks을 사용하여 구현할 수 있는 일련의 단계로 분해되어야 한다.

**standard building blocks**

- 변환(Conversion) Applications
    외부 시스템에서 제공되거나 외부 시스템에 생성되는 각 유형의 파일에 대해, 해당 거래 기록을 처리에 필요한 표준 형식으로 변환하는 변환 애플리케이션을 만들어야 한다. 이 유형의 배치 애플리케이션은 번역 유틸리티 모듈(기본 배치 서비스 참조)로 일부 또는 전부 구성될 수 있다.
- 검증(Validation) Applications
    모든 입력 및 출력 기록이 정확하고 일관성이 있는지 확인한다. 검증은 일반적으로 파일 헤더와 트레일러, 체크섬 및 검증 알고리즘, 그리고 레코드 수준의 교차 검증을 기반으로 한다.
- 추출(Extract) Applications
    데이터베이스나 입력 파일에서 일련의 레코드를 읽어 미리 정의된 규칙에 따라 레코드를 선택한 후, 해당 레코드를 출력 파일에 기록한다.
- 추출/갱신(Extract/Update) Applications
    데이터베이스나 입력 파일에서 레코드를 읽어, 각 입력 레코드에 기반하여 데이터베이스나 출력 파일을 변경한다.
- 처리 및 갱신(Processing and Updating) Applications
    추출, 검증 애플리케이션에서 가져온 입력 거래에 대해 처리를 수행한다. 이 처리는 보통 처리를 위해 필요한 데이터를 얻기 위해 데이터베이스를 읽고, 데이터베이스를 갱신하며, 출력 처리를 위한 레코드를 생성하는 과정을 포함한다.
- 출력/포맷(Output/Format) Applications
    입력 파일을 읽어 해당 기록의 데이터를 표준 형식에 맞게 재구조화하고, 인쇄 또는 다른 프로그램이나 시스템으로 전송하기 위한 출력 파일을 생성한다.

## Spring Scheduler를 Batch로 변경했을 때 장단점

### 장점 (Pros)

1. 대용량 데이터 처리의 안정성 및 성능
    * Chunk 기반 처리 : 데이터를 일정 크기(Chunk)로 나누어 읽고(Read), 가공하고(Process), 쓰기(Write) 때문에 OutOfMemoryError(OOM) 발생 가능성이 낮아진다.
    * 메모리 효율성 : 전체 데이터를 메모리에 올리지 않아도 되므로 시스템 리소스를 효율적으로 사용할 수 있다.

2. 작업의 신뢰성 및 재시작 기능
   * 상태 관리 : 모든 Job의 실행 이력(시작/종료 시간, 성공/실패 여부, 파라미터 등)이 데이터베이스(JobRepository)에 기록된다.
   * 재시작(Restart) : 만약 수만 건의 데이터를 처리하다 중간에 실패하면, Scheduler는 처음부터 다시 시작해야 한다. 반면 Batch는 실패한 지점부터 자동으로 이어서 작업을 재개할 수 있어 매우 효율적이다.

3. 정교한 오류 제어
    * Skip/Retry : 특정 데이터 처리 시 예외가 발생해도 작업을 중단시키지 않고 해당 데이터만 건너뛰거나(Skip), 지정된 횟수만큼 재시도(Retry)하는 등 유연한 오류 제어가 가능하다.

4. 고급 기능 및 확장성
    * 병렬 처리: 단일 작업을 여러 스레드로 나누어 동시에 처리하거나(Parallel Steps), 대용량 데이터 처리를 여러 서버에 분산(Remote Chunking)하여 작업 시간을 획기적으로 단축할 수 있다.
    * 다양한 Reader/Writer: CSV, XML, JSON, JPA, JDBC 등 다양한 데이터 소스를 쉽게 읽고 쓸 수 있는 기본 구현체들을 풍부하게 제공한다.

5. 모니터링 및 관리의 용이성
    * JobRepository에 저장된 메타데이터를 통해 어떤 작업이 언제, 어떤 파라미터로 실행되었고 성공했는지 실패했는지 쉽게 추적하고 관리할 수 있다.

### 단점 (Cons)

1. 높은 복잡도와 학습 곡선
    * `@Scheduled` 어노테이션 하나면 끝나는 Scheduler와 달리, Batch는 JobLauncher, JobRepository, Job, Step, ItemReader, ItemProcessor, ItemWriter 등 이해하고 설정해야 할 컴포넌트가 많다.
    * 초기 설정과 개발에 더 많은 시간과 노력이 필요하다.

2. 설정의 번거로움
    * Batch 작업을 위해서는 JobRepository가 사용할 데이터베이스(DataSource) 설정이 필수다.

3. 실행 오버헤드
    * Job을 실행할 때마다 JobRepository에 상태를 기록하고 트랜잭션을 관리하는 등 내부적으로 처리하는 로직이 많아, 아주 가벼운 작업을 처리하기에는 Scheduler보다 오버헤드가 클 수 있다.

## 마이그레이션이 목적일 때 더 좋은 방법은 없을까? Batch 말고 다른 방법은?

### 마이그레이션 시 고려해야 할 사항

1.  **데이터 규모** : 얼마나 많은 데이터를 옮겨야 하는가?
2.  **다운타임(Downtime)** : 서비스 중단이 가능한가? 가능하다면 얼마나 오래 가능한가?
3.  **데이터 변환** : 원본(Source)과 대상(Target)의 스키마가 동일한가? 아니면 복잡한 데이터 변환(Transformation)이 필요한가?
4.  **일회성 vs 지속성** : 한 번만 실행하고 끝나는 마이그레이션인가, 아니면 일정 기간 동안 계속해서 데이터를 동기화해야 하는가?
5.  **팀의 기술 스택** : 팀이 가장 자신 있게 다룰 수 있는 기술은 무엇인가?

### Batch가 아닌 다른 방법

#### 1. DB 네이티브 도구 및 SQL 스크립트

스키마 변경이 거의 없고, 서비스 중단(다운타임)이 가능하며, 가장 빠르고 간단하게 끝내고 싶을 때 사용하기 좋은 방법이다.

- **방법**:
    - `mysqldump`, `pg_dump`, `Oracle Data Pump` 같은 데이터베이스 내장 유틸리티로 데이터를 덤프하고 복원한다.
    - SQL `INSERT INTO ... SELECT FROM ...` 구문을 사용하여 DB 링크나 직접 연결을 통해 데이터를 이전한다.
- **장점**:
    - **압도적으로 빠름**: 데이터베이스 엔진에 최적화되어 있어 성능이 매우 뛰어나다.
    - **단순함**: 별도의 애플리케이션 개발 없이 몇 가지 명령어나 스크립트로 완료할 수 있다.
    - **데이터 정합성**: 보통 단일 트랜잭션으로 처리되거나 DB 레벨에서 일관성을 보장한다.
- **단점**:
    - **서비스 중단 필수**: 데이터 정합성을 위해 보통 원본 테이블에 Lock을 걸거나 서비스를 중단해야 한다.
    - **복잡한 변환 로직 처리 불가**: 데이터를 옮기는 데 집중하며, 복잡한 비즈니스 로직을 적용하기 어렵다.
    - **대용량 데이터 한계**: 수억 건 이상의 데이터를 한 번에 옮기면 DB에 큰 부하를 줄 수 있다.

#### 2. 커스텀 애플리케이션/스크립트

데이터 변환 로직이 매우 복잡하거나, 특정 API를 호출하는 등 DB 외부 로직이 필요할 때 좋은 방법이다.

-   **방법**:
    -   Python(Pandas, SQLAlchemy), Java(JPA/JDBC), Node.js 등을 이용해 직접 마이그레이션 로직을 구현한다.
    -   `SELECT`로 원본에서 데이터를 조금씩 읽어와서, 변환 로직을 적용한 후, 대상 DB에 `INSERT` 또는 `UPDATE` 한다.
-   **장점**:
    -   **최고의 유연성**: 어떤 복잡한 변환 로직이나 비즈니스 규칙도 코드로 구현할 수 있다.
    -   **팀 기술 활용**: 팀이 가장 익숙한 언어와 프레임워크를 사용할 수 있다.
-   **단점**:
    -   **"바퀴의 재발명"**: Spring Batch가 제공하는 **재시작, 로깅, 통계, 병렬 처리, 오류 제어(Skip/Retry) 기능을 모두 직접 구현**해야 한다.
    -   **안정성 문제**: 대용량 데이터 처리 시 메모리 관리(OOM), 커넥션 관리, 트랜잭션 관리를 잘못하면 실패하기 쉽다.
    -   **개발 비용**: 안정적인 마이그레이션 스크립트를 만드는 데 생각보다 많은 공수가 들어간다.

#### 3. ETL 전문 도구

마이그레이션이 일회성이 아니라 지속적인 데이터 파이프라인의 일부일 때. 또는 비개발자도 데이터 흐름을 관리해야 할 때 좋다.

-   **방법**:
    -   **Apache Nifi**: GUI 기반으로 데이터 흐름을 시각적으로 디자인하고 실행한다. 데이터 라우팅 및 간단한 변환에 강하다.
    -   **Apache Airflow**: Python 코드로 데이터 워크플로우(DAG)를 정의하고 스케줄링 및 모니터링한다. 마이그레이션 스크립트를 오케스트레이션하는 데 탁월하다.
    -   **Talend, Informatica**: 상용 ETL 솔루션으로, 강력한 기능과 GUI를 제공한다.
-   **장점**:
    -   **강력한 기능**: 스케줄링, 모니터링, 다양한 데이터 소스 커넥터 등 데이터 처리에 필요한 기능이 내장되어 있다.
    -   **시각화 및 관리**: 데이터 흐름을 시각적으로 파악하고 관리하기 용이하다.
-   **단점**:
    -   **높은 학습 곡선**: 해당 도구의 사용법을 별도로 익혀야 한다.
    -   **설치 및 운영 비용**: 별도의 서버와 인프라가 필요하며, 상용 툴은 라이선스 비용이 발생한다.

#### 4. CDC (Change Data Capture) 기반 실시간 동기화

**서비스 중단이 절대 불가능**하며, 실시간에 가깝게 데이터를 새로운 시스템으로 이전해야 할 때 좋다.

-   **방법**:
    1.  초기 데이터 적재(Initial Load): `1번`이나 `2번` 방법으로 스냅샷 데이터를 일단 옮긴다.
    2.  변경분 캡처: **Debezium**, **Maxwell** 같은 CDC 도구를 사용해 원본 DB의 트랜잭션 로그(Binlog 등)를 읽는다.
    3.  변경분 전송 및 적용: 캡처된 변경 데이터(INSERT, UPDATE, DELETE)를 **Kafka** 같은 메시지 큐로 보낸 후, 구독(Subscribe)하는 애플리케이션이 대상 DB에 실시간으로 반영한다.
-   **장점**:
    -   **제로 다운타임(Zero Downtime)**: 서비스 중단 없이 마이그레이션을 진행할 수 있다.
    -   **데이터 정합성**: 트랜잭션 로그 기반이라 데이터 유실이 거의 없다.
    -   **점진적 전환**: 구 시스템과 신 시스템을 동시에 운영하며 안정적으로 전환할 수 있다.
-   **단점**:
    -   **최고의 복잡도**: Debezium, Kafka 등 여러 시스템을 구축하고 운영해야 하므로 기술적 난이도가 가장 높다.
    -   **인프라 비용**: 추가적인 시스템 구성으로 인한 비용이 발생한다.


### 시나리오별 추천 방법

| 상황 | 추천 방법 | 이유 |
| :--- | :--- | :--- |
| **1. 간단한 DB 이전**<br>(서버 점검 시간 활용, 스키마 동일) | **DB 네이티브 도구** | 가장 빠르고 간단하며 안정적 |
| **2. 대용량 데이터 이전**<br>(복잡한 변환 로직, 실패 시 재처리 필요) | **Spring Batch** | 재시작, 청크 처리, 병렬화 등 대용량 처리에 필요한 모든 기능을 제공하는 검증된 솔루션 |
| **3. 복잡한 비즈니스 로직**<br>(외부 API 호출 등, 데이터량은 적음) | **커스텀 스크립트 (Python/Java)** | 유연성이 가장 중요할 때. 단, 안정성은 직접 확보해야 한다. |
| **4. 무중단 마이그레이션**<br>(서비스 중단 절대 불가) | **CDC (Debezium + Kafka)** | 복잡하지만 유일한 대안. 실시간으로 데이터를 동기화하여 안전하게 전환 |
| **5. 클라우드 환경**<br>(AWS, GCP, Azure 사용) | **클라우드 네이티브 서비스**<br>(AWS DMS, Glue, Azure Data Factory) | 클라우드 환경에 최적화되어 있고, 관리 포인트를 줄여주며 확장성이 뛰어나다. |
