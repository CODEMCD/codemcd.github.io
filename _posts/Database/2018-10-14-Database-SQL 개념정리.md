---
title: "[Database]SQL 개념 정리"
excerpt: "데이터베이스 SQL 개념 정리 및 예제"
search: true
categories:
  - Study
tags:
  - Study_Database
toc: true
sidebar_main: true
---

# SQL(Structed Query Language)
## SQL에서 지원하는 기본 데이터 타입
- 정수(Integer): INT(4Byte), SMALLINT(2Byte)
- 실수(Float): FLOAT, REAL, DOUBLE PRECISION
- 형식화된 숫자: DEC(i, j) (i = 전체 자릿수, j = 소수부 자릿수)
- 고정길이 문자: CHAR(n) (n = 문자수)
- 가변길이 문자: VARCHAR(n) (n = 최대 문자 수)
- 고정길이 비트 열(Bit String): BIT(n)
- 가변길이 비트 열: VARBIT(n)
- 날짜: DATE
- 시간: TIME


## DDL(Data Define Language)
- DDL로 정의된 내용은 메타데이터가 되며, 시스템카탈로그에 저장한다.
- CREATE, ALTER, DROP 명령문이 존재한다.
### CREATE
- 스키마, 도메인 테이블, 뷰 인덱스를 정의한다.
#### CREATE SCHEMA
- 스키마를 정의하는 명령문
- 스키마는 하나의 응용(사용자)에 속하는 테이블과 기타 구성 요소등을 그룹짓기 위함이다.

```
CREATE SCHEMA 스키마명 AUTHORIZATION 사용자ID;
```

___예제___
- ID가 홍길동인 사용자의 스키마 '대학교'를 정의하는 SQL문

```
CREATE SCHEMA 대학교 AUTHORIZATION 홍길동;
```
#### CREATE DOMAIN
- 도메인을 정의하는 명령문
- 도메인은 하나의 속성이 취할 수 있는 동일한 타입의 원자값들의 집합이다.

```
CREATE DOMAIN 도메인명 데이터타입
  [DEFAULT 기본값]
  [CONSTRAINT 제약조건명 CHECK (범위값)];
```
- 데이터타입: SQL에서 지원하는 데이터 타입
- 기본값: 데이터를 입력하지 않았을 때 자동으로 입력되는 값

___예제___
- 성별을 '남' 또는 '여'와 같은 정해진 1개의 문자로 표현되는 도메인 SEX를 정의하는 SQL문

```
CREATE DOMAIN SEX CHAR(1)
  DEFAULT '남'
  CONSTRAINT VALID-SEX CHECK (VALUE IN('남', '여'));
```

#### CREATE TABLE
- 테이블을 정의하는 명령문

```
CREATE TABLE 테이블명
  (속성명 데이터타입[NOT NULL], ...
  [, PRIMARY KEY (기본키 속성명, ...)]
  [, UNIQUE (대채키 속성명, ...)]
  [, FOREIGN KEY (외래키 속성명, ...)
    REFERENCES 참조테이블(기본키 속성명, ...)]
    [ON DELETE 옵션]
    [ON UPDATE 옵션]
  [, CONSTRAINT 제약조건명][CHECK (조건식)]);
```

- 옵션 종류
  - ```NO ACTION```: 참조 테이블에 변화가 있어도 기본 테이블에는 아무런 조취를 취하지 않는다.
  - ```CASCADE```: 참조 테이블의 튜플이 삭제되거나 속성이 변경되면 그에 해당하는 기본 테이블의 모든 튜플이 그에 맞게 갱신된다.
  - ```SET NULL```: 참조 테이블에 변화가 생기면 기본 테이블의 관련 튜플의 속성 값을 NULL로 변경한다.
  - ```SET DEFAULT```: 참조 테이블에 변화가 있으면 기본 테이블의 관련 튜플의 속성 값을 기본값으로 변경한다.
- ```CONSTRAINT```: 제약  조건의 이름을 지정하며, 반드시 지정할 필요는 없다.

