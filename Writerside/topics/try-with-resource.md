# try-with-resource 구문

### **`try-with-resources` 구문의 장단점과 활용 시 유의점**

`try-with-resources` 구문은 **자원이 자동으로 닫히도록 보장하는** Java의 기능으로, `AutoCloseable` 또는 `Closeable` 인터페이스를 구현한 객체를 대상으로 사용할 수 있습니다.

---

## **✅ 장점**

### **자원 해제를 자동으로 처리**
- `try` 블록이 끝나면 `close()` 메서드가 자동으로 호출됩니다.
- 개발자가 직접 `finally` 블록에서 `close()`를 호출할 필요가 없어 코드가 간결해집니다.

### **예외 발생 시 자원 누수 방지**
- `finally` 블록에서 `close()`를 호출할 경우, 예외가 발생하면 자원이 닫히지 않을 위험이 있지만 `try-with-resources`는 이를 보장합니다.

### **코드 가독성과 유지보수성 향상**
- `finally` 블록 없이도 안전한 자원 관리를 할 수 있어 코드가 깔끔해집니다.

### **멀티 캐치와 결합 가능**
- `try-with-resources` 내부에서 발생한 예외를 `catch` 블록으로 쉽게 처리할 수 있습니다.

---

## **⚠️ 유의해야 할 점**

### 1. **`AutoCloseable` 인터페이스를 구현한 클래스에서만 사용 가능**
- 직접 만든 클래스에서 사용하려면 `AutoCloseable` 인터페이스를 구현해야 합니다.
- 예시:
  ```java
  class MyResource implements AutoCloseable {
      @Override
      public void close() {
          System.out.println("Resource closed");
      }
  }
  ```

### 2. **자원 해제 순서에 주의**
- 여러 개의 자원을 사용할 경우, **반대로 닫힙니다** (LIFO 방식).
  ```java
  try (ResourceA a = new ResourceA(); ResourceB b = new ResourceB()) {
      // 실행 코드
  }
  ```
    - 위 코드에서는 `b.close()`가 먼저 호출되고, 그다음 `a.close()`가 호출됩니다.

### 3. **중첩된 `try` 블록 없이 한 번에 여러 자원 처리 가능**
- 예전에는 `finally`에서 각 자원을 닫았지만, `try-with-resources`를 활용하면 **여러 개의 자원을 한 번에 선언**할 수 있습니다.
  ```java
  try (BufferedReader br = new BufferedReader(new FileReader("file.txt"));
       FileWriter fw = new FileWriter("output.txt")) {
      // 자원 사용
  }
  ```

### 4. **기존 코드와의 호환성 고려**
- Java 7부터 지원되므로, 이전 버전(JDK 6 이하)에서는 사용할 수 없습니다.

---

## **💡 실용적인 활용 예시**

### **1. 파일 입출력 시 활용**
```java
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) {
    String line;
    while ((line = br.readLine()) != null) {
        System.out.println(line);
    }
} catch (IOException e) {
    e.printStackTrace();
}
```
- `BufferedReader`가 자동으로 닫혀 **자원 누수를 방지**합니다.

### **2. 데이터베이스 연결 시 활용 (JDBC)**
```java
try (Connection conn = DriverManager.getConnection(url, user, password);
     PreparedStatement pstmt = conn.prepareStatement("SELECT * FROM users");
     ResultSet rs = pstmt.executeQuery()) {

    while (rs.next()) {
        System.out.println(rs.getString("name"));
    }
} catch (SQLException e) {
    e.printStackTrace();
}
```
- **`Connection`, `PreparedStatement`, `ResultSet`을 안전하게 닫아** DB 연결 문제를 예방합니다.

---

## **🔎 정리**
| 항목 | 설명 |
|------|------|
| **장점** | 자동 자원 해제, 예외 발생 시 안전한 자원 관리, 가독성 향상 |
| **단점 및 유의점** | `AutoCloseable` 구현 필요, 자원 해제 순서 주의, JDK 7 이상 필요 |
| **주요 활용 예시** | 파일 입출력, 데이터베이스 연결, 네트워크 스트림 관리 |

---

### **👉 결론**
`try-with-resources`는 자원을 다룰 때 발생하는 **자원 누수 문제를 방지하고, 코드의 안정성을 높이는 중요한 기능**입니다.  
특히 **파일 처리, 데이터베이스 연결, 네트워크 스트림 처리** 등에서 적극적으로 활용하는 것이 좋습니다. 🚀