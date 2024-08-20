# JDK의 GC

Java Development Kit (JDK)에서 가비지 컬렉션(Garbage Collection, GC)은 자동으로 메모리를 관리하는 메커니즘입니다. 이는 Java 애플리케이션에서 더 이상 사용되지 않는 객체를 찾아 메모리에서 해제함으로써 메모리 누수를 방지하고, 효율적인 메모리 관리를 가능하게 합니다.

### 1. **Heap 메모리 구조**
Java의 메모리는 크게 두 가지 영역으로 나눌 수 있습니다:
- **Young Generation**: 새롭게 생성된 객체가 이 영역에 할당됩니다. Young Generation은 다시 `Eden`, `Survivor 0`, `Survivor 1` 세 가지 영역으로 나뉩니다.
- **Old Generation**: Young Generation에서 오래 살아남은 객체가 이곳으로 이동합니다.

### 2. **GC의 주요 알고리즘**

1. **Mark-and-Sweep**
    - **Mark 단계**: 모든 객체를 순회하며 참조되고 있는 객체를 마킹합니다.
    - **Sweep 단계**: 마킹되지 않은 객체를 메모리에서 해제합니다.

2. **Generational GC**
    - Java에서는 객체의 생존 기간에 따라 `Young Generation`과 `Old Generation`으로 나누어 관리합니다.
    - 대부분의 객체는 짧은 생명주기를 가지므로 `Young Generation`에서 빠르게 수거됩니다. 오래 살아남은 객체는 `Old Generation`으로 이동하여, 여기에서 더 적은 빈도로 수거됩니다.

3. **Stop-the-world**
    - GC 작업 중에는 애플리케이션의 실행이 일시 중지됩니다. 이를 "Stop-the-world"라고 부르며, 짧게 지속되지만 성능에 영향을 줄 수 있습니다.

4. **Minor GC와 Major GC**
    - **Minor GC**: Young Generation에서 수행되는 GC입니다. Young Generation의 `Eden`과 `Survivor` 영역에서 사용되지 않는 객체를 제거합니다.
    - **Major GC (또는 Full GC)**: Old Generation과 Young Generation에서 모두 수행되는 GC입니다. 메모리를 대량으로 해제하기 때문에 상대적으로 더 많은 시간이 소요될 수 있습니다.

### 3. **GC의 종류**

1. **Serial GC**
    - 단일 스레드를 사용하여 GC 작업을 수행합니다. 간단하지만 멀티코어 환경에서 비효율적일 수 있습니다.

2. **Parallel GC**
    - 여러 개의 스레드를 사용하여 GC 작업을 병렬로 수행합니다. 멀티코어 시스템에서 효율적입니다.

3. **CMS (Concurrent Mark-Sweep) GC**
    - 애플리케이션 실행 중에 GC 작업을 수행하여 "Stop-the-world" 시간을 최소화합니다.

4. **G1 (Garbage First) GC**
    - Young Generation과 Old Generation을 구분하지 않고, 메모리를 여러 개의 작은 영역으로 나누어 GC 작업을 수행합니다. CMS GC의 단점을 보완하고, 큰 힙에서도 효율적으로 작동합니다.

### 4. **GC 튜닝**
- GC의 성능을 최적화하기 위해 JVM 옵션을 조정할 수 있습니다. 예를 들어, `-Xms`와 `-Xmx` 옵션으로 힙 메모리 크기를 설정하거나, `-XX:+UseG1GC` 옵션으로 G1 GC를 활성화할 수 있습니다.

GC의 작동 원리를 이해하고, 적절한 GC 알고리즘을 선택하여 애플리케이션의 성능을 최적화하는 것이 중요합니다. GC는 Java의 메모리 관리를 자동화하지만, 잘못된 설정이나 과도한 "Stop-the-world" 시간이 애플리케이션의 성능에 영향을 줄 수 있습니다.