___예제___
- 이름, 학번, 전공, 성별, 생년월일로 구성된 <학생> 테이블을 정의하는 SQL문
  - 이름은 NULL이 올 수 없고, 학번은 기본키이다.
  - 전공은 <학과> 테이블의 학과코드를 참조하는 외래키로 사용된다.
  - <학과> 테이블에서 삭제가 일어나면 관련된 튜플들의 전공 값을 NULL로 만든다.
  - <학과> 테이블에서 학과코드가 변경되면 전공 값도 같은 값으로 변경된다.
  - 생년월일은 1980-01-01 이후의 데이터만 저장할 수 있다.
  - 제약 조건의 이름은 '생년월일제약'으로 한다.
  - 각 속성의 데이터형은 적당하게 지정한다. 단 성별은 도메인 'SEX'를 사용한다.

```
CREATE TABLE 학생
  (이름 VARCHAR(15) NOT NULL,
  학번 CHAR(8),
  전공 CHAR(5),
  성별 SEX,
  생년월일 DATE,
  PRIMARY KEY(학번),
  FOREIGN KEY(전공) REFERENCES 학과(학과코드)
    ON DELETE SET NULL
    ON UPDATE CASCADE,
  CONSTRAINT 생년월일제약 CHECK(생년월일 >= '1980-01-01'));
```

#### CREATE VIEW
- 뷰를 정의하는 명령문
- 뷰(View)는 하나 이상의 기본 테이블로부터 유도되는 이름을 갖는 가상 테이블이다.

```
CREATE VIEW 뷰명[(속성명[, 속성명, ...])]
AS SELECT문;
```

- ```SELECT```문을 서브 쿼리로 사용하여 SELECT문의 결과로서 뷰를 생성한다.

___예제___
- <고객> 테이블에서 주소가 '안산시'인 고객들의 성명과 전화번호를 '안산고객'이라는 뷰로 정의한다.

```
CREATE VIEW 안산고객(성명, 전화번호)
AS SELECT 성명, 전화번호
FROM 고객
WHERE 주소='안산시';
```

#### CREATE INDEX
- 인덱스를 정의하는 명령문
- 인덱스는 검색을 빠르게 하기 위해 만든 보조적인 데이터 구조이다.

```
CREATE [UNIQUE] INDEX <인덱스명>
  ON 테이블명({속성명 [ASC | DESC] [,속성명 [ASC | DESC]]})
  [CLUSTER];
```

- ```UNIQUE```
  - 사용된 경우: 중복 값이 없는 속성으로 인덱스를 생성한다.
  - 생략된 경우: 중복 값을 허용하는 속성으로 인덱스를 생성한다.
- 정렬 여부 지정
  - ```ASC```: 오름차순 정렬
  - ```DESC```: 내림차순 정렬
  - 생략된 경우: 오름차순으로 정렬된다.
- ```CLUSTER```: 지정된 키에 따라 튜플들으르 그룹으로 지정하기 위해 사용된다.

___예시___
- <고객> 테이블에서 UNIQUE한 특성을 갖는 고객번호 속성에 대해 내림차순으로 정렬하여 '고객번호_idx'라는 이름으로 인덱스를 정의한다.

```
CREATE UNIQUE INDEX 고객번호_idx
  ON 고객(고객번호 DESC);
```


### ALTER
#### ALTER TABLE
- 테이블에 대한 정의를 변경하는 명령문

```
ALTER TABLE 테이블명 ADD 속성명 데이터타입 [DEFAULT '기본값'];
ALTER TABLE 테이블명 ALTER 속성명 [SET DEFAULT '기본값'];
ALTER TABLE 테이블명 DROP COLUMN 속성명 [CASCADE];
```

- ```ADD```: 새로운 속성을 추가한다.
- ```ALTER```:  속성의 기본값을 변경한다.
- ```DROP COLUMN```: 속성을 제거한다.

___예시___
- <학생> 테이블에 최대 3문자로 구성되는 학년 속성을 추가하는 SQL문

```
ALTER TABLE 학생 ADD 학년 VARCHAR(3);
```

### DROP
- 스키마, 도메인, 테이블, 뷰, 인덱스, 트리거를 제거하는 명령문

```
DROP SCHEMA 스키마명 [CASCADE | RESTRICT];
DROP DOMAIN 도메인명 [CASCADE | RESTRICT];
DROP TABLE 테이블명 [CASCADE | RESTRICT];
DROP VIEW 뷰명 [CASCADE | RESTRICT];
DROP INDEX 인덱스명 [CASCADE | RESTRICT];
DROP TRIGGER 트리거명 [CASCADE | RESTRICT];
DROP CONSTRAINT 제약조건명;
```

