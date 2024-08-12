# collection-and-collections

A referenceJava에서 `Collection`과 `Collections`는 서로 다른 개념을 나타내며, 주요 차이점은 다음과 같습니다:

### 1. `Collection` 인터페이스

`Collection`은 Java 컬렉션 프레임워크의 루트 인터페이스로, 여러 객체를 하나의 단위로 그룹화하는 표준을 정의합니다. `Collection` 인터페이스는 다음과 같은 메서드를 포함합니다:

- **추가 및 삭제**:
    - `boolean add(E e)`
    - `boolean remove(Object o)`
- **조회 및 검사**:
    - `boolean contains(Object o)`
    - `int size()`
    - `boolean isEmpty()`
- **변환 및 순회**:
    - `Iterator<E> iterator()`
    - `Object[] toArray()`

`Collection` 인터페이스를 직접 구현하는 클래스는 많지 않지만, 이 인터페이스를 확장하는 다양한 하위 인터페이스들이 존재합니다:

- **`List`**: 순서가 있는 컬렉션으로, 중복을 허용합니다. (`ArrayList`, `LinkedList`, `Vector` 등)
- **`Set`**: 순서가 없고, 중복을 허용하지 않는 컬렉션입니다. (`HashSet`, `TreeSet`, `LinkedHashSet` 등)
- **`Queue`**: FIFO (First-In-First-Out) 원칙을 따르는 컬렉션입니다. (`LinkedList`, `PriorityQueue` 등)
- **`Deque`**: 양방향으로 요소를 추가 및 삭제할 수 있는 컬렉션입니다. (`ArrayDeque`, `LinkedList` 등)

### 2. `Collections` 유틸리티 클래스

`Collections`는 Java에서 컬렉션을 조작하기 위한 정적 메서드들을 포함한 유틸리티 클래스입니다. 이 클래스는 컬렉션의 정렬, 검색, 복사, 채우기, 역순 정렬 등을 위한 다양한 메서드를 제공합니다. 주요 메서드들은 다음과 같습니다:

- **정렬 및 검색**:
    - `Collections.sort(List<T> list)`
    - `Collections.binarySearch(List<? extends Comparable<? super T>> list, T key)`
- **수정**:
    - `Collections.reverse(List<?> list)`
    - `Collections.shuffle(List<?> list)`
- **동기화**:
    - `Collections.synchronizedList(List<T> list)`
    - `Collections.synchronizedSet(Set<T> s)`
- **불변 컬렉션**:
    - `Collections.unmodifiableList(List<? extends T> list)`
    - `Collections.unmodifiableSet(Set<? extends T> s)`

### 차이점 요약

- **`Collection`**:
    - 인터페이스
    - 객체를 그룹화하는 방법을 정의
    - 다양한 컬렉션 클래스에서 구현

- **`Collections`**:
    - 유틸리티 클래스
    - 컬렉션을 조작하기 위한 정적 메서드 제공
    - 컬렉션의 정렬, 검색, 동기화, 불변성 등을 처리

### 예제

`Collection`을 사용한 간단한 예제와 `Collections`를 사용하는 예제를 살펴보겠습니다.

#### `Collection` 인터페이스 사용 예제

```java
import java.util.ArrayList;
import java.util.Collection;

public class CollectionExample {
    public static void main(String[] args) {
        Collection<String> collection = new ArrayList<>();
        collection.add("Apple");
        collection.add("Banana");
        collection.add("Cherry");

        for (String fruit : collection) {
            System.out.println(fruit);
        }
    }
}
```

#### `Collections` 유틸리티 클래스 사용 예제

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class CollectionsExample {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("Banana");
        list.add("Apple");
        list.add("Cherry");

        // 정렬
        Collections.sort(list);
        System.out.println("Sorted list: " + list);

        // 역순 정렬
        Collections.reverse(list);
        System.out.println("Reversed list: " + list);
    }
}
```

위의 예제에서 `Collection` 인터페이스를 구현한 `ArrayList`를 사용하여 요소를 추가하고 순회하는 방법과, `Collections` 클래스의 정적 메서드를 사용하여 리스트를 정렬하고 역순 정렬하는 방법을 볼 수 있습니다.

# 제네릭 타입

`Collection<? extends E> c`는 Java의 제네릭 타입 시스템을 사용하여 특정 타입의 객체들을 포함하는 컬렉션을 매개변수로 받는 것을 의미합니다. 이 구문을 이해하기 위해 몇 가지 개념을 살펴보겠습니다.

### 1. 제네릭 타입

제네릭 타입은 클래스나 메서드가 사용할 데이터 타입을 지정할 수 있게 해줍니다. 여기서 `E`는 제네릭 타입 매개변수로, 구체적인 타입으로 대체될 수 있는 변수입니다.

### 2. 와일드카드 (`?`)

`?`는 와일드카드 타입으로, 어떤 타입이든 올 수 있음을 나타냅니다.

### 3. 상한 바운드 (`extends`)

`? extends E`는 상한 바운드 와일드카드를 사용하여 `E` 타입 또는 `E`의 서브타입을 허용합니다. 이는 특정 타입의 상위 클래스나 인터페이스를 확장하는 모든 클래스 타입을 나타냅니다.

### 결론: `Collection<? extends E> c`

이 구문은 `E` 타입 또는 `E`의 서브타입을 요소로 가지는 컬렉션을 매개변수로 받는 것을 의미합니다. 예를 들어, `E`가 `Number`일 경우, `Collection<? extends Number>`는 `Collection<Integer>`, `Collection<Double>`, `Collection<Float>` 등을 허용합니다.

이 구문을 통해 메서드나 생성자는 `E` 타입의 객체들뿐만 아니라, `E`의 서브타입을 포함하는 컬렉션도 처리할 수 있습니다.

### 예제 코드

위의 생성자를 포함하는 `LinkedList` 클래스의 예제를 살펴보겠습니다:

```java
import java.util.Collection;
import java.util.LinkedList;

public class MyLinkedList<E> extends LinkedList<E> {
    public MyLinkedList(Collection<? extends E> c) {
        this();
        addAll(c);
    }

    public static void main(String[] args) {
        Collection<Integer> integers = List.of(1, 2, 3, 4, 5);
        MyLinkedList<Number> numbers = new MyLinkedList<>(integers);
        
        for (Number number : numbers) {
            System.out.println(number);
        }
    }
}
```

이 예제에서 `MyLinkedList<Number>` 객체를 생성할 때 `Collection<Integer>`를 매개변수로 전달할 수 있습니다. 이는 `Integer`가 `Number`의 서브타입이기 때문에 가능하며, 제네릭 타입 시스템이 이를 허용합니다.

### 요약

- **`Collection<? extends E> c`**는 `E` 타입 또는 `E`의 서브타입을 요소로 가지는 컬렉션을 의미합니다.
- 이를 통해 제네릭 클래스나 메서드가 더 유연하게 다양한 타입을 처리할 수 있습니다.
- 상한 바운드 와일드카드를 사용하면 컬렉션이 특정 타입의 상위 클래스나 인터페이스를 확장하는 모든 타입을 포함할 수 있습니다.