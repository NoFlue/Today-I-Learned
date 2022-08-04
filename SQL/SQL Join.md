레코드를 저장하기 위해 아래와 같이 테이블에 저장할 수 있습니다.  
아래 테이블은 `게시글의 제목` 과 `내용` , `작성자` 와 `작성자의 세부정보` 를 담고 있습니다.
|ID|Title|Contents|AuthorID|Name|Age|
|---|---|---|---|---|---|
|1|SQL 공부|SQL JOIN 내용|noflue|Kim|21|
|2|Android 공부|코루틴 비동기 처리|noflue|Kim|21|
|3|C++ 공부|포인터 사용법|cplusplus_user|Lee|22|

<br>

이 테이블은 이상한 점이 있는데 중복된 값이 존재한다는 것입니다. 중복된 값은 데이터의 용량이 늘어나게 되며, 처리 속도 및 연산 횟수가 늘어난다는 문제점이 있습니다.  
이를 해결하기 위해 테이블을 한 종류의 데이터를 담는 여러 개의 테이블로 나눠보겠습니다.
<br>

### Topics
|ID|Title|Contents|AuthorID|
|---|---|---|---|
|1|SQL 공부|SQL JOIN 내용|noflue|
|2|Android 공부|코루틴 비동기 처리|noflue|
|3|C++ 공부|포인터 사용법|cplusplus_user|

### Writers
|ID|Name|Age|
|---|---|---|
|noflue|Kim|21|
|cplusplus_user|Lee|22|

테이블을 작성한 글의 정보와 작성자의 정보로 나눴습니다.  
이렇게 테이블을 나누게 되면 중복된 값을 처리하는 일이 줄어듭니다. 하지만 처음 테이블과 비교를 해보면 읽기가 더 힘들어지게 되었습니다.  
<br>

그렇기 때문에 처음 테이블의 `읽기 쉽다는 장점` 과 쪼갠 테이블들의 `쓰기 좋다는 장점` 을 모두 가지기 위해 `Join` 이 나오게 되었습니다.

<br>
<br>
<br>

# Join
`Join` 은 여러 테이블을 하나로 통합할 수 있으며 `LEFT OUTER JOIN` , `INNER JOIN` , `FULL OUTER JOIN` 등 여러 종류가 있습니다.
<br>

`JOIN` 의 종류는 벤 다이어그램으로 나타낼 수 있습니다.  
<p align="center">
	<img src="https://dq-blog-files.s3.amazonaws.com/joins-tutorial/join_venn_diagram.svg">
</p>

- `Inner Join` : 두 테이블이 공통으로 가지고 있는 레코드들을 선택합니다. ( A ∩ B )
- `Left/Right Join` : 각 테이블만이 가지고 있는 레코드들과 두 테이블이 공통으로 가지고 있는 레코드들을 선택합니다. ( A or B )
- `Full Outer Join` : 모든 레코드를 선택합니다. 중복된 레코드가 있을 경우 하나의 레코드만 선택합니다. ( A ∪ B )
- `Exclusive Join` : 두 테이블이 공통으로 가지고 있는 레코드들을 제외한 각 테이블만이 가지고 있는 레코드들만 선택합니다. ( A - B or B - A )

<br>
<br>

## Left/Right Join
위에서 예시로 보인 테이블을 이용해 Join을 사용해보겠습니다
```sql
SELECT * FROM Topics AS T
LEFT JOIN Writers AS W
ON T.AuthorID = W.ID;
```

|ID(Topics)|Title|Contents|AuthorID|ID(Writers)|Name|Age|
|---|---|---|---|---|---|---|
|1|SQL 공부|SQL JOIN 내용|noflue|noflue|Kim|21|
|2|Android 공부|코루틴 비동기 처리|noflue|noflue|Kim|21|
|3|C++ 공부|포인터 사용법|cplusplus_user|cplusplus_user|Lee|22|

Join을 써서 중복된 값을 없애기 위해 쪼갰던 테이블을 하나로 합치게 되어 읽기 쉬워졌습니다.  
위 예시에는 공통된 레코드들만 존재하지만 만약 서로 공통된 레코드가 없으면 NULL 값을 반환합니다.  
<br>

