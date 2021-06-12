# PL/SQL(Procedural Langauge for SQL) 기초-변수,상수, 조건문, 반복문

- SQL만으로 구현이 어렵거나 불가능한 작업을 수행하기 위해서 오라클에서 제공하는 프로그래밍 언어

🌹 PL/SQL 구성

1. DB 관련 특정 작업을 수행하는 명령어
2. 실행에 필요한 여러 요소를 정의하는 명령어
3. etc

💐 Block 블록 

- 위에서 언급된 명령어들을 모아둔, PL/SQL 프로그램의 기본 단위

🌺 PL/SQL 블록의 기본 형식

```sql
--선언부
DECLARE --선택
[실행에 사용될 변수,상수, 커서 등을 선언]
--실행부
BEGIN --필수
[조건문, 반복문, SELECT, DML, 함수 등을 정의]
--예외처리부
EXCEPTION --선택
[PL/SQL 실행 도중 발생하는 예외 상황을 해결하는 문장 기술]
END;
```

🌟 중요한 점은 위에서 지금은 설명을 위해 중간에 주석을 달아두었지만, 주석이 중간에 들어가면 오류가 발생될 수 있다! 따라서 이점을 주의하자!

PL/SQL 블록의 기본 형식은 위와 같이

1. 선언부 DECLARE
-실행에 사용될 변수, 상수, 커서 등을 선언
-선택사항
2. 실행부 BEGIN
-조건문, 반복문, SELECT, DML, 함수 등을 정의
-필수 사항
3. 예외처리부 EXCEPTION
-PL/SQL 실행 도중 발생하는 예외 상황을 해결하는 문장을 기술
-선택 사항

🌟 위의 기본 형식의 마무리에는 꼭 블록이 끝났음을 나타내는 

END;
/

를 적어주어야 한다!

🌟 PL/SQL 실행 결과를 화면에 출력하기 위해 미리 준비해주어야 하는 설정!

-SERVEROUTPUT 환경 변수 값을 ON으로 변경

SET SERVEROUTPUT ON;

-DBMS_OUTPUT.PUT_LINE('~'); == 자바에서의 System.out.println();
DBMS_OUTPUT.PUT('~')==자바에서의 System.out.print();

DBMS_OUTPUT.PUT_LINE('Hello World!');
DBMS_OUTPUT.PUT('Hello World!');

```sql
SET SERVEROUTPUT ON;

BEGIN
    DBMS_OUTPUT.PUT_LINE('Hello World!');
END;
/
```

위의 블록을 실행하면 스크립트 화면에 Hello World!가 출력되는 것을 확인해볼 수 있다!

🌟 PL/SQL 블록에서 주의해야할 사항 🌟

1. DECLARE, BEGIN, EXCEPTION 키워드에는 ;를 사용하지 않는다
2. PL/SQL 블록에서 각 부(선언부, 실행부, 예외처리부) 내부 문장에서는 끝에 ;를 꼭 붙이기
3. PL/SQL 블록 내부에서는 한 줄 주석(—)과 여러줄 주석(/**/)을 사용할 수 있지만, 중간에 삽입되는 경우 간혹 오류를 내뱉기도 한다!
4. PL/SQL 블록을 마무리하고 실행하기 위해서 마지막에는 END;/를 붙여서 사용한다

## 1. 변수와 상수

🌺 기본 변수 선언과 사용

변수이름 자료형:=값 또는 값이 도출되는 여러 표현식;

🌟 ||  기호는 DBMS_OUTPUT.PUT_LINE() 내부에서 여러 데이터 사이를 연결하여 출력될 때 사용된다!

(변수 사용 예시)

