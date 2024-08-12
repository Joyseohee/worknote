# 데이터를 실어나르는 객체

소프트웨어 개발에서 자주 사용되는 용어인 DTO, DAO, VO, Domain, Entity에 대해 설명해드리겠습니다.

### 1. DTO (Data Transfer Object)
**정의**: DTO는 계층 간 데이터를 전송하는 데 사용되는 객체입니다. 주로 서비스 계층과 프론트엔드(컨트롤러) 간의 데이터 교환을 위해 사용됩니다.

**특징**:
- 간단한 데이터 보관용 객체로, 비즈니스 로직이 포함되지 않습니다.
- 네트워크를 통해 데이터를 전송하거나, API 응답으로 데이터를 반환할 때 사용됩니다.
- 주로 GETTERS와 SETTERS만 포함합니다.

**예시**:
```java
public class UserDTO {
    private String username;
    private String email;

    // getters and setters
}
```

### 2. DAO (Data Access Object)
**정의**: DAO는 데이터베이스나 다른 영속성 저장소에 접근하는 메서드를 제공하는 객체입니다. 데이터베이스 연산(CRUD)을 캡슐화하여 비즈니스 로직과 데이터 접근 로직을 분리합니다.

**특징**:
- 데이터베이스와의 상호작용을 담당하는 메서드를 포함합니다.
- JDBC, JPA, MyBatis 등 다양한 기술을 사용하여 구현할 수 있습니다.

**예시**:
```java
public interface UserDAO {
    User findById(Long id);
    void save(User user);
    void update(User user);
    void delete(Long id);
}
```

### 3. VO (Value Object)
**정의**: VO는 특정 속성의 집합을 나타내며, 변경 불가능한(immutable) 객체입니다. 주로 값 자체를 표현하고 비교할 때 사용됩니다.

**특징**:
- 객체의 상태가 불변(immutable)입니다.
- VO 객체는 동일한 속성 값을 가지면 동일한 객체로 간주됩니다.
- 일반적으로 `equals()`와 `hashCode()` 메서드를 오버라이드합니다.

**예시**:
```java
public class AddressVO {
    private final String street;
    private final String city;

    public AddressVO(String street, String city) {
        this.street = street;
        this.city = city;
    }

    // getters and equals(), hashCode() override
}
```

### 4. Domain (도메인 객체)
**정의**: 도메인 객체는 비즈니스 로직과 비즈니스 상태를 포함하는 객체로, 주로 애플리케이션의 핵심 비즈니스 모델을 나타냅니다.

**특징**:
- 애플리케이션의 비즈니스 로직이 포함됩니다.
- 엔티티(Entity)와 유사하지만, 반드시 데이터베이스와 연동될 필요는 없습니다.
- 도메인 객체는 비즈니스 규칙을 적용하는 데 사용됩니다.

**예시**:
```java
public class User {
    private Long id;
    private String username;
    private String email;
    private String password;

    // Business logic methods
    public void changePassword(String newPassword) {
        // Password validation logic
        this.password = newPassword;
    }

    // getters and setters
}
```

### 5. Entity (엔티티)
**정의**: 엔티티는 데이터베이스 테이블과 매핑되는 객체로, 주로 ORM(Object-Relational Mapping) 프레임워크에서 사용됩니다. JPA와 같은 ORM 프레임워크를 통해 데이터베이스 테이블과의 매핑을 담당합니다.

**특징**:
- 데이터베이스 테이블의 행(row)을 나타냅니다.
- @Entity 어노테이션을 사용하여 엔티티임을 명시합니다.
- 필드에는 데이터베이스 컬럼과 매핑된 어노테이션이 사용됩니다.

**예시**:
```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String username;
    private String email;
    private String password;

    // getters and setters
}
```

### 요약
- **DTO (Data Transfer Object)**: 계층 간 데이터 전송을 위한 객체.
- **DAO (Data Access Object)**: 데이터베이스 접근을 캡슐화하는 객체.
- **VO (Value Object)**: 불변 객체로 값 자체를 표현하고 비교하는 객체.
- **Domain**: 비즈니스 로직과 상태를 포함하는 애플리케이션의 핵심 비즈니스 모델.
- **Entity**: 데이터베이스 테이블과 매핑되는 객체.

이러한 객체들은 각각 특정 역할과 책임을 가지고 있으며, 이를 통해 애플리케이션의 구조를 명확히 하고 유지보수성을 높일 수 있습니다.