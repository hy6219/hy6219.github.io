# 데이터를 추가, 수정, 삭제하는 데이터 조작어

⚠️ 테이블을 잘못 만들었거나, 지워야 할 경우 다음의 명령어를 사용

DROP TABLE 테이블이름;

⚠️ 실습 중 프로그램이 종료되었을 때

🌟commit 버튼을 누르면 됨!

---

잠깐!!

⭐기존 테이블과 동일한 열 구조, 데이터를 갖는 테이블 생성하기

CREATE TABLE 새로운 테이블의 이름
AS (SELECT * FROM 기존 테이블의 이름);

## INSERT 문

- 테이블에 데이터를 추가

```sql
INSERT INTO 테이블 이름[(열1, 열2,...,열n)]
VALUES (열1에 들어갈 데이터, 열2에 들어갈 데이터,...,열n에 들어갈 데이터);
```

📌 INSERT 문 오류는 

(1) 지정한 열 갯수 ≠ 각 열에 입력할 데이터 갯수 [ORA-00947 : 값의 수가 충분하지 않습니다] 

[ORA-00913 : 값의 수가 너무 많습니다]

(2) 자료형이 맞지 않는 경우 [ORA-01722: 수치가 부적합합니다]

(3) 열 길이를 초과하는 데이터를 지정하는 경우 [ORA-01438: 이 열에 대해 지정된 전체 자릿수보다 큰 값이 허용됩니다]

위와 같은 경우에 발생될 수 있다!

---

INSERT문은 위에서처럼 열을 명시할 수도, 생략해줄 수도 있는데

**생략하게 된다면 모든 열에 맞추어서 데이터를 넣어주어야** 한다!

```sql
INSERT INTO OT.TMP_PRODUCT
VALUES(1000, 'Galaxy A8 Series','blah',2000000,280,4);
```

🐨 그리고 NULL값을 추가하는 방법을 살펴보자! 🐨

#1. 첫번째는 명시적으로 , NULL이라고 적어주는 방법이다!

```sql
INSERT INTO OT.TMP_PRODUCT
VALUES(1000, 'Galaxy A8 Series',NULL,2000000,280,4);
```

⭐ 실무에서는 ''보다 NULL을 직접적으로 명시해주는 방식을 선호!

#2. 두번째는 암시적으로, NULL을 적는 대신 열 이름을 입력하지 않는 것!!

```sql
INSERT INTO OT.TMP_PRODUCT(PRODUCT_ID,PRODUCT_NAME,CATEGORY_ID)
VALUES(1500,'Samsung Galaxy Flip',4);
```

위와 같이 선언하면, 위의 컬럼 3개 외의 다른 컬럼들은 NULL값으로 채워질 것이다!

🐨 날짜 데이터 입력하기 🐨

단순하게 날짜 데이터를 직접적으로 입력하고 싶다면, YYYY/MM/DD 형식이나 YYYY-MM-DD형식을 이용하여 입력해줄 수 있다!

1) YYYY/MM/DD 형식으로 입력하기

```sql
INSERT INTO EMP_TEMP(EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)
VALUES (9999,'홍길동','PRESIDENT',NULL,'2001/01/01',5000,1000,10);
```

2) YYYY-MM-DD 형식으로 입력하기

```sql
INSERT INTO EMP_TEMP(EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)
VALUES (10000,'정길동','DIRECTOR',NULL,'2001-01-02',5000,1000,10);
```

⚠️ 단!! 일/월/년 순서로 바꾸어 기입하면, 오류가 발생하고 날짜 데이터 입력이 전혀 이루어지지 않는다! [ORA-01830: 날짜 형식의 지정에 불필요한 데이터가 포함되어 있습니다]

-만약, NLS_LANGUAGE를 변경한다면 해결할 수 있다!

-아니라면, **아래처럼 TO_DATE 함수를 사용하면 괜찮다!**

```sql
INSERT INTO EMP_TEMP(EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)
VALUES (9998,'김길동','DIRECTOR',NULL,TO_DATE('07/01/2001','DD/MM/YYYY'),5000,1000,10);
```

위와 같이 TO_DATE 함수를 이용해서 입력을 받으면, 해당 행이 YYYY/MM/DD로 맞추어져 있다면, 입력받은 정보를 자동으로 매칭시켜줄 수 있다!

