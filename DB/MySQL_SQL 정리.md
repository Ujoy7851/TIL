## MySQL

MySQL은 관계형 데이터베이스 관리 시스템으로, 다중 사용자와 다중 스레드를 지원한다. 또한 C, C++, JAVA, PHP 등을 위한 다양한 API를 제공한다.

SQL은 목적에 따라 크게 세 가지 범주로 구분한다.

1. **DDL(Data Definition Language)** : 데이터 베이스나 테이블을 생성, 삭제하거나 그 구조를 변경 (CREATE, ALTER, DROP)
2. **DML(Data Manipulation Language)** : 데이터를 처리하거나 조회, 검색 (INSERT, UPDATE, DELETE, SELECT)
3. **DCL(Data Control Language)** : 데이터의 관리를 위해 보안성 및 무결성 등을 제어 (GRANT, REVOKE)

---

## 기본 문법

**MySQL 구문**

```sql
SELECT * FROM User;
select * fRoM  user;
```

- 구문의 끝에는 `;` 를 붙인다.
- 키워드와 구문에서 대소문자를 구분하지 않는다.

**MySQL 주석**

```sql
# 주석1
-- 주석2
/*
주석3
*/
```

---

아래에서 주요 구문들을 설명하기 위해 다음의 예제 테이블을 사용한다.

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/75a8c0a5-f013-4b57-8641-ed3ad4a4bc83/_2020-04-28_15-19-22.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20200706%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200706T133604Z&X-Amz-Expires=86400&X-Amz-Signature=da7ab669daba88848638ccd030dd221dcc6a83d315b572d147f9f779ef90415b&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22_2020-04-28_15-19-22.png%22)

```sql
# create table
CREATE TABLE Reservation (
	ID INT,
	Name VARCHAR(30),
	ReserveDate DATE,
	RoomNum INT
);
CREATE TABLE Customer(
	ID INT,
	Name VARCHAR(30),
	Age INT,
	Address VARCHAR(20)
);

#Insert data
INSERT INTO Reservation(ID, Name, ReserveDate, RoomNum) VALUES(1, '홍길동', '2016-01-05', 2014);
INSERT INTO Reservation(ID, Name, ReserveDate, RoomNum) VALUES(2, '임꺽정', '2016-02-12', 918);
INSERT INTO Reservation(ID, Name, ReserveDate, RoomNum) VALUES(3, '장길산', '2016-01-16', 1208);
INSERT INTO Reservation(ID, Name, ReserveDate, RoomNum) VALUES(4, '홍길동', '2016-03-17', 504);

INSERT INTO Customer (ID, Name, Age, Address) VALUES (1, '홍길동', 17, '서울');
INSERT INTO Customer (ID, Name, Age, Address) VALUES (2, '임꺽정', 11, '인천');
INSERT INTO Customer (ID, Name, Age, Address) VALUES (3, '장길산', 13, '서울');
INSERT INTO Customer (ID, Name, Age, Address) VALUES (4, '전우치', 17, '수원');
```

---

## CREATE

**데이터베이스 생성**

```sql
CREATE DATABASE Hotel;
USE Hotel;
```

데이터베이스를 생성한 뒤에 해당 데이터베이스를 사용하려면 `USE` 를 사용해 데이터베이스를 선택해야 한다.

유닉스 환경에서는 데이터베이스의 대소문자를 구분, 윈도우 환경에서는 구분하지 않는다. 하지만 가독성을 위해 구분하여 사용하는 것이 좋다.

**테이블 생성**

```sql
CREATE TABLE Reservation (
	ID INT,
	Name VARCHAR(30),
	ReserveDate DATE,
	RoomNum INT
);
```

- 테이블 이름, 필드 이름, 필드 타입을 명시해 테이블을 생성한다.
- `SHOW TABLES` 구문을 통해 생성된 테이블 목록을 확인할 수 있다.
- `DESCRIBE` 구문을 사용해 테이블의 상세 정보를 확인할 수 있다.

**MySQL 타입**

