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

> **자원관리가 중요한 이유**
>
> * 파일 기술자(file descriptor), 데이터베이스 연결, 소켓(socket)과 같은 자원은 유한합니다. 이러한 자원을 더는 사용하지 않는다면 적절히 반환해야 합니다.
> * Java 프로그램은 참조되지 않는 메모리 자원을 스스로 해제합니다. 하지만 파일 기술자, 데이터베이스 연결, 소켓과 같은 비-메모리 자원은 스스로 해제하지 않고 계속 점유합니다. 따라서 앞의 예제처럼 fis를 해제하지 않으면 다량의 요청이 들어올 때 다음과 같은 오류가 발생하면서 처리가 중단될 수 있습니다.
> Too many open files
> 이처럼 자원을 오래 점유하면 처리 성능이 저하될 수 있습니다. 자원을 더는 사용하지 않는다면 최대한 빨리 해제하십시오. 앞의 예제의 경우, loadFile 메서드 이후로 fis를 사용하지 않는다면 다음과 같이 try...finally 문을 사용하여 fis를 바로 해제해야 합니다.
>
{style="warning"}


예를 들어 다음은 파일을 열고 사용한 후 해제하지 않은 채로 다른 작업을 수행하는 코드입니다.

```java
FileInputStream fis = new FileInputStream("data.txt");
loadFile(fis);
doSomethingElse();
```
```java
FileInputStream fis = null;
try {
  FileInputStream fis = new FileInputStream("data.txt");
  loadFile(fis);
} finally {
  if (fis != null) {
    try { fis.close(); }
    catch (Exception e) { logger.error(e); }
  }
}
doSomethingElse();
```

이때 반드시 finally 블록 내에서 자원을 해제하십시오. 그래야만 예외가 발생하였을 때도 올바르게 자원을 해제할 수 있습니다. 예를 들어 다음과 같이 구현하면,
```Java
FileInputStream fis = null;
loadFile(fis);
fis.close();
doSomethingElse();
```
loadFile 메서드에서 예외가 발생되면 fis를 해제하는 구문을 건너뛰게 됩니다.

자원을 담은 변수에 새 자원을 담을 때는 먼저 기존 자원을 해제해야 합니다. 예를 들어 다음과 같은 프로그램은 언뜻 잘 작성된 프로그램으로 보입니다.

```Java
FileInputStream fis = null;
try {
  fis = new FileInputStream("data1.txt"); // ...(1)
  loadFile(fis);
  fis = new FileInputStream("data2.txt"); // ...(2)
  loadFile(fis);
} finally {
  if (fis != null) {
    try { fis.close(); }
    catch (Exception e) { logger.error(e); }
  }
}
doSomethingElse();
```
하지만 (2)가 수행되면 이후로는 (1)에서 생성한 자원에 접근할 수가 없으므로 이는 잘못 작성된 프로그램입니다. 다음과 같이 기존 자원을 해제한 후에 (2)를 수행해야 합니다.

```Java
fis = new FileInputStream("data1.txt"); // ...(1)
loadFile(fis);
fis.close();
fis = new FileInputStream("data2.txt"); // ...(2)
...
```

Java 7 버전 이상을 지원한다면 직접 자원을 해제하는 것보다 다음과 같이 try-with-resource 문을 사용하는 것이 더 간편합니다.

```Java
try (FileInputStream fis = new FileInputStream("data.txt")) {
  loadFile(fis);
}
doSomethingElse();
```
