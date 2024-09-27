# DI와 생성자
스프링 프레임워크에서는 의존성 주입(Dependency Injection, DI)을 통해 객체 간의 의존 관계를 관리합니다. 의존성을 주입하는 방법에는 여러 가지가 있지만, 주로 **`@Autowired`** 어노테이션을 사용하는 **필드 주입(Field Injection)**과 **생성자 주입(Constructor Injection)** 방식이 널리 사용됩니다. 또한, 직접 객체를 생성하는 방식과의 차이점도 중요합니다. 아래에서 각각의 차이점과 장단점에 대해 상세히 설명드리겠습니다.

## 1. `@Autowired`를 사용한 의존성 주입 vs 직접 객체 생성

### **1.1 `@Autowired`를 사용한 의존성 주입**

스프링의 **`@Autowired`** 어노테이션을 사용하면 스프링 컨테이너가 필요한 의존성을 자동으로 주입해줍니다. 예를 들어:

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class ExampleService {

    @Autowired
    private CategoryMapper categoryMapper;

    // 서비스 로직
}
```

이 경우, 스프링은 `CategoryMapper` 빈을 찾아 `ExampleService`에 주입합니다.

### **1.2 직접 객체 생성**

반대로, 직접 객체를 생성하면 스프링의 의존성 관리 기능을 활용할 수 없습니다. 예를 들어:

```java
public class ExampleService {

    private CategoryMapper categoryMapper = new CategoryMapper();

    // 서비스 로직
}
```

이 경우, `CategoryMapper`는 스프링 컨테이너가 관리하는 빈이 아니라, `ExampleService`가 직접 생성한 인스턴스가 됩니다.

### **주요 차이점**

| **특징**                         | **`@Autowired`를 사용한 DI**                                   | **직접 객체 생성**                           |
|----------------------------------|---------------------------------------------------------------|----------------------------------------------|
| **스프링의 관리**                | 스프링 컨테이너가 빈을 관리하고 주입                             | 스프링과 무관하게 객체가 생성됨               |
| **의존성 관리**                  | 의존성이 외부에서 주입되므로 변경 및 관리가 용이                 | 클래스 내부에서 직접 생성하므로 변경이 어려움 |
| **테스트 용이성**                | 목(Mock) 객체로 쉽게 대체 가능                                   | 테스트 시 직접 생성된 객체를 대체하기 어려움   |
| **결합도**                       | 낮음 (느슨한 결합)                                             | 높음 (강한 결합)                             |
| **라이프사이클 관리**             | 스프링이 빈의 라이프사이클을 관리                               | 개발자가 직접 관리해야 함                     |

### **결론**

스프링 프레임워크를 사용하는 프로젝트에서는 **`@Autowired`** 등을 통한 의존성 주입을 사용하는 것이 일반적이며, 이는 코드의 유연성, 유지보수성, 테스트 용이성을 높여줍니다. 반면, 직접 객체를 생성하는 방식은 스프링의 장점을 활용하지 못하고 코드의 결합도가 높아지는 단점이 있습니다.

## 2. 필드 주입(`@Autowired` on Fields) vs 생성자 주입(Constructor Injection)

스프링에서는 의존성 주입을 구현하는 방법으로 **필드 주입**과 **생성자 주입**이 주로 사용됩니다. 두 방법의 차이점과 장단점을 알아보겠습니다.

### **2.1 필드 주입(Field Injection)**

**필드 주입**은 클래스의 필드에 **`@Autowired`** 어노테이션을 직접 붙여 의존성을 주입하는 방식입니다.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class ExampleService {

    @Autowired
    private CategoryMapper categoryMapper;

    // 서비스 로직
}
```

### **2.2 생성자 주입(Constructor Injection)**

**생성자 주입**은 클래스의 생성자를 통해 의존성을 주입하는 방식입니다. 스프링 4.3 이후로는 생성자가 하나일 경우 **`@Autowired`** 어노테이션을 생략할 수 있습니다.