![https://www.mysqltutorial.org/wp-content/uploads/0211/03/MySQL-Data-Types.jpg](https://www.mysqltutorial.org/wp-content/uploads/0211/03/MySQL-Data-Types.jpg)

- `CHAR` vs `VARCHAR` : `CHAR`와 `VARCHAR`는 길이를 선언해 사용하는데, 길이를 넘어가는 경우 데이터가 잘린다. 명시한 길이보다 짧은 문자열을 저장할 경우 `CHAR`는 고정형으로 명시한 만큼의 데이터를 할당하는 반면, `VARCHAR`는 가변형으로 입력한 크기만큼만 공간을 할당한다. `CHAR` 타입은 사이즈가 고정되어 있어 검색속도 및 읽는 속도가 더 빠르지만 데이터의 낭비가 있을 수 있다.
- `VARCHAR` vs `TINYTEXT` : 둘다 가변길이 데이터를 저장할 때 사용하지만, `VARCHAR`의 경우 최대 길이를 선언하기 때문에 저장될 데이터의 크기를 제한할 수 있다.
- `TEXT` vs `BLOB` : 둘다 큰 데이터를 저장할 때 사용한다. `TEXT`는 문자 데이터를, `BLOB`은 바이너리 데이터를 저장한다. `TEXT`는 `VARCHAR`와 비슷하지만 기본값을 가질 수 없으며, `BLOB`은 주로 이미지나 파일 데이터를 저장하는데 사용한다.

**제약 조건 (Constraint)**

데이터의 무결성을 지키기 위해 데이터를 입력받을 때 실행되는 검사 규약으로, `CREATE` 로 테이블을 생성하거나, `ALTER`로 필드를 추가할 때 설정할 수 있다.

1. `NOT NULL` : 해당 필드가 NULL 값을 저장할 수 없다.
2. `UNIQUE` : 서로 다른 값을 가져야 한다.
3. `PRIMARY KEY` : `NOT NULL`과 `UNIQUE` 제약 조건의 특징을 모두 가진다.
4. `FOREIGN KEY` : 다른 테이블에 의존시킨다.
5. `DEFAULT` : 기본값을 설정한다.

**키워드**

- `AUTO INCREMENT`: 해당 필드의 값을 1부터 시작해 새로운 데이터가 추가될 때마다 1씩 증가된 값을 저장한다.

---

## ALTER

**데이터베이스 수정**

문자 집합이나 콜레이션과 같은 데이터베이스의 특성을 수정

(콜레이션이란, 데이터베이스의 검색이나 정렬같은 작업에 사용하는 비교를 위한 규칙의 집합을 말한다.)

```sql
ALTER DATABASE Hotel CHARACTER SET=utf8 COLLATE=euckr_korean_ci;
```

`CHARACTER SET` 종류

- utf8
- euckr (한글 지원 문자셋)

`COLLATE` 종류

- utf8_bin
- utf8_general_ci (기본 설정)
- euckr_bin
- euckr_korean_ci

**테이블 수정**

1. `ADD` : 새로운 필드 추가

```sql
ALTER TABLE Reservation;
ADD Phone INT;
```

2. `DROP` : 기존 필드 삭제

```sql
ALTER TABLE Reservation;
DROP RoomNum;
```

3. `MODIFY COLUMN` : 필드 타입 변경

```sql
ALTER TABLE Reservation;
MODIFY COLUMN ReserveDate VARCHAR(20);
```

---

## DROP

**데이터베이스 삭제**

```sql
DROP DATABASE Hotel;
```

데이터베이스 삭제 시 포함된 테이블과 데이터도 같이 삭제된다.

**테이블 삭제**

```sql
DROP TABLE Reservation;
TRUNCATE TABLE Reservation;
```

`TRUNCATE` 를 사용해 테이블에 저장된 데이터만 지울 수 있다.

---

## INSERT

**테이블에 레코드 추가**

```sql
INSERT INTO Reservation (ID, Name, Reservation, RoomNum)
VALUES (5, 'JOY', '2020-04-28', 1101);
INSERT INTO Reservation
VALUES (5, 'JOY', '2020-04-28', 1101);
```

필드의 이름을 생략한 경우, 데이터베이스의 스키마와 같은 순서대로 값이 자동 대입된다. 데이터의 값이 문자열인 경우 반드시 `''` 를 사용해야 한다.

다음의 경우에 필드값을 생략할 수 있다.

1. NULL을 저장할 수 있도록 설정된 필드
2. `DEFAULT` 제약조건이 설정된 필드
3. `AUTO_INCREMENT` 키워드가 설정된 필드

---

## UPDATE

**레코드 내용 수정**

```sql
UPDATE Reservation
SET RoomNum = 2101
WHERE Name = 'JOY';
```

`WHERE` 절의 조건에 해당하는 레코드의 값을 수정한다.

`WHERE` 절을 생략하면 모든 레코드의 값이 변경된다.

---

## DELETE

**레코드 삭제**

```sql
DELETE FROM Reservation
WHERE Name = 'JOY';
```

`WHERE` 절의 조건에 해당하는 레코드가 삭제되고, 생략한 경우 모든 데이터가 삭제된다.

---

## SELECT

**테이블의 모든 필드 선택**

```sql
SELECT * FROM Reservation;
```

**특정 조건의 레코드 선택**

```sql
SELECT *
FROM Reservation
WHERE ID <= 3 AND ReserveDate > '2016-02-01';
```

`WHERE` 절 안에 `AND` 나 `OR` 연산자를 이용해 여러개의 조건을 명시할 수 있다.

**특정 필드만 선택**

```sql
SELECT Name, RoomName
FROM Reservation
WHERE ID <= 3 AND ReserveDate > '2016-02-01';
```

**중복값 제거**

```sql
SELECT DISTINCT Name
FROM Reservation;
```

`DISTINCT` 키워드를 사용해 같은 필드에서 중복되는 값을 가지는 레코드가 있을 때, 그 값이 한번만 선택되도록 할 수 있다.

**선택 결과의 정렬**

```sql
SELECT * 
FROM Reservation
ORDER BY ReserveDate DESC, RoomNum ASC;
```

- `ORDER BY` 키워드를 사용해 선택 결과를 정렬할 수 있다. 기본 설정은 오름차순이고, `ASC`, `DESC` 키워드로 직접 오름차순과 내림차순을 명시할 수 있다.
- `ORDER BY BINARY` 를 사용하면 대소문자까지 구분하여 정렬할 수 있다.
- 여러 필드의 데이터를 `,` 를 사용해 한 번에 정렬할 수 있다.

**별칭을 이용한 처리**

```sql
SELECT ReserveData, CONCAT(RoomNum, ' : ', Name) AS ReserveInfo
FROM Reservation;
SELECT * FROM Reservation AS R;
```

- 필드와 테이블에 임시로 별칭(alias)를 부여하고, SELECT 문 안에서 사용할 수 있다.
- `CONCAT` : 인수로 전달받은 문자열을 모두 합쳐 하나의 문자열로 반환하는 함수
- 별칭의 이름이 ASCII가 아닌 문자열이면 `"` 를 사용해야 한다.
- 조건문(`WHERE`)에서 사용하면 에러 발생 (where → select의 순서로 쿼리가 실행됨)
- 테이블에 별칭을 부여하면 `JOIN` 사용시 유용하다.

---

## 연산자

**산술 연산자**

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/59e3ea6d-dd0f-4871-82e9-c8c2fd23192d/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20200706%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200706T133659Z&X-Amz-Expires=86400&X-Amz-Signature=f068d1ffa1c09f0219868213d7d51ce68b8373e2606d2611e6ceb7321ad145a9&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

```sql
SELECT 504.7 + 13, 504.7 * 0.9, 504.7 / 2, 504.7 DIV 2, 504.7 % 2;
```

**대입 연산자**

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/f54d3339-294d-4d62-9239-830a7252e373/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20200706%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200706T133752Z&X-Amz-Expires=86400&X-Amz-Signature=024dd8cf1cc151df3f1799c2e15cea0c7d367ff6388dfa6dc0156bcf27c38258&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

- `=` 연산자는 `SET` 문을 제외하고는 비교 연산자로 해석된다.
- `:=` 연산자는 항상 대입 연산자로 해석된다.

**비교 연산자**

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/3e441f02-d2f7-44af-b11b-8ecc41ada7f9/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20200706%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200706T133851Z&X-Amz-Expires=86400&X-Amz-Signature=82917b060940d1ae5c3d2c5ce5af54f429fd46a96a5ee71288fa8e7377376504&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

```sql
SELECT
3 = 3, 0 = NULL, 1 IS TRUE, 1 IS NULL,
3 BETWEEN 2 AND 7, 5 IN (2, 3, 4, 5);
```

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/39a8d592-5559-4280-9581-dfa874283084/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20200706%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200706T133931Z&X-Amz-Expires=86400&X-Amz-Signature=c005a3c438cf3544fc888a09c2d024e0a000d300f0632bfba4ba8a4409c9453f&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

**비트 연산자**

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/1b0b5e00-55a1-42fd-bd5d-ff007b55a9b8/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20200706%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200706T133945Z&X-Amz-Expires=86400&X-Amz-Signature=0f8a2c3a88bdb751dab274dd0bb833292aa95ec5aff4bc04b0ead8baf57055de&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

**흐름 제어**

- `CASE` : 조건식의 값에 따라 다른 값을 반환

```sql
SELECT CASE 0
    WHEN 0 THEN 'zero'
    WHEN 1 THEN 'one'
    ELSE 'more'
END;
# result: zero
```

- `IF()`

```sql
SELECT IF(0 < 1, 'yes', 'no');
```

- `IFNULL()` : 첫번째 인수의 값이 NULL이면 첫번째 인수를, 아니면 두번째 인수를 반환한다.

```sql
SELECT IFNULL(NULL, '전달받은 값이 null입니다.');
# result: 전달받은 값이 null입니다.
```

- `NULLIF()` : 두 인수가 같으면 NULL을, 다르면 첫번째 인수를 반환한다.

```sql
SELECT NULLIF(3, 3);
# result: NULL
```

**패턴 매칭**

특정 패턴을 검색하기 위한 연산자

- `LIKE` : 특정 패턴을 포함하는 데이터만을 검색한다.

```sql
SELECT * FROM Reservation
WHERE Name LIKE '홍%';

SELECT * FROM Reservation
WHERE RoomNum NOT LIKE '20__';
```

- 와일드카드
    - %는 0개 이상의 문자 대체
    - _ 는 1개의 문자를 대체

- `REGEXP` : 정규 표현식을 토대로 `LIKE` 보다 복잡한 패턴 검색에 사용한다.

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/3e5589ae-cdb8-4e25-8c42-095f16c89baf/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20200706%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200706T134056Z&X-Amz-Expires=86400&X-Amz-Signature=7b63139f4a801f2fc29282a28d1d4547317d55a4e998250934901c24d294bf68&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

```sql
SELECT * FROM Reservation
WHERE Name REGEXP '^홍|산$';
# '홍'으로 시작하거나 '산'으로 끝나는 레코드 선택
```

**타입 연산**

MySQL은 비교나 검색을 수행할 때 데이터 타입이 다른 경우, 자동으로 변환하여 처리한다. 하지만 사용자가 명시적으로 타입을 변환시킬 수도 있다.

- `BINARY` : 문자열을 바이너리 문자열로 변환시켜 바이트를 기준으로 비교나 검색을 수행할 수 있게 한다.

```sql
SELECT BINARY 'a' = 'A',
'a' = 'A';
# result: 0 1
```

- `CAST()` : 명시된 타입으로 변환하여 반환한다.
    - `AS` 절에서 사용가능한 타입 - BINARY, CHAR, DATE, DATETIME, TIME, DECIMAL, JSON, NCHAR, SIGNED [INTEGER], UNSIGNED [INTEGER]

```sql
SELECT 4 / '2',
4 / 2,
4 / CAST('2' AS UNSIGNED);
# result: 2 2.0000 2.0000
```

- `CONVERT()` : 명시된 타입으로 값을 변환하거나 데이터 셋을 변환한다.

```sql
SELECT CONVERT('2', UNSIGNED);
SELECT CONVERT('abc' USING utf8);
```

---

## JOIN

데이터베이스 내의 여러 테이블에서 가져온 레코드를 조합하여 하나의 테이블이나 결과 집합으로 표현해준다. 보통 SELECT 문과 자주 사용된다.

표준 SQL에서는 레코드를 조합하는 방식에 따라 다음과 같이 구분한다.

**INNER JOIN**

```sql
SELECT *
FROM Reservation
INNER JOIN Customer
ON Reservation.Name = Customer.Name;

SELECT *
FROM Reservation
JOIN Customer
ON Reservation.Name = Customer.Name;
```

INNER JOIN은 ON 절과 함께 사용되며, ON 절의 조건을 만족하는 데이터만을 가져온다. ON 절에서는 WHERE 절에서 사용할 수 있는 모든 조건을 사용할 수 있다.

표준 SQL과는 달리 MySQL에서는 JOIN, INNER JOIN, CROSS JOIN이 모두 같은 의미로 사용된다.

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/c595cc8a-3e15-4cf8-8451-7f48d59efd4b/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20200706%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200706T134118Z&X-Amz-Expires=86400&X-Amz-Signature=a4d15c2892190fe9877e2588ffd162b964cbc3d2887d31d1386b3500697bc435&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)