- ```CASCADE```: 제거할 개체를 참조하는 다른 모든 개체를 함께 제거한다. 즉, 주 테이블의 데이터 제거 시, 각각의 외래키와 관계를 맺고 있는 모든 데이터를 함께 제거한다.(참조 무결성 제약 조건을 위함)
- ```RESTRICT```: 다른 개체가 제거할 개체를 참조중일 경우 제거가 취소된다.


## SELECT
- 테이블을 구성하는 튜플들 중에서 전체 또는 조건을 만족하는 튜플을 검색하여 주기억장치에 임시 테이블로 구성하는 명령문이다.

```
SELECT [PREDICATE] [테이블명.]속성명[ AS 별칭][, [테이블명.]속성명, ...]
FROM 테이블명[, 테이블명, ...]
[WHERE 조건]
[GROUP BY 속성명[, 속성명, ...]]
[HAVING 조건]
[ORDER BY 속성명 [ASC | DESC][, 속성명 [ASC | DESC], ...]];
```

- ```PREDICATE```: 검색할 튜플을 제한할 목적으로 사용되는 조건이다.
  - ```ALL```: 모든 튜플들을 검삭할 때 사용되며, 기본값이다.
  - ```DISTINCT```: 중복된 튜플들을 제거할 때 사용된다.
  - ```DISTINCTROW```: 중복된 튜플을 제거하지만, 선택된 속성의 값이 아닌 튜플의 전체 값을 대상으로 사용된다.
- ```AS```: 속성 및 연산의 이름을 다른 제목으로 표시하기 위해 사용된다.
- FROM절: 검색할 데이터가 들어 있는 테이블명을 기술한다.
- WHERE절: 검색할 조건을 기술한다.
- GROUP BY절: 특정 속성을 기준으로 그룹화하여 검색할 때 사용된다. 일반적으로 그룹 함수와 함께 사용한다.
- HAVING절: 그룹에 대한 조건을 기술한다.
- ORDER BY절: 특정 속성을 기분으로 정렬하여 검색할 때 사용된다.
### 하위 질의
- <여가 활동> 테이블에서 취미가 '나이트댄스'인 사원에 대해 <사원> 테이블에서 해당 사원의 이름과 주소를 검색하는 SQL문

```
SELECT 이름, 주소
FROM 사원
WHERE 이름 IN(SELECT 이름 FROM 여가활동 WHERE 취미='나이트댄스');
```

### 복수 테이블 검색
- 경력이 10년 이상인 사원의 이름, 부서, 취미, 경력을 검색하는 SQL문

```
SELECT 사원.이름, 사원.부서, 여가활동.취미, 여가활동.경력
FROM 사원, 여가활동
WHERE 여가활동.경력 >= 10 AND 사원.이름 = 여가활동.이름;
```

### 통합(UNION) 질의
- UNION은 일반 집합 이론에서의 합집합과 같다.
- 두 테이블을 합치면 두 테이블에 모두 속해 있는 튜플은 중복이 제거되어 1개만 표시된다.
- 사원들의 명단이 <임원> 테이블과 <직원> 테이블에 저장되어 있다. 두 테이블을 통합하는 SQL문(중복 제거)

```
SELECT *
FROM 임원
UNION SELECT * 직원;
```


## JOIN
- 조인은 2개의 테이블에 대해 연관된 튜플들을 결합하여, 하나의 새로운 릴레이션을 반환한다.
- 조인은 크게 INNER JOIN과 OUTER JOIN으로 구분된다.
### INNER JOIN
- INNER JOIN은 일반적으로 EQUI JOIN과 NON-EQUI JOIN으로 구분된다.
#### EQUI JOIN
- EQUI JOIN은 JOIN 대상 테이블에서 공통 속성을 기준으로 '='(equal)비교에 의해 같은 값을 가지는 행을 연결하여 결과를 생성하는 방법이다.
- 중복된 속성을 제거하여 같은 속성을 한 번만 표기하는 방법을 NATURAL JOIN이라 한다.
- WHERE절을 이용한 EQUI JOIN

```
SELECT [테이블명1.]속성명, [테이블명2.]속성명, ...
FROM 테이블명1, 테이블명2, ...
WHERE 테이블명1.속성명 = 테이블명2.속성명;
```

