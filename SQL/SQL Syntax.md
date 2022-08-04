> Android Jetpack Room 을 쓰면서 쿼리문을 다루지 않으면 정확한 데이터 관리를 할 수 없다는 불편한 점이 있었다. 그렇기에 [w3schools](https://www.w3schools.com/sql/default.asp) 와 책을 통해서 쿼리문을 배워나가야 겠다.  
> <br>
> 쿼리문을 효율적으로 사용할 수 있다는 점과 SQLD를 딴다는 목표를 가져야겠다.

<br>

# SQL
`SQL` 은 `Structured Query Language (구조화 질의어)` 의 약자로 `DBMS` 의 데이터를 관리하기 위한 언어입니다.  
<br>
SQL의 문법은 크게 3가지로 나뉘어져 있습니다.
- DDL(Data Definition Language, 데이터 정의 언어) :  각 데이터를 집합해 놓은 테이블을 정의하는 언어입니다. (CREATE, ALTER, DROP, ...)
- DML(Data Manipulation Language, 데이터 조작 언어) : 각 데이터를 CRUD 등 관리하는 언어입니다. (SELECT, INSERT, DELETE, UPDATE, ...)
- DCL(Data Control Language, 데이터 제어 언어) : 데이터베이스에 접근하거나 객체에 권한을 주는 언어입니다. (GRANT, REVOKE, ...)

<br>

모든 프로그래밍 언어에 규칙이 있듯이 SQL에도 규칙이 있습니다.
- SQL은 대소문자를 가리지 않습니다.
- SQL의 명령은 `;` (세미콜론)으로 끝나야 합니다.
- 고유의 값은 `''` (따옴표)로 감싸줍니다.
- SQL에서 객체를 나타낼 때는 ` `` ` (백틱)으로 감싸줍니다.
- 한 줄 주석은 `--` 를 추가해 처리할 수 있으며 여러 줄 주석은 `/* */` 로 처리할 수 있습니다.

<br>
<br>
<br>

## SELECT
`SELECT` 문은 데이터베이스에서 데이터를 선택할 때 사용합니다. 반환된 데이터는 결과 테이블에 저장됩니다,
```sql
SELECT column1, column2 FROM table_name;
```
<br>

`SELECT DISTINCT` 문은 중복되지 않게 데이터를 선택합니다.  
`SELECT DISTINCT` 를 사용해보기 위해 `User` 테이블을 아래와 같이 생성해보겠습니다.

|Id|Name|Age|Gender|
|---|---|---|---|
|1|Kim|21|Man|
|2|Lee|20|Woman|
|3|Lim|24|Man|
|4|Bae|20|Woman|

```sql
SELECT DISTINCT Age FROM User;
```
결과는 21, 20, 24 가 나오게 됩니다.

<br>
<br>

## WHERE
`WHERE` 은 조건문과 같이 지정된 조건을 충족한 데이터만 반환합니다.
```sql
SELECT column1, column2 FROM table_name
WHERE condition;
```

|Id|Name|Age|Gender|
|---|---|---|---|
|1|Kim|21|Man|
|2|Lee|20|Woman|
|3|Lim|24|Man|
|4|Bae|20|Woman|

```sql
SELECT * FROM User
WHERE Gender='Man';
```
결과는
|Id|Name|Age|Gender|
|---|---|---|---|
|1|Kim|21|Man|
|3|Lim|24|Man|

<br>

조건문에 연산자를 넣을 수 있듯이 `WHERE` 에도 연산자를 제공합니다.
|연산자|설명|예시|
|:---:|---|---|
|=|같음|WHERE Name='Kim'
|>|큼|WHERE Age>20|
|<|작음|WHERE Age<23|
|>=|크거나 같음|WHERE Age>=20|
|<=|작거나 같음|WHERE Age<=21|
|<>|같지 않음 (일부 버전에서는 != 연산자로 사용합니다)|WHERE Name!='Lee'|
|BETWEEN|두 값의 범위|WHERE Age BETWEEN 22 AND 24|
|LIKE|지정된 값의 패턴과 일치하는 경우|WHERE Name LIKE 'L%'|
|IN|조건에 맞는 값을 여러 개 지정할 경우|WHERE Id In (1, 4)|

<br>

논리 연산자도 사용할 수 있습니다.
|연산자|설명|예시|
|:---:|---|---|
|AND|모든 조건이 참인 경우|WHERE Name='Kim' AND Age=21|
|OR|하나의 조건이라도 참인 경우|WHERE Id=2 OR Age>=23|
|NOT|조건이 참이 아닐 경우|WHERE NOT Id=1|

<br>
<br>

## ORDER BY
`ORDER BY` 는 결과 테이블을 오름차순 혹은 내림차순으로 정렬합니다.  정렬할 기준을 정해주지 않으면 기본 값으로 오름차순으로 정렬하게 됩니다.
```sql
SELECT column1, column2
FROM table_name
ORDER BY column1, column2 ASC|DESC;
```

<br>
<br>

## INSERT INTO
`INSERT INTO` 는 테이블에 새 레코드(행)을 추가합니다.  
새 레코드를 추가하는 방법에는 2가지가 있습니다.  
<br>

**첫 번째**는 각 열과 삽입할 값을 지정합니다.
```sql
INSERT INTO table_name (column1, column2, column3)
VALUE (value1, value2, value3);
```

<br>

**두 번째**는 삽입할 값만 지정합니다. 하지만 열의 순서대로 값을 넣기 때문에 추가하기 전 올바르게 작성했는지 확인해야 합니다.
```sql
INSERT TO table_name
VALUES (value1, value2, value3);
```

<br>

예시로 아래와 같은 테이블(Region)에 데이터를 추가해보겠습니다.
|Id|Name|Address|City|Country|
|---|---|---|---|---|
|1|청와대|종로구 청와대로 1|서울|대한민국|
<br>

```sql
INSERT INTO Region (Name, City, Country)
VALUES (유튜브, 캘리포니아, 미국)
```
만약 위와 같이 `Address` 열의 값을 추가하지 않으면 
|Id|Name|Address|City|Country|
|---|---|---|---|---|
|1|청와대|종로구 청와대로 1|서울|대한민국|
|2|유튜브|null|캘리포니아|미국|

다음과 같이 해당 열의 값은 `null` 로 처리됩니다.

<br>
<br>

## NULL
해당 열의 값이 선택사항일 경우 값이 `NULL` 일 경우도 있습니다. `NULL` 값은 비교할 수 있는 값이 아니기 때문에 연산자로 비교할 수 없습니다.  
<br>

`NULL` 값인지 판단하기 위해선 `IS NULL` 와 `IS NOT NULL` 키워드를 사용해야 합니다.
```sql
SELECT column1 FROM table_name
WHERE column1 IS NULL;

또는

WHERE column1 IS NOT NULL;
```

<br>
<br>

## UPDATE
`UPDATE` 는 기존 레코드의 값을 변경합니다.
```sql
UPDATE table_name
SET column1 = value1, column2 = value2
WHERE condition;
```
<br>

`WHERE` 을 통해 조건을 걸어 원하는 레코드만 값을 업데이트 할 수 있습니다. 만약 조건을 걸지 않을 경우 해당 열의 값을 전부 변경하게 됩니다.

<br>
<br>

## DELETE
`DELETE` 는 레코드를 제거합니다.
```sql
DELETE FROM table_name
WHERE condition;
```
<br>

`UPDATE` 와 마찬가지로 `WHERE` 을 통해 조건을 걸지 않을 경우 테이블이 삭제가 됩니다.

<br>
<br>

## Functions
SQL에도 다른 프로그래밍 언어와 같이 연산을 할 수 있는 함수들이 존재합니다.  
<br>

### MIN, MAX
최솟값과 최댓값을 반환합니다.
```sql
SELECT MIN(column1) FROM table_name
WHERE condition;

SELECT MAX(column2) FROM table_name
WHERE condition;
```

<br>

### COUNT, SUM, AVG
`COUNT` 는 해당 열의 행의 수,  `SUM` 은 해당 열의 모든 값을 합한 값, `AVG` 는 해당 열의 평균값을 반환합니다.
```sql
SELECT COUNT(DISTINCT column1) FROM table_name

SELECT SUM(column2) FROM table_name

SELECT AVG(column3) FROM table_name
```

<br>
<br>

## Wildcards
`LIKE` 키워드를 사용하기 위해선 패턴을 입력해야 합니다. 패턴을 만들기 위해서는 `Wildcards` 를 활용해야 합니다.
|문자(MS, SQL Server)|설명|예시|
|:---:|---|---|
|*, %|0개 이상의 문자를 나타냅니다.|`bl*` -> `black`, `blue`, `blank`|
|?, _|하나의 문자를 나타냅니다.|`h?t` -> `hat`, `hit`, `hot`|
|[]|괄호 안에 있는 문자 중 하나를 나타냅니다.|`h[ao]t` -> `hat`, `hot`|
|!, ^|괄호 안의 문자를 제외한 하나를 나타냅니다.|`h[!ao]t` -> `hit`, `hut`|
|-|괄호 안의 문자들의 범위에 속하는 문자 하나를 나타냅니다.|`h[a-c]t` -> `hat`, `hbt`, `hct`|
|#|숫자 하나를 나타냅니다.|`1#2` == `1[0-9]2`|
<br>

`Wildcards` 를 사용해 아래와 같은 조건을 만들 수 있습니다.
- 두 번째 문자가 `s` 인 문자열 -> `'_s%`
- `el` 이 포함된 문자열 -> `'%el%`
- `a` 로 시작하고 `o` 로 끝나는 문자열 -> `'a%o'`