```sql
SELECT * 
FROM Reservation 
INNER JOIN Customer using(ID, Name)
WHERE Reservation.Name = Customer.Name;
```

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/3c5b6401-343c-469d-a132-cd4720c35d6f/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20200706%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200706T134139Z&X-Amz-Expires=86400&X-Amz-Signature=e38da8fa3d2382ce750d279330fc977c3b2ce30c9c2affb1d00f7edc7bb777a1&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

INNER JOIN의 경우에는 앞서 살펴본 표준 SQL 방식과는 별도로 MySQL에서만 사용할 수 있는 방식이 따로 존재한다.

```sql
SELECT *
FROM Reservation, Customer
WHERE Reservation.Name = Customer.Name;

# 별칭 사용
SELECT *
FROM Reservation AS r, Customer AS c
WHERE r.Name = c.Name;
```

**LEFT JOIN**

```sql
SELECT * 
FROM Customer 
LEFT JOIN Reservation 
ON Reservation.Name = Customer.Name;
```

LEFT JOIN은 첫 번째 테이블을 기준으로, 두 번째 테이블을 조합하는 JOIN이다. ON 절의 조건을 만족하지 않는 경우에는 첫 번째 테이블의 필드 값은 그대로 가져오고, 해당 레코드의 두 번째 테이블의 필드 값은 모두 NULL로 표시된다.

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/e0c07c64-953a-4e07-8dda-24f0f09f8e13/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20200706%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200706T134211Z&X-Amz-Expires=86400&X-Amz-Signature=0c9e6b9fd80767550438ab1a33721b07d566bb02f314697c008a5e0d35a8796f&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