- NATURAL JOIN을 이용한 EQUI JOIN

```
SELECT [테이블명1.]속성명, [테이블명2.]속성명, ...
FROM 테이블명1 NATURAL JOIN 테이블명2;
```

- JOIN ~ USING절을 이용한 EQUI JOIN

```
SELECT [테이블명1.]속성명, [테이블명2.]속성명, ...
FROM 테이블명1 JOIN 테이블명2 USING(속성명);
```

___예제___
- <학생> 테이블과 <학과> 테이블에서 학과코드 값이 같은 튜플을 JOIN하여 학번, 이름, 학과코드, 학과명을 출력하는 SQL문

```
SELECT 학번, 이름, 학생.학과코드, 학과명
FROM 학생, 학과
WHERE 학생.학과코드 = 학과.학과코드;

SELECT 학번, 이름, 학생.학과코드, 학과명
FROM 학생 NATURAL JOIN 학과;

SELECT 학번, 이름, 학생.학과코드, 학과명
FROM 학생 JOIN 학과 USING(학과코드);
```

#### NON-EQUI JOIN
- NON-EQUI JOIN은 JOIN조건에 '=' 조건이 아닌 나머지 비교 연산자인 '>', '<', '<=', '>=' 연산자를 사용하는 방법이다.

___예제___
- <학생> 테이블과 <성적등급> 테이블을 JOIN하여 각 학생의 학번, 이름, 성적, 등급을 출력하는 SQL문

```
SELECT 학번, 이름, 성적, 등급
FROM 학생, 성적등급
WHERE 학생.성적 BETWEEN 성적등급.최저 AND 성적등급.최고;
```

### OUTER JOIN
- OUTER JOIN은 릴레이션에서 JOIN조건에 만족하지 않는 튜플도 결과로 출력하기 위한 JOIN 방법이다.
#### LEFT OUTER JOIN
- INNER JOIN의 결과를 구한 후, 우측 항 릴레이션과 맞지 않는 좌측 항 릴레이션의 튜플에 NULL값을 대입한다.
- 좌측 릴레이션은 모두 출력된다.

```
SELECT [테이블명1.]속성명, [테이블명2.]속성명, ...
FROM 테이블명1 LEFT OUTER JOIN 테이블명2
ON 테이블명1.속성명 = 테이블명2.속성명;

SELECT [테이블명1.]속성명, [테이블명2.]속성명, ...
FROM 테이블명1, 테이블명2
WHERE 테이블명1.속성명 = 테이블명2.속성명(+);
```

#### RIGHT OUTER JOIN
- INNER JOIN의 결과를 구한 후, 좌측 항 릴레이션과 맞지 않는 우측 항 릴레이션의 튜플에 NULL값을 대입한다.
- 우측 릴레이션은 모두 출력된다.

```
SELECT [테이블명1.]속성명, [테이블명2.]속성명, ...
FROM 테이블명1 RIGHT OUTER JOIN 테이블명2
ON 테이블명1.속성명 = 테이블명2.속성명;

SELECT [테이블명1.]속성명, [테이블명2.]속성명, ...
FROM 테이블명1, 테이블명2
WHERE 테이블명1.속성명(+) = 테이블명2.속성명;
```

#### FULL OUTER JOIN
- LEFT OUTER JOIN과 RIGHT OUTER JOIN을 합쳐 놓은 것이다.
- 양쪽 릴레이션이 모두 출력된다.

```
SELECT [테이블명1.]속성명, [테이블명2.]속성명, ...
FROM 테이블명1 FULL OUTER JOIN 테이블명2
ON 테이블명1.속성명 = 테이블명2.속성명;
```

### SELF JOIN
- SELF JOIN은 같은 테이블에서 2개의 속성을 연결하여 EQUI JOIN을 하는 방법이다.

```
SELECT [별칭1.]속성명, [별칭2.]속성명, ...
FROM 테이블명1 [AS] 별칭1 JOIN 테이블명1 [AS] 별칭2
ON 별칭1.속성명 = 별칭2.속성명;

SELECT [별칭1.]속성명, [별칭2.]속성명, ...
FROM 테이블명1 [AS] 별칭1, 테이블명1 [AS] 별칭2
WHERE 별칭1.속성명 = 별칭2.속성명;
```


