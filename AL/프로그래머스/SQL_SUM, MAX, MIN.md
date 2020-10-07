### 최댓값 구하기
```mysql
SELECT MAX(DATETIME) AS 시간
FROM ANIMAL_INS
```

### 최솟값 구하기
```mysql
SELECT MIN(DATETIME) AS 시간
FROM ANIMAL_INS
```

### 동물 수 구하기
```mysql
SELECT COUNT(ANIMAL_ID) AS count
FROM ANIMAL_INS
```

### 중복 제거하기
```mysql
SELECT COUNT(DISTINCT NAME) AS NAMECOUNT
FROM ANIMAL_INS
```
MySQL에서
COUNT(*) - NULL 값을 포함해서 COUNT
COUNT(ColumnName) - NULL 값은 제외하고 COUNT, 하지만 빈 문자열은 포함