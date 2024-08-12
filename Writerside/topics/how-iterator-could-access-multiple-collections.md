# Iterator 인터페이스가 다양한 컬렉션에 접근할 수 있는 이유

Java에서 `Iterator` 인터페이스는 컬렉션의 구체적인 구현에 관계없이 일관된 방법으로 요소를 순회할 수 있게 해줍니다. 이는 컬렉션이 `Iterator` 인터페이스를 구현하도록 설계되어 있기 때문에 가능합니다. 그 원리를 이해하려면 컬렉션과 `Iterator`의 상호 작용 방식을 살펴봐야 합니다.

### 1. 컬렉션의 `iterator()` 메서드

각 컬렉션 클래스는 `java.util.Collection` 인터페이스를 구현하며, 이 인터페이스는 `iterator()` 메서드를 제공합니다. 이 메서드는 해당 컬렉션에 대한 `Iterator` 객체를 반환합니다. 예를 들어, `ArrayList` 클래스는 다음과 같이 `iterator()` 메서드를 구현합니다:

```java
public Iterator<E> iterator() {
    return new Itr();
}

private class Itr implements Iterator<E> {
    // 내부 클래스 Itr는 ArrayList의 요소를 순회하는 Iterator의 구현입니다.
    int cursor; // 다음 요소를 가리키는 인덱스

    public boolean hasNext() {
        return cursor != size();
    }

    public E next() {
        if (cursor >= size())
            throw new NoSuchElementException();
        return get(cursor++);
    }

    public void remove() {
        throw new UnsupportedOperationException();
    }
}
```

### 2. `Iterator`의 내부 구조

위 예제에서 `Itr` 클래스는 `Iterator` 인터페이스를 구현합니다. 이 클래스는 `ArrayList`의 요소에 접근하는 방법을 알고 있습니다. 예를 들어, `ArrayList`의 경우 요소는 배열에 저장되므로 인덱스를 사용하여 접근할 수 있습니다.

`hasNext()` 메서드는 현재 인덱스가 리스트의 크기보다 작은지 확인하여 더 순회할 요소가 있는지 여부를 판단합니다. `next()` 메서드는 현재 인덱스에 해당하는 요소를 반환하고, 인덱스를 증가시켜 다음 요소를 가리키도록 합니다.

### 3. 다형성과 캡슐화

각 컬렉션 클래스는 자신만의 `Iterator` 구현을 가지고 있습니다. `ArrayList`는 `Itr` 클래스를 사용하고, `LinkedList`는 `ListItr` 클래스를 사용하는 식입니다. 이 구현들은 컬렉션의 내부 구조에 맞게 요소를 순회하는 방법을 정의합니다. 따라서 사용자는 `Iterator` 인터페이스를 통해 컬렉션의 내부 구조를 신경 쓰지 않고도 일관되게 요소를 순회할 수 있습니다.

### 예제: `LinkedList`의 `Iterator` 구현

`LinkedList`는 노드 기반의 데이터 구조이므로, `Iterator` 구현은 노드를 따라가면서 요소에 접근합니다:

```java
public Iterator<E> iterator() {
    return new ListItr(0);
}

private class ListItr implements Iterator<E> {
    private Node<E> lastReturned;
    private Node<E> next;
    private int nextIndex;

    ListItr(int index) {
        // next를 index에 해당하는 노드로 초기화
    }

    public boolean hasNext() {
        return nextIndex < size();
    }

    public E next() {
        if (!hasNext())
            throw new NoSuchElementException();
        lastReturned = next;
        next = next.next;
        nextIndex++;
        return lastReturned.item;
    }

    public void remove() {
        throw new UnsupportedOperationException();
    }
}
```

### 결론

`Iterator` 인터페이스는 컬렉션의 구체적인 구현을 추상화하여, 사용자가 일관된 방식으로 컬렉션의 요소를 순회할 수 있게 합니다. 각 컬렉션 클래스는 자신에게 맞는 `Iterator` 구현을 제공하며, `iterator()` 메서드를 통해 이 `Iterator`를 반환합니다. 이를 통해 사용자는 컬렉션의 내부 구조를 몰라도 요소를 순회할 수 있게 됩니다.