```sql
DECLARE
    V_EMPNO NUMBER(4) :=123;
    V_ENAME VARCHAR2(30);
BEGIN
    V_ENAME:='홍길동';
     DBMS_OUTPUT.PUT_LINE('직원번호:'||V_EMPNO||',직원이름: '||V_ENAME);
END;
/
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql_%EB%B3%80%EC%88%98%EC%82%AC%EC%9A%A9.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql_%EB%B3%80%EC%88%98%EC%82%AC%EC%9A%A9.PNG?raw=true)

🌺 기본 상수 선언과 사용

변수이름 CONSTANT 자료형:=값 또는 값을 도출하는 여러 표현식;

변수와의 차이점은 변수이름과 자료형 사이에 CONSTANT라는 키워드를 추가했다는 점이다

(상수 사용 예시)

아래와 같이,  상수는 CONSTANT 키워드를 붙임으로써 구별할 수 있다

뿐만아니라, 한 가지 더 눈여겨볼 점은 r-value(편의상 r-value로 명명. 자바나 c 언어 기준으로 이해하기 쉽도록!)에 표현식을 넣어도 계산되어 저장되었다는 점이다!

```sql
DECLARE
    V_CONST CONSTANT NUMBER(3) :=5*3;
BEGIN
    DBMS_OUTPUT.PUT_LINE('상수 V_CONST: '||V_CONST);
END;
/
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql_%EC%83%81%EC%88%98%EC%82%AC%EC%9A%A9.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql_%EC%83%81%EC%88%98%EC%82%AC%EC%9A%A9.PNG?raw=true)

🌺 변수의 기본값 지정하기

변수이름 자료형 DEFAULT 값 또는 값 도출되는 여러 표현식;

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql%20%EA%B8%B0%EB%B3%B8%EA%B0%92.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql%20%EA%B8%B0%EB%B3%B8%EA%B0%92.PNG?raw=true)

🌺 변수에 NULL 값 저장 막기

변수이름 자료형 NOT NULL:= :=값 또는 값이 도출되는 여러 표현식;

변수이름 자료형 NOT NULL DEFAULT 값 또는 값이 도출되는 여러 표현식;
▶️ 변수의 기본값으로도 지정할 수 있음!

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql%20NULL%EA%B0%92%20%EC%A0%80%EC%9E%A5%20%EB%A7%89%EA%B8%B0.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql%20NULL%EA%B0%92%20%EC%A0%80%EC%9E%A5%20%EB%A7%89%EA%B8%B0.PNG?raw=true)

🌟 PL/SQL 에서 식별자(변수 및 객체) 이름 짓기 규칙 🌟

1. 같은 블록 내에서 식별자는 고유해야 하고, 중복될 수 없음
2. 대소문자 구별 ❌
3. 테이블 이름 붙이는 규칙과 같은 규칙을 따름

🌺 변수의 자료형 - 스칼라, 복합, 참조, LOB(Large OBject)

1. 스칼라형: 내부 구성 요소가 없는 단일 값. C/JAVA 등의 프로그래밍 언어에서의 원시 자료형과 유사

[대표적인 스칼라형](PL%20SQL(Procedural%20Langauge%20for%20SQL)%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9-%E1%84%87%E1%85%A7%E1%86%AB%E1%84%89%E1%85%AE,%E1%84%89%E1%85%A1%E1%86%BC%207cfe2ac18bce414d88eb92de5486accb/%E1%84%83%E1%85%A2%E1%84%91%E1%85%AD%E1%84%8C%E1%85%A5%E1%86%A8%E1%84%8B%E1%85%B5%E1%86%AB%20%E1%84%89%E1%85%B3%E1%84%8F%E1%85%A1%E1%86%AF%E1%84%85%E1%85%A1%E1%84%92%E1%85%A7%E1%86%BC%203a21615b65c14346b0f196e31119b816.csv)

2. 참조형

A) 열을 참조하는 %TYPE

변수이름 테이블이름.열이름%TYPE;

B.) 행을 참조하는 %ROWTYPE;

변수이름 테이블이름%ROWTYPE;

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/%EC%B0%B8%EC%A1%B0%ED%98%95%20%EB%B3%80%EC%88%98%20%25TYPE.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/%EC%B0%B8%EC%A1%B0%ED%98%95%20%EB%B3%80%EC%88%98%20%25TYPE.PNG?raw=true)

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/%EC%B0%B8%EC%A1%B0%ED%98%95%20%EB%B3%80%EC%88%98%20%25ROWTYPE.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/%EC%B0%B8%EC%A1%B0%ED%98%95%20%EB%B3%80%EC%88%98%20%25ROWTYPE.PNG?raw=true)

