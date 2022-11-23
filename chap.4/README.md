# 4장
> 기본적인 데이터 사곳방식 단위를 '레코드' 에서 '레코드의 집합'으로 변경하려면 어느 정도 발상의 전환이 필요하다.

### 12강 - 집약

#### 여러 개의 레코드를 한 개의 레코드로 집약

집약함수(aggregate function)
- 여러 개의 레코드를 한개의 코드로 집약하는 기능
  - COUNT
  - SUM
  - AVG
  - MAX
  - MIN


`GROUP BY` 함수를 사용할 때 문자, 날짜 등도 MAX, MIN을 사용하는 습관을 들이는게 좋다.

`GROUP BY` 의 집약 조작 모두 `해시` 라는 알고리즘을 사용한다.

최근 `GROUP BY`를 사용하는 집약에서 정렬보다 해시를 사용하는 경우가 많다.

해시 함수를 사용해 해시키로 변환하고, 같은 해시 키를 가진 그룹을 모아 집약하는 방법

### 13강 - 자르기

1. 파티션
```sql
SELECT CASE WHEN age < 20 THEN '어린이'
            ELSE '어른'
            END AS age_class
FROM PERSON
GROUP BY CASE WHEN age < 20 THEN '어린이'
            ELSE '어른'
            END -- 표준
```
```sql
SELECT CASE WHEN age < 20 THEN '어린이'
            ELSE '어른'
            END AS age_class
FROM PERSON
GROUP BY age_class -- 비표준
```

`SELECT` 구에 붙인 `age_class` 라는 별칭을 사용해 `GROUP BY age_class` 처럼 단순하게 작성할 수 있는데, 이 방법은 표준에 없는 내용이다. 
> ORACLE로 작업할 때 매번 이렇게 작성하곤했는데, 비표준이라고 해서 놀랐다.

`GROUP BY` 구의 키로 필드에 연산을 추가한 식을 키로 한다면 어느정도 CPU 연산에 오버헤드가 걸릴수 있지만, 데이터를 뽑아온 뒤의 이야기 이므로 데이터 접근 경로에는 큰 영향이 없다.
집약 함수와 GROUP BY 의 실행 계획은 성능 측면에서 해시에 사용되는 워킹 메모리의 용량에 주의해야 한다.

### 마치며
- `GROUP BY` 구 또는 윈도우 함수의 `PARTITION BY` 구는 집합을 자를 때 사용
- `GROUP BY` 구 또는 윈도우 함수는 내부적으로 해시 또는 정렬 처리를 실행
- 해시 또는 정렬은 메모리를 많이 사용해 만약 메모리가 부족하면 일시 영역으로 저장소를 사용해 성능 문제를 일으킴
- `GROUP BY` 구 또는 윈도우 함수와 `CASE` 식을 함께 사용하면 굉장히 다양한 것을 표현할 수 있
