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