## NoSQL의 등장 배경

예전에 컴퓨팅 시스템은 기업 업무를 자동화하고 효율화하는 데 그 목적이 있었다. 그래서 기업의 복잡한 데이터를 저장하고 그 데이터 간의 관계를 정의하고 분석하는 데 최적화되어 있었다.

그러나 2000년대에 들어서면서 인터넷의 발전과 함께 한정된 규모의 복잡성이 높은 데이터에서 단순한 대량의 데이터로 넘어가기 시작했다. 이는 기존의 데이터 저장 시스템으로는 커버할 수 없는 여러 가지 한계를 야기했고 결국에는 새로운 형태의 데이터 저장 기술을 요구하게 되었다.

NoSQL은 Not Only SQL의 약자로 기존 RDBMS 형태의 관계형 데이터베이스가 아닌 다른 형태의 데이터 저장 기술을 의미한다. NoSQL이라고 해서 RDBMS 제품군(MS-SQL, Oracle, Sybase, MySQL) 등과 같이 공통된 형태의 데이터 저장 방식(테이블)과 접근 방식(SQL)을 갖는 제품군이 아니라 RDBMS와 다른 형태의 데이터 저장 구조를 총칭하며, 제품에 따라 각기 그 특성이 매우 달라서 NoSQL을 하나의 제품군으로 정의할 수는 없다.

---

## NoSQL의 특징

1. **NoSQL은 RDBMS와는 달리 데이터 간의 관계를 정의하지 않는다.**

    RDBMS가 데이터의 관계를 Foreign Key 등으로 정의하고 이를 이용해 Join 등의 관계형 연산을 한다고 하면, NoSQL은 데이터 간의 관계를 정의하지 않는다. 데이터 테이블은 그냥 하나의 테이블이며 각 테이블 간의 관계를 정의하지도 않고 일반적으로 테이블 간의 Join도 불가능하다.

2. **RDBMS에 비해 훨씬 더 대용량의 데이터를 저장할 수 있다.**

    RDBMS의 복잡도와 용량 한계를 극복하기 위한 목적으로 등장한 만큼, 페타바이트급의 대용량 데이터를 저장할 수 있다.

3. **분산형 구조**

    RDBMS와는 다르게 하나의 고성능 머신에 데이터를 저장하는 것이 아니라, 일반적인 서버 수십 대를 연결해 데이터를 저장 및 처리하는 구조를 갖는다. 즉 분산형 구조를 통해 데이터를 여러 대의 서버에 분산해 저장하고, 분산 시에 데이터를 상호 복제해 특정 서버에 장애가 발생했을 때에도 데이터 유실이나 서비스 중지가 없는 형태의 구조다.

4. **고정되지 않은 테이블 스키마**

    RDBMS와는 다르게 테이블의 스키마가 유동적이다.

---

## NoSQL의 분류

NoSQL은 데이터 저장 구조에 따라 다음과 같이 크게 세 가지 분류로 정의될 수 있다.

**Key/Value Store** - Oracle Coherence, Redis

**Wide Columnar Store** - HBase, Cassandra

**Document Key/Value Store** - MongoDB, CouchDB, Riak

이외에도 다양한 데이터 모델을 가진 NoSQL 제품들이 있다.

---

## NoSQL과 기존 RDBMS와의 차이

NoSQL이 DBMS라고 생각해서 RDBMS와 같은, 또는 떨어지지만 유사한 기능을 제공할 것이라고 생각하면 큰 오산이다. NoSQL은 데이터를 저장한다. 그리고 Key에 대한 Put/Get만 지원한다. RDBMS로 치면 다음과 같이만 딱 지원한다.

```
Put : Insert into TABLE VALUES(KEY,value1,value2,…,valuen)
Get : Select * from TABLE where KEY=”key”
```

물론 제품에 따라서 기능에 대한 지원 범위가 다르긴 하지만, 공통적으로 고민해야 하는 기능은 다음과 같다.

- Sorting (SQL의 Order By)
- Join (RDBMS에서 2개의 Table을 Foreign Key를 이용해 join)
- Grouping (SQL 문의 group by)
- Range Query (where key>“start” and key<“end”와 같이 일정 범위 내의 내용을 쿼리해오는 기능)
- Index (RDBMS에 Index를 지정해 select query 성능을 높이는 기능)



### Reference

[https://www.oss.kr/info_techtip/show/b29a00bb-351e-4834-95c4-debf8589f706](https://www.oss.kr/info_techtip/show/b29a00bb-351e-4834-95c4-debf8589f706)