Topics 테이블에 작성자의 아이디가 NULL 인 레코드를 추가한 뒤 쿼리문을 작성하면 아래와 같은 결과가 나타납니다.

|ID(Topics)|Title|Contents|AuthorID|ID(Writers)|Name|Age|
|---|---|---|---|---|---|---|
|1|SQL 공부|SQL JOIN 내용|noflue|noflue|Kim|21|
|2|Android 공부|코루틴 비동기 처리|noflue|noflue|Kim|21|
|3|C++ 공부|포인터 사용법|cplusplus_user|cplusplus_user|Lee|22|
|4|IOS 공부|Swift 기초 문법|NULL|NULL|NULL|NULL|

<br>
<br>

## Inner Join
`Inner Join` 은 교집합이기 때문에 `Left/Right Join` 과 달리 작성자의 아이디가 NULL 이면 공통된 값이 아니기 때문에 선택되지 않습니다.
```sql
SELECT * FROM Topics AS T
INNER JOIN Writers AS W
ON T.AuthorID = W.ID;
```

|ID(Topics)|Title|Contents|AuthorID|ID(Writers)|Name|Age|
|---|---|---|---|---|---|---|
|1|SQL 공부|SQL JOIN 내용|noflue|noflue|Kim|21|
|2|Android 공부|코루틴 비동기 처리|noflue|noflue|Kim|21|
|3|C++ 공부|포인터 사용법|cplusplus_user|cplusplus_user|Lee|22|

<br>
<br>

## Full Outer Join
`Full Outer Join` 은 모든 레코드를 선택하므로 실질적으로 잘 사용하지 않는다고 생각되는 Join 입니다.  
<br>

이번에는 Writers 테이블에 새 레코드를 추가한 뒤 쿼리문을 작성해보겠습니다.

```sql
SELECT * FROM Topics AS T
FULL OUTER JOIN Writers AS W
ON T.AuthorID = W.ID;
```

|ID(Topics)|Title|Contents|AuthorID|ID(Writers)|Name|Age|
|---|---|---|---|---|---|---|
|1|SQL 공부|SQL JOIN 내용|noflue|noflue|Kim|21|
|2|Android 공부|코루틴 비동기 처리|noflue|noflue|Kim|21|
|3|C++ 공부|포인터 사용법|cplusplus_user|cplusplus_user|Lee|22|
|4|IOS 공부|Swift 기초 문법|NULL|NULL|NULL|NULL|
|NULL|NULL|NULL|NULL|full_join|Lim|23|

Topics 테이블과 Writers 테이블에 각각 다른 새로운 레코드를 추가해버리게 되면 공통된 레코드가 존재하지 않기 때문에 서로의 레코드의 데이터들을 NULL 값으로 반환해버리게 됩니다.

<br>
<br>

## Exclusive Join
`Exclusive Join` 은 차집합이므로 공통되지 않는 레코드들을 선택합니다. 지원하는 키워드가 없으므로 `Left/Right Join` 을 사용하여 구현합니다.

```sql
SELECT * FROM Topics AS T
LEFT JOIN Writers AS W
ON T.AuthorID = W.ID
WHERE W.ID IS NULL;
```

|ID(Topics)|Title|Contents|AuthorID|ID(Writers)|Name|Age|
|---|---|---|---|---|---|---|
|4|IOS 공부|Swift 기초 문법|NULL|NULL|NULL|NULL|

<br>
<br>

## Self Join
제공하는 Join 키워드 외에 직접 Join을 구현할 수 있습니다. SELECT 를 할 때 2개 이상의 테이블을 선택하고 조건을 걸어주면 됩니다.
```sql
SELECT * FROM Topics AS T, Writers AS W
WHERE T.AuthorID != W.ID
ORDER BY T.ID ASC;
```

|ID(Topics)|Title|Contents|AuthorID|ID(Writers)|Name|Age|
|---|---|---|---|---|---|---|
|1|SQL 공부|SQL JOIN 내용|noflue|cplusplus_user|Lee|22|
|2|Android 공부|코루틴 비동기 처리|noflue|cplusplus_user|Lee|22|
|3|C++ 공부|포인터 사용법|cplusplus_user|noflue|Kim|21|