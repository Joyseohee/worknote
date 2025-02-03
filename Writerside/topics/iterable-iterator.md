# iterable-iterator

Java에서 **Iterable**과 **Iterator**는 컬렉션 프레임워크에서 데이터를 순차적으로 접근하기 위해 제공되는 인터페이스입니다. 아래에서 **Iterable**과 **Iterator**의 개념과 Java에서의 구현을 설명하겠습니다.

---

## **1. Iterable과 Iterator의 개념**

### 1-1. Iterable
- **정의**: Iterable은 **반복(iteration)할 수 있는 객체**를 의미합니다.
- **역할**: Iterable 객체는 **Iterator를 반환**하여 컬렉션의 요소를 순회할 수 있도록 합니다.
- **사용 예시**: 모든 컬렉션(List, Set 등)은 Iterable을 구현하므로 `for-each` 루프에서 사용할 수 있습니다.

### **2) Iterator**
- **정의**: Iterator는 **반복자**로, 컬렉션의 요소에 순차적으로 접근할 수 있도록 하는 객체입니다.
- **역할**: Iterator는 컬렉션의 각 요소를 **한 번에 하나씩 순회**하면서 접근하거나 삭제할 수 있는 메서드를 제공합니다.
- **주요 메서드**:
    - `hasNext()`: 다음 요소가 있는지 확인합니다.
    - `next()`: 다음 요소를 반환합니다.
    - `remove()`: 현재 요소를 제거합니다.

---

## **2. Iterable과 Iterator의 관계**

- **Iterable 인터페이스**는 **Iterator를 반환**하는 메서드 `iterator()`를 정의합니다.
- **Iterator 인터페이스**는 실제로 컬렉션 요소를 순회하며, Iterable이 제공한 데이터를 다룰 수 있게 합니다.

> **Iterable과 Iterator의 관계**:  
> Iterable은 Iterator를 제공하며, Iterator는 Iterable이 보유한 데이터를 순회합니다.

---

## **3. Java의 Iterable 인터페이스**

### ** 3-1. Iterable 인터페이스 정의**
Java에서 `java.lang.Iterable` 인터페이스는 다음과 같이 정의되어 있습니다:

```java
public interface Iterable<T> {
    Iterator<T> iterator();
}
```

- **주요 메서드**:
    - `iterator()`: Iterable 객체의 요소를 순회하기 위한 Iterator를 반환합니다.
- **기본 구현 클래스**:
    - 모든 Java 컬렉션 클래스(List, Set, Queue 등)는 `Iterable` 인터페이스를 구현합니다.

---

### **Iterable 사용 예제**

```java
import java.util.ArrayList;
import java.util.Iterator;

public class IterableExample {
    public static void main(String[] args) {
        // ArrayList는 Iterable을 구현
        ArrayList<String> list = new ArrayList<>();
        list.add("A");
        list.add("B");
        list.add("C");

        // for-each 사용 (Iterable 인터페이스 덕분)
        for (String item : list) {
            System.out.println(item);
        }

        // 직접 Iterator를 사용
        Iterator<String> iterator = list.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
    }
}
```

---

## **4. Java의 Iterator 인터페이스**

### ** 4-1.Iterator 인터페이스 정의**
`java.util.Iterator` 인터페이스는 다음과 같이 정의되어 있습니다:

```java
public interface Iterator<E> {
    boolean hasNext();
    E next();
    default void remove() {
        throw new UnsupportedOperationException("remove");
    }
}
```

### ** 4-2.Iterator의 주요 메서드**
1. **`hasNext()`**:
    - 컬렉션에서 순회할 다음 요소가 있는지 확인합니다.
    - 반환값: `true`(다음 요소 있음) / `false`(없음).

2. **`next()`**:
    - 다음 요소를 반환합니다.
    - 호출 전에 `hasNext()`를 사용해 다음 요소가 있는지 확인하는 것이 권장됩니다.

3. **`remove()`** (Optional):
    - 현재 요소를 컬렉션에서 제거합니다.
    - 일부 구현체에서는 `UnsupportedOperationException`을 던질 수 있습니다.

---

### **Iterator 사용 예제**

```java
import java.util.ArrayList;
import java.util.Iterator;

public class IteratorExample {
    public static void main(String[] args) {
        // 리스트 생성
        ArrayList<String> list = new ArrayList<>();
        list.add("Java");
        list.add("Python");
        list.add("C++");

        // Iterator 사용
        Iterator<String> iterator = list.iterator();

        while (iterator.hasNext()) {
            String item = iterator.next();
            System.out.println(item);
            if (item.equals("Python")) {
                iterator.remove(); // 현재 요소 제거
            }
        }

        // Python이 제거된 결과 확인
        System.out.println(list);
    }
}
```

---

## **5. Iterable과 Iterator의 차이**

| **특징**            | **Iterable**                                          | **Iterator**                                      |
|---------------------|-------------------------------------------------------|--------------------------------------------------|
| **역할**            | `Iterator`를 반환하여 순회할 수 있도록 정의합니다.       | 컬렉션 요소를 순회하면서 데이터를 접근합니다.       |
| **주요 메서드**     | `iterator()`                                           | `hasNext()`, `next()`, `remove()`               |
| **예제**            | `for-each` 루프에서 사용 가능합니다.                   | 직접 요소를 순회하며, 조건부 작업에 적합합니다.    |
| **사용 위치**       | 컬렉션 전체의 반복을 가능하게 합니다.                   | 요소 하나씩 접근하거나 수정할 때 사용합니다.       |

---

## **6. 요약**
- **Iterable**: 순회 가능한 객체로, `iterator()` 메서드를 통해 **Iterator를 반환**합니다.
- **Iterator**: 데이터를 한 요소씩 순회하며 접근하고 삭제할 수 있는 메서드를 제공합니다.
- **실용성**:
    - `for-each` 루프: Iterable 덕분에 간단히 사용.
    - `Iterator`: 커스텀 순회 로직이나 조건부 삭제 작업에 유용.