# DB 트리거 실행 시점

PostgreSQL에서 트리거를 생성할 때 트리거 실행 시점을 지정할 수 있다. 트리거 실행 시점은 아래 세가지 중 한 가지로 지정할 수 있다.

* 활성화 시간이 **BEFORE**이면 트리거 이벤트가 실행되기 전에 영향을 받은 행 세트의 각 행에 대해 트리거 조치가 활성화됩니다. 그러므로 각 행에 대해 BEFORE 트리거의 실행이 완료된 후에만 주제 테이블이 수정됩니다.
* 활성화 시간이 **AFTER**이면 트리거 수준에 따라 영향을 받은 행 세트의 각 행 또는 명령문에 대해 트리거 조치가 활성화됩니다. 이는 참조 제한조건 조치를 비롯하여 트리거 이벤트가 완료된 후 및 데이터베이스 관리자가 모든 제한조건을 검사한 후 발생합니다. AFTER 트리거에는 FOR EACH ROW 또는 FOR EACH STATEMENT 트리거 수준이 있을 수 있습니다.
* 예를 들어, 다음 트리거의 활성화 시간은 employee에 대한 INSERT 조작 이후입니다.
* 활성화 시간이 **INSTEAD OF**이면 트리거 이벤트를 실행하는 대신 영향을 받은 행 세트의 각 행에 대해 트리거 조치가 활성화됩니다. INSTEAD OF 트리거에는 FOR EACH ROW 트리거 수준이 있어야 하고 주제 테이블은 뷰여야 합니다. 그 외 다른 트리거는 주제 테이블을 뷰로 사용할 수 없습니다.


   ```sql
      CREATE TRIGGER NEW_HIRE
      AFTER INSERT ON EMPLOYEE
      FOR EACH ROW
      UPDATE COMPANY_STATS SET NBEMP = NBEMP + 1
   ```

> **row-level과 statement-level**
>
> 트리거는 트리거 수준에 따라 실행됩니다. 
> FOR EACH ROW(row-level)로 지정 시 event의 영향을 받은 행 수 만큼 실행됩니다.
> FOR EACH STATEMENT는 전체 트리거 이벤트에 대해 한 번만 실행됩니다. 
>
{style="warning"}



<seealso>
    <category ref="wrs">
        <a href="https://www.postgresql.org/docs/current/sql-createtrigger.html">PostgreSQL: Documentation: 17: CREATE TRIGGER</a>
        <a href="https://www.ibm.com/docs/ko/db2/11.5?topic=dt-specifying-when-trigger-fires-before-after-instead-clauses">트리거 실행 시점 지정(BEFORE, AFTER 및 INSTEAD OF절)</a>
        <a href="https://www.ibm.com/docs/ko/db2/11.1?topic=dt-specifying-what-makes-trigger-fire-triggering-statement-event">트리거를 실행하는 항목 지정(트리거링 명령문 또는 이벤트)</a>
    </category>
</seealso>