**RIGHT JOIN**

```sql
SELECT * 
FROM Reservation 
RIGHT JOIN Customer 
ON Reservation.Name = Customer.Name;
```

RIGHT JOIN은 LEFT 조인과는 반대로 두 번째 테이블을 기준으로, 첫 번째 테이블을 조합하는 JOIN이다. ON 절의 조건을 만족하지 않는 경우에는 두 번째 테이블의 필드 값은 그대로 가져옵니다. 하지만 해당 레코드의 첫 번째 테이블의 필드 값은 모두 NULL로 표시된다.

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/de1b28a9-b32d-43b7-b960-56b97d77b3bc/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20200706%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200706T134247Z&X-Amz-Expires=86400&X-Amz-Signature=5f230037322086e652d9fce47cf824ac5bbc10145d1281aaf8cabb3c833ef009&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)


---

## UNION

**UNION**

```sql
SELECT Nam
FROM Reservation
UNION
SELECT Name
FROM Customer;
```

UNION은 여러 개의 SELECT 문의 결과를 하나의 테이블이나 결과 집합으로 표현할 때 사용한다. 이때 각각의 SELECT 문으로 선택된 필드의 개수와 타입은 모두 같아야 하며, 필드의 순서 또한 같아야 합니다. 중복된 레코드는 한 번만 표시한다.

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/728ccbe8-a3d4-4beb-b72b-359507eb3cad/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20200706%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200706T134323Z&X-Amz-Expires=86400&X-Amz-Signature=967574f3925054d57f65ae9d49e5f8e7f438c98f009d489d759c6e13d7b61d3c&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

