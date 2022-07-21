# Query Info

> -> 와 ->>의 차이

    -> 은 JSON의 형태로 받아오게 되고
    ->> 은 Text의 형태로 받아오게 된다.

> @>
    
    @>는 'contains' operator이다. arrays, range types, geometric types, JSON등의 자료형으로 정의되어 있다. 


> Postgresql의 Upsert 구문

```sql
    INSERT INTO [TABLE] (COLUMN1, COLUMN2, ...) 
      VALUES (VALUE1, VALUE2, ...)      
      ON CONFLICT ([column_name / ON CONSTRAINT constraint_name/ WHERE predicate])
      [DO NOTHING]      
      [DO UPDATE SET column1 = value1, ...]
```

    ON CONFLICT column_name : 특정 컬럼명을 기준으로 체크를하며 컬럼을 여러개 넣을수도 있다.(pk기준)

    ON CONFLICT ON CONSTRAINT ~ : 테이블 생성시 만든 constraint명을 기준으로 체크한다.

    ON CONFLICT WHERE predicate : UNIQUE INDEX 생성 시 사용

그리고 ON CONFLICT에서 걸릴 경우 그 아래에서 사용하는 것들은 다음과 같다.

    DO NOTHING : 아무런 동작을 안하도록 설정

    DO UPDATE SET ~ : 기존에 쓰던 UPDATE의 역할 가능 


> Returning

    PGSQL에서는 INSERT, UPDATE, DELETE 쿼리 실행 후 처리 ROWS만 알려주는데, 조금더 상세한 정보를 알수 있는 방법이 있습니다.
    
    INSERT, UPDATE, DELETE 쿼리  뒤에 RETURNING *난 입력해주면 실행한 쿼리의 결과를 출력해주게됩니다.

출처: https://mine-it-record.tistory.com/342
