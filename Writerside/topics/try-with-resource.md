# try-with-resource 구문

최근 실행한 정적 분석에 제대로 닫히지 않은 입출력 클래스가 포착됐습니다.<br/>
try-catch 구문으로 자원을 해제해도 되지만, 특정 조건만 만족한다면 조금 더 간결하게 코드를 수정할 수 있습니다.<br/>
try-with-resources 구문을 활용하는 방법인데요. 자바 1.7 이상의 버전에서는 자원이 자동으로 닫히도록 보장하는 try-with-resources 구문을 지원합니다.<br/>
try-catch 구문으로 작성한 코드를 try-with-resources 구문으로 수정하면서 어떻게 다른지 비교해보겠습니다.<br/>

## try-catch 구문의 사용
BufferedReader와 FileReader는 모두 Reader 클래스를 상속합니다. Reader 클래스는 IOException을 유발할 수 있습니다.<br/> 
때문에 반드시 예외처리해야 합니다.<br/>
보편적으로 알려져있듯 try-catch 구문을 활용해 예외처리할 수 있습니다.

```java
BufferedReader br = null;
try {
    br = new BufferedReader(new FileReader("file.txt"));
    String line;
    while ((line = br.readLine()) != null) {
        System.out.println(line);
    }
} catch (IOException e) {
    e.printStackTrace();
} finally {
    if (br != null) {
        try {
            br.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
입출력 작업은 자원을 사용하고 다시 닫아줘야하는 작업입니다. <br/>
위와 같이 자원을 이중으로 사용하는 케이스라면 try-catch 구문으로 인해 코드가 장황해지고 가독성도 악화됩니다.<br/>
이 때 try-with-resources 구문을 활용하면 코드를 간결하게 수정할 수 있습니다.<br/>

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

try-with-resources 구문이 활용되는 주된 상황은 아래와 같습니다.

### 1. 파일 입출력 시 활용
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

### 2. 데이터베이스 연결 시 활용 (JDBC)
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
- `Connection`, `PreparedStatement`, `ResultSet`을 안전하게 닫아 DB 연결 문제를 예방합니다.

---

### **`try-with-resources` 구문 활용 시 유의점**

코드의 양을 줄이고 가독성을 높이는 유용한 표기법이지만, 모든 예외처리에 적용할 수 있는 방법은 아닙니다.<br/>
`AutoCloseable` 인터페이스를 구현한 객체를 대상으로 사용할 수 있습니다.<br/>
`BufferedReader`와 `FileReader`가 상속한 `Reader` 클래스는 `Closeable` 인터페이스를 상속하고 있습니다.<br/>
그리고 이 `Closeable`는 `AutoCloseable` 인터페이스를 상속하고 있습니다.<br/>
try-with-resource 구문은 `AutoCloseable`을 구현하고 있는 구현체의 close 함수를 실행함으로 써 자원을 해제합니다.<br/>
`AutoCloseable`를 구현하지 않은 클래스는 try-with-resources 구문으로 자원을 해제할 수 없고 직접 해제해야 합니다.<br/>

```JAVA
Socket socket = null;
try {
    socket = new Socket("example.com", 80);
    // 데이터 송수신 처리
} catch (IOException e) {
    e.printStackTrace();
} finally {
    if (socket != null) {
        try {
            socket.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```

---

## **✅ 장점**

### **자원 해제를 자동으로 처리**
- `try` 블록이 끝나면 `close()` 메서드가 자동으로 호출됩니다.
-  `finally` 블록에서 `close()`를 호출할 필요가 없어 코드가 간결해집니다.

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

## **🔎 정리**
| 항목 | 설명 |
|------|------|
| **장점** | 자동 자원 해제, 예외 발생 시 안전한 자원 관리, 가독성 향상 |
| **단점 및 유의점** | `AutoCloseable` 구현 필요, 자원 해제 순서 주의, JDK 7 이상 필요 |
| **주요 활용 예시** | 파일 입출력, 데이터베이스 연결, 네트워크 스트림 관리 |

---

### **👉 결론**
`try-with-resources`는 자원을 다룰 때 발생하는 **자원 누수 문제를 방지하고, 코드의 안정성을 높이는 중요한 기능**입니다.<br/>
특히 **파일 처리, 데이터베이스 연결, 네트워크 스트림 처리** 등에서 활용하면 try-catch-finally 구문보다 간결하고 정확하게 예외를 처리할 수 있습니다. 🚀

> **BONUS :: 자원관리 유의점**
>
> * 파일 입출력, 데이터베이스 연결, 소켓(socket)과 같은 자원은 유한합니다. 이러한 자원을 사용하지 않는 시점에는 반환해야 합니다.
> * Java 프로그램은 참조되지 않는 메모리 자원을 스스로 해제합니다. 하지만 파일 입출력, 데이터베이스 연결, 소켓과 같은 비-메모리 자원은 스스로 해제하지 않고 계속 점유합니다. 
> * 따라서 자원을 해제하지 않으면 다량의 요청이 들어올 때 Too many open files와 같은 오류가 발생하면서 처리가 중단될 수 있습니다.
>
{style="warning"}