**UNION ALL**

```sql
SELECT Name
FROM Reservation
UNION ALL 
SELECT Name
FROM Customer;
```

UNION은 `DISTINCT` 키워드를 따로 명시하지 않아도 기본적으로 중복되는 레코드를 제거한다. 중복되는 레코드까지 모두 출력하고 싶다면, `ALL` 키워드를 사용해야 한다.

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/48d9b08e-c204-4abc-b694-eb4f24bc85e1/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20200706%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200706T134342Z&X-Amz-Expires=86400&X-Amz-Signature=4b7795a3fa0e84e019c566da65522bdc6df7ced9eafea7bbb4081b5ee0c0e569&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

---

## INDEX

**인덱스란**

인덱스는 자주 사용되는 필드 값으로 만들어진 원본 테이블의 사본으로 일종의 목차를 생성해 **특정 칼럼 값을 가지고 있는 열 혹은 값을 빠르게 찾기 위해 사용한다.**

MySQL은 데이터를 검색할 때 첫 번째 필드부터 차례대로 테이블 전체를 검색하므로 테이블이 클수록 탐색하는 시간도 늘어나게 된다. 하지만 인덱스를 사용하면 테이블 전체를 읽지 않아도 되므로, 검색과 질의에 대한 처리가 빠르게 이루어진다.