## DML(Date Manipulation Language)
- DML은 데이터베이스 사용자가 응용 프로그램이나 질의어를 통해 저장된 데이터를 실질적으로 관리하는데 사용되는 언어이다.
- DML은 데이터베이스 사용자와 데이터베이스 관리 시스템 간의 인터페이스를 제공한다.
- INSERT, DELETE, UPDATE 명령문이 있다.
### INSERT문
- 테이블에 새로운 튜플을 삽입할 때 사용하는 명령문이다.

```
INSERT INTO 테이블명[(속성명1, 속성명2, ...)]
VALUES (데이터1, 데이터2, ...);
```

___예시___
- <사원> 테이블에 이름이 "이순신"이고, 부서가 "기획"인 사원을 삽입하는 SQL문

```
INSERT INTO 사원(이름, 부서) VALUES ('이순신', '기획');
```

- <사원> 테이블에 있는 편집 부서의 모든 튜플을 <편집부원(이름, 생일, 주소, 기본급)> 테이블에 삽입하는 SQL문

```
INSERT INTO 편집부원(이름, 생일, 주소, 기본급)
SELECT 이름, 생일, 주소, 기본급 FROM 사원 WHERE 부서='편집';
```

### DELETE문
- 테이블에 있는 튜플들 중에서 특정 튜플을 삭제할 때 사용하는 명령문이다.

```
DELETE FROM 테이블명 WHERE 조건;
```

___예시___
- <사원> 테이블에서 이름이 "임꺽정"인 튜플을 삭제하는 SQL문

```
DELETE FROM 사원 WHERE 이름='임꺽정';
```

- <사원> 테이블의 모든 튜플을 삭제하는 SQL문

```
DELETE FROM 사원;
```

### UPDATE문
- 테이블에 있는 튜플들 중에서 특정 튜플의 내용을 갱신할 때 사용하는 명령문이다.

```
UPDATE 테이블명
SET 속성명=데이터[, 속성명=데이터, ...]
WHERE 조건;
```

___예시___
- <사원> 테이블에서 홍길동의 주소를 "퇴계동"으로 갱신하는 SQL문

```
UPDATE 사원 SET 주소='퇴계동' WHERE 이름='홍길동';
```

- <사원> 테이블에서 황진이의 부서를 "기획"으로 변경하고 기본급을 5 인상하는 SQL문

```
UPDATE 사원 SET 부서='기획', 기본급=기본급+5 WHERE 이름='황진이';
```


## DCL(Data Control Language)
- DCL은 데이터의 보안, 무결성, 회복, 병행 제어 등을 정의하는데 사용하는 언어이다.
- DCL은 데이터베이스 관리자(DBA)가 데이터 관리를 목적으로 사용한다.
- COMMIT, ROLLBACK, GRANT, REVOKE 명령문이 있다.
### COMMIT
- 트랜잭션의 모든 변경 내용들을 영구적으로 데이터베이스에 반영하는 명령어이다.
### ROLLBACK
- 변경된 모든 내용들을 취소하고 데이터베이스를 이전 상태로 되돌리는 명령어이다.
- 트랜잭션의 일부를 성공적으로 끝내지 못하면 데이터베이스가 비일관성인 상태를 가질 수 있으므로 모든 내용들을 취소해야한다.
### GRANT
- 데이터베이스 관리자가 데이터베이스 사용자에게 권한을 부여하기 위한 명령어이다.
- 사용자등급 지정

```
GRANT 사용자등급 TO 사용자ID리스트[IDENTIFIED BY 암호];
```

- 사용자 등급: DBA(데이터베이스 관리자), RESOURCE(데이터베이스 및 테이블 생성 가능자), CONNECT(단순 사용자)
- 테이블 및 속성에 대한 권한 부여

```
GRANT 권한리스트 ON 개체 TO 사용자 [WITH GRANT OPTION];
```

### REVOKE
- 권한 취소를 위한 명령어이다.
- 사용자등급 해제

```
REVOKE 사용자등급 FROM 사용자ID리스트;
```

- 테이블 및 속성에 대한 권한 취소

```
REVOKE [GRANT OPTION FOR] 권한리스트 ON 개채 FROM 사용자 [CASCADE];
```