%ROWTYPE에서 중요하게 살펴볼 점은, 

```sql
DECLARE
    **D_ROW** DEPT%ROWTYPE;
BEGIN
    SELECT DEPTNO, DNAME,LOC INTO **D_ROW**
    FROM  DEPT
    WHERE DEPTNO=40;
    
    DBMS_OUTPUT.PUT_LINE('DEPTNO: '||D_ROW.DEPTNO);
    DBMS_OUTPUT.PUT_LINE('DNAME: ' || D_ROW.DNAME);
    DBMS_OUTPUT.PUT_LINE('LOC: '||D_ROW.LOC);
END;
/
```

선언부에서 ROWTYPE으로 참조하여 행을 저장한 변수 D_ROW를 실행부에서 이용하여

사용할 데이터를 선택할 때 INTO를 사용하여 매칭시켜주었다는 점이다!

```sql
SELECT A,B,C INTO 참조형변수
FROM  테이블이름
[WHERE 조건식];
```

3. 복합형, LOB형

- 복합형 : 여러 종류 및 갯수의 데이터를 저장하기 위해서 사용자가 직접 정의하는 자료형
-컬렉션 : 한 가지 자료형의 데이터를 여러개 저장(테이블의 열과 유사)(자료형: TABLE)
-레코드 : 여러 종류 자료형의 데이터를 저장(테이블의 행과 유사)(자료형: RECORD)
- LOB형 : 대용량의 텍스트, 이미지, 동영상, 사운드 데이터 등 대용량 데이터를 저장하기 위한 자료형
-BLOB, CLOB 등

## 2. 조건문, 반복문

### 2.1. 조건문 - IF문, CASE 문

1. IF 조건문
- IF-THEN : 특정 조건 만족하는 경우 작업 수행
- IF-THEN-ELSE: 특정 조건을 만족하는 경우와 반대의 경우에 각각 지정한 작업 수행
- IF-THEN-ELSIF : 여러 조건에 따라 각각 지정한 작업 수행

[USAGE]

🌹 IF-THEN

```sql
IF 조건식 THEN
	수행할 명령어;
END IF; --IF문 종료
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql%20IF-THEN.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql%20IF-THEN.PNG?raw=true)

🌹 IF-THEN-ELSE

```sql
IF 조건식 THEN
	수행할 명령어;
ELSE
	수행할 명령어;
END IF;
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql%20IF-THEN-ELSE.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql%20IF-THEN-ELSE.PNG?raw=true)

🌹 IF-THEN-ELSIF

```sql
IF 조건식 THEN
	수행할 명령어;
ELSIF 조건식
	수행할 명령어;
ELSE
	수행할 명령어;
END IF;
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql%20IF-THEN-ELSIF.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql%20IF-THEN-ELSIF.PNG?raw=true)

2. CASE 조건문

- 자바에서의 SWITCH-CASE와 대응되는 '결과값에 따라 여러 수행 작업을 지정할 수 있는 조건문!'
- 자바에서의 경우와 유사하게 PL/SQL에서는 숫자, 문자열 에서 사용가능!
- 단순 CASE 문 : 비교 기준이 되는 조건의 값이 여러가지일 때, 해당 값만 명시하여 작업 수행
- 검색 CASE 문: 비교 기준을 명시하지 않고 각각의 WHEN절에서 조건식을 명시한 후 해당 조건 만족 시 수행할 작업을 명시

🌹 단순 CASE 문

```sql
CASE 비교기준
	WHEN 값1 THEN
		수행할 명령어;
	WHEN 값2 THEN
	  수행할 명령어;
...
	ELSE
	  수행할 명령어;
END CASE;
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql%20%EB%8B%A8%EC%88%9C%20CASE.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql%20%EB%8B%A8%EC%88%9C%20CASE.PNG?raw=true)

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql%20%EB%8B%A8%EC%88%9C%20CASE-%EB%AC%B8%EC%9E%90%EC%97%B4.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql%20%EB%8B%A8%EC%88%9C%20CASE-%EB%AC%B8%EC%9E%90%EC%97%B4.PNG?raw=true)