인덱스는 사용자가 직접 접근할 수는 없으며, 검색과 질의에 대한 처리에서만 사용된다. 인덱스가 설정된 필드 값을 포함한 데이터의 삽입, 삭제, 수정 작업이 원본 테이블에서 이루어질 경우, 인덱스도 함께 수정되어야 한다. 따라서 인덱스는 수정보다는 검색이 자주 사용되는 테이블에서 사용하는 것이 좋다.

대부분의 인덱스 알고리즘은 B-tree 자료구조를 이용해 인덱스 구조를 관리한다.

**인덱스 생성**

```sql
CREATE INDEX NameIdx
On Reservation (Name);
```

`CREATE` 문을 사용해 인덱스를 생성한다.

**인덱스 정보 보기**

```sql
SHOW INDEX FROM Reservation;
```

**UNIQUE INDEX 생성**

```sql
CREATE UNIQUE INDEX IdIdx
On Reservation (ID);
```

UNIQUE INDEX는 중복 값을 허용하지 않는 인덱스다.

**인덱스 정렬**

```sql
CREATE INDEX NameDescIdx
On Reservation (Name DESC);
```

인덱스를 생성할 때 인덱스에 포함되는 필드의 정렬 방식을 설정할 수 있다.

**인덱스 추가**

ALTER 문을 사용하여 테이블에 인덱스를 추가할 수 있다.

- 기본 인덱스 추가

    ```sql
    ALTER TABLE Reservation
    ADD INDEX NameIdx (Name);
    ```

    기본 인덱스에서 필드의 값은 중복될 수 있고, NULL 값을 가질 수 있다.

- UNIQUE INDEX 추가

    ```sql
    ALTER TABLE Reservation
    ADD UNIQUE IdIdx (ID);
    ```

    UNIQUE 인덱스에서 필드의 값은 중복될 수 없고, NULL 값을 가질 수 있다.

- FULLTEXT INDEX 추가

    ```sql
    ALTER TABLE Reservation
    ADD FULLTEXT NameFtIdx (Name);
    ```

    FULLTEXT INDEX는 일반적인 인덱스와는 달리 매우 빠르게 테이블의 모든 텍스트 필드를 검색한다. 이 인덱스는 검색 엔진과 유사한 방법으로 자연어를 이용하여 데이터를 검색할 수 있도록 모든 데이터의 문자열 단어를 저장한다.

**ALTER 문으로 인덱스 삭제**

```sql
ALTER TABLE Reservation
DROP INDEX NameIdx;
```

**DROP 문으로 인덱스 삭제**

```sql
DROP INDEX IdIdx
ON Reservation;
```

참고

[[mysql] 인덱스 정리 및 팁](https://jojoldu.tistory.com/243)

---

## VIEW

**VIEW**

뷰(view)는 데이터베이스에 존재하는 일종의 가상 테이블을 의미한다. 뷰는 실제 테이블처럼 행과 열을 가지고 있지만, 실제로 데이터를 저장하고 있지는 않다. MySQL에서 뷰(view)는 다른 테이블이나 다른 뷰에 저장되어 있는 데이터를 보여주는 역할만을 수행한다. 즉, 뷰를 사용하면 여러 테이블이나 뷰를 하나의 테이블처럼 볼 수 있다.

**VIEW의 특징**

1. 특정 사용자에게 테이블 전체가 아닌 필요한 필드만을 보여줄 수 있다.
2. 복잡한 쿼리를 단순화해서 사용할 수 있다.
3. 쿼리를 재사용할 수 있다.
4. 한 번 정의된 뷰는 변경할 수 없다.
5. 삽입, 삭제, 갱신 작업에 많은 제한 사항을 가진다.
6. 자신만의 인덱스를 가질 수 없다.
7. 참조 테이블의 값이 변경되면 뷰의 값도 변경되고, 뷰의 값이 변경되면 참조 테이블의 값도 변경된다.

**뷰 생성**

```sql
CREATE VIEW MyView AS
SELECT Name, ReserveDate, ReserveDate - Curdate() AS Dday
FROM Reservation
WHERE Name = '홍길동';
```

뷰는 원본 테이블과 같은 이름을 가질 수 없다.

**뷰 대체**

```sql
CREATE OR REPLACE VIEW MyView AS
SELECT Name, RoomNum, Date - Curdate() AS Dday
FROM Reservation;
```

**뷰 수정**

```sql
ALTER VIEW MyView AS
SELECT ID, Name
FROM Reservation;
```

**뷰 삭제**

```sql
DROP VIEW MyView;
```

**뷰 확인**

```sql
SHOW TABLES;
```

테이블과 뷰 모두 표시된다.