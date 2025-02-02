# 동시성 문제 해결

이 문제는 동시성(concurrency) 문제로 인해 발생하는 것으로, DB에 데이터를 삽입하는 과정에서 "중복 확인"과 "삽입" 사이의 시간 간격 동안 다른 요청이 같은 데이터를 삽입하려 시도하면서 중복이 발생하는 경우입니다. 이를 방지하기 위한 여러 가지 방법이 있습니다:

---

## 1. **데이터베이스 차원에서 중복 방지**
### (1) **유니크 제약 조건 (Unique Constraint)**
- DB 레벨에서 특정 컬럼이나 컬럼 조합에 유니크 제약 조건을 설정합니다.
- 예를 들어, `CREATE UNIQUE INDEX`를 사용:
  ```sql
  CREATE UNIQUE INDEX idx_unique_data ON your_table(column_name);
  ```
- 중복 데이터 삽입 시 DB에서 에러를 반환하므로, 애플리케이션에서 이를 처리하면 됩니다.
- **장점**: DB 차원에서 가장 간단하고 확실한 방법.
- **단점**: 중복 에러를 처리해야 하는 로직이 필요.

---

## 2. **낙관적 잠금 (Optimistic Locking)**
- 엔티티에 버전 정보를 추가하여 동시성 충돌을 감지하는 방식.
- 예를 들어, `@Version` 어노테이션을 사용하면 Hibernate에서 지원:
  ```java
  @Entity
  public class YourEntity {
      @Id
      private Long id;
      
      @Version
      private Integer version;
      
      private String data;
  }
  ```
- 데이터 삽입 전 조회 시 해당 엔티티의 버전 정보를 확인하고, 충돌이 발생하면 다시 시도하도록 로직을 작성합니다.
- **장점**: 충돌 감지가 가능.
- **단점**: 충돌이 발생할 경우 추가 로직 필요.

---

## 3. **비관적 잠금 (Pessimistic Locking)**
- 데이터베이스에서 해당 데이터에 락을 걸어 다른 트랜잭션이 접근하지 못하게 합니다.
- Spring Data JPA에서 제공하는 `@Lock` 어노테이션을 사용:
  ```java
  @Lock(LockModeType.PESSIMISTIC_WRITE)
  @Query("SELECT e FROM YourEntity e WHERE e.data = :data")
  YourEntity findByDataWithLock(@Param("data") String data);
  ```
- **장점**: 강력한 동시성 제어 가능.
- **단점**: 성능 저하 우려(특히 많은 트래픽이 있을 경우).

---

## 4. **원자적 연산 사용 (Atomic Operation)**
- 데이터 조회와 삽입을 하나의 트랜잭션 내에서 처리.
- 예를 들어, MySQL의 `INSERT IGNORE`나 `ON DUPLICATE KEY UPDATE`를 활용:
  ```sql
  INSERT INTO your_table (column_name) VALUES ('value')
  ON DUPLICATE KEY UPDATE column_name = VALUES(column_name);
  ```
- **장점**: 삽입 로직을 단순화.
- **단점**: 어플리케이션 레벨의 유연한 처리 로직 작성이 어려울 수 있음.

---

## 5. **분산 락 사용**
- 분산 환경에서 Redis나 ZooKeeper 등을 활용해 락을 구현.
- 예를 들어, Redis의 `SETNX` 명령을 사용:
  ```java
  String lockKey = "lock:your_key";
  boolean isLocked = redisTemplate.opsForValue().setIfAbsent(lockKey, "lock", Duration.ofSeconds(10));
  if (isLocked) {
      try {
          // 삽입 로직 실행
      } finally {
          redisTemplate.delete(lockKey);
      }
  } else {
      throw new RuntimeException("다른 작업이 진행 중입니다.");
  }
  ```
- **장점**: 분산 환경에서도 동시성 제어 가능.
- **단점**: 락 해제를 위한 추가 로직 필요.

---

## 6. **큐(Queue) 기반 처리**
- 데이터 삽입 작업을 직접 처리하지 않고 메시지 큐(Kafka, RabbitMQ 등)를 통해 순차적으로 처리.
- **장점**: 동시성 문제가 완전히 해결됨.
- **단점**: 시스템 설계가 복잡해질 수 있음.

---

## 7. **논리적 중복 처리 (Soft Duplicate Check)**
- 데이터베이스에 "상태" 컬럼을 추가하여 삽입 중 상태를 저장(예: `PROCESSING`, `DONE`).
- 데이터 확인 시 `DONE` 상태만 중복 확인 대상으로 처리.
- **장점**: 애플리케이션에서 유연하게 처리 가능.
- **단점**: 추가 컬럼 및 로직 설계 필요.

---

### 최적의 방법 선택하기
- **단순한 API**: 유니크 제약 조건.
- **복잡한 트랜잭션 처리**: 비관적 잠금 또는 낙관적 잠금.
- **분산 시스템**: 분산 락 또는 메시지 큐.
- **고성능 필요**: 원자적 연산.

상황에 맞는 방법을 적용하면 중복 삽입 문제를 효과적으로 해결할 수 있습니다. 필요한 경우 자세한 구현 방법을 추가적으로 도와드릴게요!