```java
import org.springframework.stereotype.Service;

@Service
public class ExampleService {

    private final CategoryMapper categoryMapper;

    public ExampleService(CategoryMapper categoryMapper) {
        this.categoryMapper = categoryMapper;
    }

    // 서비스 로직
}
```

### **주요 차이점 및 장단점**

| **특징**                        | **필드 주입**                                     | **생성자 주입**                                     |
|---------------------------------|-------------------------------------------------|----------------------------------------------------|
| **코드의 간결성**               | 간단하고 코드가 짧음                              | 생성자 코드가 추가됨                               |
| **불변성(Immutable)**           | 필드가 `final`일 수 없음                          | 필드를 `final`로 선언할 수 있어 불변성을 유지할 수 있음 |
| **테스트 용이성**               | 리플렉션 등을 사용해야 할 수 있어 테스트가 어려움   | 생성자를 통해 쉽게 목 객체를 주입할 수 있어 테스트가 용이함 |
| **순환 의존성(Circular Dependency)** | 순환 의존성 발생 시 디버깅이 어려움                  | 컴파일 시점에서 순환 의존성을 감지할 수 있어 더 안전함 |
| **의존성 명확성**               | 클래스의 의존성이 명확히 드러나지 않음               | 생성자를 통해 의존성이 명확하게 드러남              |
| **스프링의 권장 방식**           | 권장되지 않음                                     | 권장되는 방식                                     |

### **추천 사항**

스프링 팀과 커뮤니티는 **생성자 주입**을 권장합니다. 그 이유는 다음과 같습니다:

1. **불변성 보장**: 생성자 주입을 사용하면 `final` 필드를 사용할 수 있어 객체의 불변성을 유지할 수 있습니다.
2. **테스트 용이성**: 생성자를 통해 의존성을 주입하므로 단위 테스트 시 목 객체를 쉽게 주입할 수 있습니다.
3. **의존성 명확성**: 클래스가 어떤 의존성을 가지는지 생성자를 통해 명확하게 알 수 있습니다.
4. **순환 의존성 방지**: 생성자 주입은 순환 의존성을 컴파일 시점에서 감지할 수 있어 안전합니다.

### **예시: 생성자 주입**

```java
import org.springframework.stereotype.Service;

@Service
public class ExampleService {

    private final CategoryMapper categoryMapper;

    public ExampleService(CategoryMapper categoryMapper) {
        this.categoryMapper = categoryMapper;
    }

    // 서비스 로직
}
```

이 방식은 다음과 같은 장점을 가집니다:

- **명확한 의존성**: `ExampleService`가 `CategoryMapper`에 의존한다는 것이 생성자에서 명확히 드러납니다.
- **불변성**: `categoryMapper` 필드를 `final`로 선언할 수 있어 변경할 수 없습니다.
- **테스트 용이성**: 테스트 시 생성자를 통해 목 객체를 주입할 수 있습니다.

## 3. 요약

- **`@Autowired`를 사용한 의존성 주입**과 **직접 객체 생성** 방식의 차이점:
    - `@Autowired`는 스프링 컨테이너가 의존성을 관리하고 주입하여 코드의 유연성, 유지보수성, 테스트 용이성을 높여줍니다.
    - 직접 객체를 생성하면 스프링의 의존성 관리 기능을 활용할 수 없고, 코드의 결합도가 높아지는 단점이 있습니다.

- **필드 주입과 생성자 주입의 차이점**:
    - **필드 주입**은 간단하지만 의존성이 명확하지 않고 테스트가 어려우며, 권장되지 않습니다.
    - **생성자 주입**은 의존성이 명확하고 테스트가 용이하며, 스프링에서 권장하는 방식입니다.

따라서, 스프링 프레임워크를 사용하는 프로젝트에서는 **생성자 주입**을 사용하는 것이 모범 사례로 간주되며, 이는 코드의 품질과 유지보수성을 높이는 데 도움이 됩니다.