🌹 검색 CASE 문

```sql
CASE
	WHEN 값1 THEN
		수행할 명령어;
	WHEN 값2 THEN
	  수행할 명령어;
...
	ELSE
	  수행할 명령어;
END CASE;
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql%20%EA%B2%80%EC%83%89%20CASE.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql%20%EA%B2%80%EC%83%89%20CASE.PNG?raw=true)

### 2.2 반복(제어)문

🌹반복 제어문의 종류

- 기본 LOOP : 기본 반복문

```sql
LOOP
	반복 수행 작업;
END LOOP;
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql-%EA%B8%B0%EB%B3%B8%20LOOP.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql-%EA%B8%B0%EB%B3%B8%20LOOP.PNG?raw=true)

위에서처럼 일정 반복을 수행하는 경우, EXIT-WHEN으로 특정 조건 만족 시 현재 반복하는 주기를 건너뛰고 나갈 수 있게 하였다. 이 EXIT-WHEN은 IF와 EXIT를 이용해서 동일한 기능을 수행할 수 있게 된다

```sql
DECLARE
    V_NUM NUMBER:=6;
BEGIN
    LOOP
        DBMS_OUTPUT.PUT_LINE('현재 V_NUM: '||V_NUM);
        V_NUM:=V_NUM+1;
        IF V_NUM>10 THEN EXIT;
        END IF;
    END LOOP;
END;
/
```

- WHILE LOOP : 특정 조건식의 결과를 통해 반복 수행

```sql
WHILE 조건식 LOOP
	반복 수행 작업;
END LOOP;
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql-WHILE.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql-WHILE.PNG?raw=true)

- FOR LOOP : 반복 횟수를 정하여 반복 수행 
-i는 카운터
-1씩 증감하면서 실행됨

```sql
FOR i IN 시작값 .. 종료값 LOOP
	반복 수행 작업;
END LOOP;
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql-FOR%20%EB%A3%A8%ED%94%84.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql-FOR%20%EB%A3%A8%ED%94%84.PNG?raw=true)

위와 같이 자바에서처럼 중첩시켜서 구구단 출력 등을 수행할 수도 있다!
위의 경우는 바깥 루프에서 한번 고정하고, 내부 루프에서 반복을 수행한 후 다시 바깥 루프를 실행하는 순서로 진행됨으로써 위와 같은 형태로 구구단이 출력된다

-역순 반복 : 시작값과 종료값의 위치는 변하지 않고 오로지 REVERSE로 역순이 진행되는 것!

```sql
FOR i IN REVERSE 시작값..종료값 LOOP
	반복 수행 작업;
END LOOP;
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql-FOR%20%EB%A3%A8%ED%94%84%20REVERSE.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql-FOR%20%EB%A3%A8%ED%94%84%20REVERSE.PNG?raw=true)

위의 경우 시작값을 2, 종료값을 9로 지정하였는데 REVERSE 설정을 정해줌으로써 9부터 적용되는 것을 확인해볼 수 있다

아래의 경우는 FOR 루프에서 CONTINUE WHEN을 이용하여 홀수 케이스에는 아무런 작업을 수행하지 않고 다음 주기로 넘어가서 작업을 수행한다. 그 결과, 짝수가 출력되는 것을 확인할 수 있다

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql%20-%20FOR%20%EB%A3%A8%ED%94%84+CONTINUE%20WHEN.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/PLSQL/plsql%20-%20FOR%20%EB%A3%A8%ED%94%84+CONTINUE%20WHEN.PNG?raw=true)

- Cursor FOR LOOP : 커서를 활용한 반복 수행 - 이 부분에서는 생략할 예정

🌺 자바에서의 BREAK, CONTINUE절과 유사한 기능들

- EXIT: 수행 중인 반복 종료
- EXIT-WHEN: 반복 종료를 위한 조건식 지정 후 만족시 반복 종료
- CONTINUE : 수행중인 반복의 현재 주기를 건너뜀
- CONTINUE-WHEN: 특정 조건식을 지정하고 조건식을 만족하면 현재 반복 주기를 건너뜀