3) SYSDATE를 이용한 현재 시점의 날짜 입력

```sql
INSERT INTO EMP_TEMP(EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)
VALUES (9898,'이길동','DIRECTOR',NULL,SYSDATE,5000,1000,10);
```

즉, 현재시간인 2021년 05월 12일 21:28:20이 값으로 저장될 것이다!

4) 서브쿼리를 사용하여 한 번에 여러 데이터 추가하기

```sql
INSERT INTO 테이블 이름[(열1, 열2,...,열n)]
서브쿼리;
```

-예시: EMP 테이블에서 SALGRADE 테이블을 참조하여 (겹쳐지는 속성 접점이 의미상인 접점만이 존재하여 non-equal join; 비등가 조인을 이용할 것!) 급여 등급이 1인 사원만을 EMP_TEMP(EMP 테이블을 복사해둔 테이블)에 추가

```sql
INSERT INTO EMP_TEMP(EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)
SELECT E.EMPNO,E.ENAME,E.JOB,E.MGR,E.HIREDATE,E.SAL,E.COMM,E.DEPTNO
FROM EMP E, SALGRADE S
WHERE (E.SAL BETWEEN S.LOSAL AND S.HISAL) AND S.GRADE =1;
```

위와 같은 경우, 먼저 서브쿼리로 가상의 테이블(=비등가조인된 테이블)에서 급여등급이 1인 경우를 택하고, 이를 추가하게 된다

-결과적으로는, 기존의 EMP_TEMP 테이블에 해당되는 사원들의 정보가 저장된다

⚠️ INSERT 작업을 서브쿼리를 이용하여 진행할 경우, 아래와 같은 사항을 조심해야 한다!

[1]VALUES 절 사용 ❌

[2]데이터가 추가되는 테이블의 열 갯수 혹은 자료형 == 서브쿼리의 열 갯수 혹은 자료형

---

## 테이블에 있는 데이터 수정하기 UPDATE

[USAGE]

```sql
UPDATE 변경할 테이블
SET 변경할 열1=데이터, 변경할 열2=데이터,...,변경할 열 N =데이터
[WHERE 데이터를 변경할 대상 행을 선별하기 위한 조건];
```

### 1.데이터 컬럼 전체 값을 일괄 수정하기

```sql
UPDATE 변경할 테이블
SET 컬럼명=일괄적용할 값;
```

- UPDATE 문은 단순히 어떤 조건에 맞는 경우에만 값을 수정할 수 있는 것이 아니다!
- 만약 어떤 열의 값을 공통적으로 바꾸고 싶을때(조건을 추가하여서(필요시))에도 사용할 수 있지만, 많이 사용하는 방법은 아니다!

```sql
UPDATE DEPT_TEMP 
SET LOC='SEOUL';
```

- 위의 경우에는 LOC열 전체에 대해서 SEOUL 값으로 저장될 것이다!(정확히는 갱신된 것이라 함이 의미표현상 맞다!)

### 2.데이터 일부분만 수정하기

```sql
UPDATE 테이블명
SET 컬렴명1=적용될 값, 컬럼명2=적용할값
WHERE 조건식;
```

```sql
UPDATE DEPT_TEMP
SET DNAME='DATABASE',
    LOC='SEOUL'
WHERE DEPTNO=40;
```

위와 같이 일부분만 변경하고 싶다면 위에서 사용된 쿼리문에 WHERE 조건식을 달아주면 된다!

### 3. 서브쿼리를 사용하여 데이터 수정하기

🧸 A. 여러 열을 한 번에 수정하는 경우

- 다중 행 다중 열 서브쿼리를 이용하는 것이 효율적!

(예) DEPT_TEMP 테이블에서 부서코드가 40인 부서에 대해서 부서명과 지역(DNAME, LOC)을

원본 테이블이었던 DEPT테이블의 부서코드 40의 DNAME, LOC으로 갱신시키기

```sql
UPDATE DEPT_TEMP
SET (DNAME,LOC)=(SELECT DNAME, LOC
				 FROM DEPT
				 WHERE DEPTNO = 40)
WHERE DEPTNO = 40;
```

- 이렇게 된다면, 위에서 부서코드 40인 부서의 부서명이 DATABASE, 위치가 SEOUL이었는데

       각각 OPERATIONS, BOSTON으로 변경된 것을 확인해볼 수 있다!

🧸 B. 열 하나하나를 수정하는 경우

- 단일 행 서브쿼리를 이용하여 수정할 컬럼 개수 1개 당 서브쿼리 1개씩 지정하여 설정하기!

(예시) 부서코드가 40인 부서의 부서명과 위치를 각각 부서 30, 부서 10의 정보로 갱신시키기

```sql
UPDATE DEPT_TEMP
SET DNAME=(SELECT DNAME
		   FROM   DEPT
		   WHERE DEPTNO=30),
    LOC  =(SELECT LOC
    	   FROM DEPT
    	   WHERE DEPTNO=10)
WHERE DEPTNO=40;
```

이렇게 되면, 저번에 수정했던 이력으로 OPERATIONS, BOSTON 이었던 정보가 SALES, NEW YORK으로 변경된 것을 확인해볼 수 있다!

🧸 C. WHERE 절에 서브쿼리를 사용하여 데이터를 수정하는 경우

- SELECT 절의 WHERE 절에서 아용되는 서브쿼리와 동일하게 사용 가능!

(예시) DEPT_TEMP 테이블에서 부서명이 RESEARCH인 곳의 위치를 SEOUL로 변경하기

```sql
UPDATE DEPT_TEMP 
SET LOC='SEOUL'
WHERE DNAME=(SELECT DNAME
			 FROM DEPT_TEMP
			 WHERE DNAME='RESEARCH');
```

⭐ UPDATE문이나 DELETE 문을 사용하기 전에는 꼭!! SELECT 질의를 통해서 WHERE 절의 조건식이 정확한지 확인하기!!

## DELETE 문

- 테이블에 있는 데이터를 삭제

[USAGE]

```sql
DELETE [FROM] [테이블명]
[WHERE 삭제할 대상 행을 선별하기 위한 조건식];
```

UPDATE 문에서처럼 WHERE절을 사용하지 않으면 데이터 전체가 모두 삭제될 것이고,

WHERE절을 사용하면 데이터 일부분만 삭제될 것이다!!

### 데이터 일부분만 삭제하기

(예시) EMP_TEMP2 테이블에서 직책이 MANAGER인 사원들만 삭제

```sql
DELETE FROM EMP_TEMP2
WHERE JOB='MANAGER';
```

이러한 질의를 수행한 후, MANAGER 직급인 사원이 존재하는 지 

```sql
SELECT EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO
FROM EMP_TEMP2
WHERE JOB='MANAGER';
```

위의 질의로 확인해보면, 아무런 데이터도 출력되지 않는다!

### 서브쿼리를 이용하여 데이터 삭제하기

(예시) EMP_TEMP2 테이블에서 급여 등급이 3등급, 즉 급여가 1401~2000 사이에 있는 30번 부서의 사원들만 삭제

▶️ 먼저 비동등 조인으로 SAL과 HISAL, LOSAL을 연결시켜주어 1401~2000범위를 맞춰주어야!

- SALGRADE 테이블

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/SALGRADE%20%ED%85%8C%EC%9D%B4%EB%B8%94.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/SALGRADE%20%ED%85%8C%EC%9D%B4%EB%B8%94.PNG?raw=true)

▶️ 그 후에는 서브쿼리의 조건으로 맞춰주기!

▶️ 사원들의 번호는 고유하므로, 서브쿼리의 일치 조건은 사원번호 일치로 확인하는 것이 효율적!

⭐ 사용할 서브쿼리를 정리해보면 아래와 같다!

```sql
SELECT EMPNO
FROM EMP_TEMP2 E, SALGRADE S
WHERE (E.SAL BETWEEN S.LOSAL AND S.HISAL) AND
			  S.GRADE =3 AND
			 DEPTNO = 30;
```

위의 경우 결과가 여러 행으로 나오기 때문에 메인 쿼리와 연결하여 사용하기 위해서는 IN 연산자를 사용해야 한다!

즉, 아래와 같이 사용할 수 있다!

```sql
DELETE FROM EMP_TEMP2
WHERE EMPNO IN (SELECT EMPNO
			   FROM EMP_TEMP2 E, SALGRADE S
			   WHERE (E.SAL BETWEEN S.LOSAL AND S.HISAL) AND
			   	      S.GRADE =3 AND
			   	      DEPTNO = 30);
```