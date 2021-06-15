# [Database]Join

## 조인 vs 집합연산자

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/%EC%A7%91%ED%95%A9%EC%97%B0%EC%82%B0%EC%9E%90%EC%99%80%20%EC%A1%B0%EC%9D%B8%EC%9D%98%20%EC%B0%A8%EC%9D%B4%EC%A0%90.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/%EC%A7%91%ED%95%A9%EC%97%B0%EC%82%B0%EC%9E%90%EC%99%80%20%EC%A1%B0%EC%9D%B8%EC%9D%98%20%EC%B0%A8%EC%9D%B4%EC%A0%90.png?raw=true)

Join vs Set Operator

조인과 집합 연산자는 "2개 이상의 RESULT SET을 합친다는 점"에서 공통적인 특징을 갖는다

하지만 위의 그림과 같이

- 집합 연산자: result set을 세로로 합치는 것
- 조인            : result set을 가로로 합치는 것

에서 그 차이점이 존재한다

본격적인 조인 공부에 앞서서, 몇 가지 사항만 확인하고 넘어가자

- FROM 절에서는 테이블 형태(예: 뷰, 서브쿼리)면 여러 개가 와도 된다
- FROM 절에서 테이블 형태에는 별칭(ALIAS)를 아래와 같이 붙여줄 수 있다

```sql
FROM 테이블형태 ALIAS
```

- 여러개의 테이블 중에서 특정 테이블의 특정 컬럼을 선택하려면 아래와 같이 선택하자

```sql
테이블형태의 이름(상황에 따라서 별칭도 가능하다!).컬럼명
```

- 모든 열을 사용하고자 한다면, SELECT * 보다는 가능한, 출력할 순서에 맞춰서 모든 열을 기입해주자

🌟이유!

어떤 컬럼이 어떤 순서로 출력될 것인지 명확하게 알 수 없다

특정 열이 새로 생기거나 삭제되거나, 수정되었을 때, 그 변화를 감지하여 수정하는 것이 쉽지 않을 수 있기 때문

- 조인 조건이 WHERE 절 등을 통해 명시되지 않을 경우, 의미상 같은 컬럼 간의 데이터가 불일치하게 조합되어 부정확한 데이터가 출력될 수 있다

- 🌟**조인 조건을 정확하게 잘 적어주는 것은 매우 중요!** 그 이유는 데카르트 곱(Cartesian product) 으로 인한 모든 경우의 수가 조합되어 출력될 수 있기 때문!

```sql
SELECT *
FROM EMP, DEPT;
```

위 처럼 WHERE clause를 적어주지 않으면 데카르트 곱에 의해서

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/%EC%A1%B0%EC%9D%B8_%EB%8D%B0%EC%B9%B4%EB%A5%B4%ED%8A%B8%EA%B3%B1%ED%98%84%EC%83%81.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/%EC%A1%B0%EC%9D%B8_%EB%8D%B0%EC%B9%B4%EB%A5%B4%ED%8A%B8%EA%B3%B1%ED%98%84%EC%83%81.PNG?raw=true)

데카르트 곱에 의해 나타날 수 있는 부정확한 데이터 출력

위와 같이 동일한 의미를 갖는 DEPTNO 컬럼이 데이터 불일치를 보여주는 조합 결과를 나타낸다

위의 경우를 보통 조인이라고 할 수는 없지만, 이론상으로는 **"크로스 조인(Cross Join)"**이라고 한다

위를 아래와 같이 작성해도 동일한 결과를 얻을 수 있다

```sql
SELECT *
FROM EMP
CROSS JOIN DEPT;
```

## 조인의 종류

### 1. 등가 조인 Equi Join = 단순 조인 Simple Join = 내부 조인 Inner Join

: 두 테이블을 연결하되, 두 테이블 모두에 없는 값 범주는 제외하고 합치기 

: 일종의 교집합과 같다고 생각하면 쉬움! **➡️ OUTER JOIN과의 차이!!**

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/%EB%93%B1%EA%B0%80%EC%A1%B0%EC%9D%B8_%EB%82%B4%EB%B6%80%EC%A1%B0%EC%9D%B8_%EB%8B%A8%EC%88%9C%EC%A1%B0%EC%9D%B8.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/%EB%93%B1%EA%B0%80%EC%A1%B0%EC%9D%B8_%EB%82%B4%EB%B6%80%EC%A1%B0%EC%9D%B8_%EB%8B%A8%EC%88%9C%EC%A1%B0%EC%9D%B8.png?raw=true)

등가조인 내부조인 단순조인

- 주의할 점 1. 두 테이블의 컬럼명이 같은데 , 그 컬럼을 기준으로 조인하려고 할 때

➡️ 두 테이블에 별칭(alias)를 붙여서 활용하자!

```sql
SELECT E.EMPNO, E.ENAME, D.DEPTNO, D.DNAME,D.LOC
FROM EMP E, DEPT D
WHERE E.DEPTNO=D.DEPTNO
ORDER BY D.DEPTNO, E.EMPNO;
```

이처럼 별칭을 이용하면 어떤 테이블의 컬럼을 의미하는지 구분이 되어, 더이상 모호하지 않다!

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/%EB%93%B1%EA%B0%80%EC%A1%B0%EC%9D%B8_%EC%BB%AC%EB%9F%BC%EB%AA%85%EC%9D%B4%20%EA%B0%99%EC%9D%84%20%EB%95%8C.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/%EB%93%B1%EA%B0%80%EC%A1%B0%EC%9D%B8_%EC%BB%AC%EB%9F%BC%EB%AA%85%EC%9D%B4%20%EA%B0%99%EC%9D%84%20%EB%95%8C.PNG?raw=true)

테이블에 별칭을 붙이면 컬럼명이 같을 때 구별이 될 수 있다(조인시)

※ 참고로, WHERE절에서는 다른 절에서 붙인 별칭을 가져다 사용할 수 없다(예: WHERE 고기개수>30)

- 주의할 점 2. WHERE 절에 조건식을 추가하면, 출력 범위를 제한할 수 있다

복잡한 것은 아니고, **기존 조인 조건에 AND [출력하고 싶은 범위에 대한 조건식]** 을 기재해주면 된다! (아래 예제를 참고로 한다면, **SAL >=3000**)

```sql
SELECT E.EMPNO, E.ENAME, E.SAL, D.DEPTNO, D.DNAME,D.LOC
FROM EMP E, DEPT D
WHERE E.DEPTNO=D.DEPTNO AND
            SAL >=3000;
```

위의 예제처럼 실행해보게 된다면, 아래와 같이 급여가 3000만원 이상인 경우에 제한시킨 출력 결과를 확인해볼 수 있다

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/%5B%EB%93%B1%EA%B0%80%EC%A1%B0%EC%9D%B8%5D%EC%B6%94%EA%B0%80%EB%A1%9C%20%EC%A1%B0%EA%B1%B4%EC%8B%9D%20%EB%84%A3%EC%96%B4%EC%84%9C%20%EC%B6%9C%EB%A0%A5%20%EC%A0%9C%ED%95%9C.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/%5B%EB%93%B1%EA%B0%80%EC%A1%B0%EC%9D%B8%5D%EC%B6%94%EA%B0%80%EB%A1%9C%20%EC%A1%B0%EA%B1%B4%EC%8B%9D%20%EB%84%A3%EC%96%B4%EC%84%9C%20%EC%B6%9C%EB%A0%A5%20%EC%A0%9C%ED%95%9C.PNG?raw=true)

- 주의할 점 3. 데카르트 곱 현상을 방지하기 위해 필요한 조건식의 최소 갯수! ( ➡️주의할 점 1.과 관련!)

**조인 시, 데카르트 곱 현상을 방지하기 위해 필요한 조건식의 최소 갯수= 

조인할 테이블 갯수 - 1**

### 2. 비등가 조인 Non-equi Join

- 비등가 조인은 등가조인 외의 방식을 나타내는 말이라고 일컬어지는데, 쉽게 생각하면, WHERE 절이나 ANSI 문법으로 작성되지 않은 특이한 방식이라고 이해하면 좋다

- **다만, 등가조인과 다르게, 동일한 의미를 지닌 열을 기준으로 조인하지 않는다!**

```sql
--ORACLE--
SELECT *
FROM EMP E, SALGRADE S
WHERE E.SAL BETWEEN S.LOSAL AND S.HISAL;

--ANSI--(뒤에서 정리할 방법으로, SQL을 작성할 때에 오라클 방식과 ANSI 방식 각각 존재)
SELECT *
FROM EMP
JOIN SALGRADE ON(SAL BETWEEN LOSAL AND HISAL);

SELECT 컬럼명
FROM 테이블형태명(1)
JOIN 조인할 테이블명(2) ON(1에서 조인과 관련된 컬럼 BETWEEN 테이블2에서 범주1 컬럼 AND 
													테이블2에서 범주2 컬럼)
```

위는 급여 범위를 BETWEEN A AND B를 이용하여 지정하고, 이를 통해서 두 테이블 EMP와 SALGRADE를 **비등가조인**한 것이다

결과는 아래와 같이,  EMP 테이블에 SALGRADE 테이블이 연결된 것을 확인해볼 수 있다!

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/%EB%B9%84%EB%93%B1%EA%B0%80%EC%A1%B0%EC%9D%B8_BETWEEN%20A%20AND%20B%20%EB%A1%9C%20%EB%B2%94%EC%9C%84%EB%A5%BC%20%EC%A7%80%EC%A0%95%ED%95%98%EB%8A%94%20%EC%A1%B0%EA%B1%B4%EC%8B%9D%EC%9C%BC%EB%A1%9C%20%EC%A1%B0%EC%9D%B8%ED%95%98%EA%B8%B0.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/%EB%B9%84%EB%93%B1%EA%B0%80%EC%A1%B0%EC%9D%B8_BETWEEN%20A%20AND%20B%20%EB%A1%9C%20%EB%B2%94%EC%9C%84%EB%A5%BC%20%EC%A7%80%EC%A0%95%ED%95%98%EB%8A%94%20%EC%A1%B0%EA%B1%B4%EC%8B%9D%EC%9C%BC%EB%A1%9C%20%EC%A1%B0%EC%9D%B8%ED%95%98%EA%B8%B0.PNG?raw=true)

비등가조인

### 셀프 조인 Self Join = 자체 조인

- 셀프 조인은 쉽게 말해서, 한 테이블 내에서 다른 컬럼에서 하나의 컬럼을 참조해야 할 때, 연결해주는 개념이다! 마치 아래 그림처럼!

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/%EC%85%80%ED%94%84%EC%A1%B0%EC%9D%B8.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/%EC%85%80%ED%94%84%EC%A1%B0%EC%9D%B8.png?raw=true)

셀프 조인

위는 오라클에서 기본적으로 제공되는 SCOTT 계정의 EMP 테이블을 예로 들은 것이다!

EMP 테이블에는 MGR이라는 매니저의 사원번호 정보를 담은 컬럼이 존재한다. 이를 보면

매니저의 사번을 통해서 A 사원의 매니저가 과연 누구인지 , 그 매니저 정보를 한 줄에 확인해보고 싶다!

라는 생각이 든다!

이럴 경우가 바로 🌟**셀프 조인**🌟을 하기 딱 좋다!

개념적으로는 아래와 같다

```sql
--ORACLE--

SELECT A1.컬럼1, A1.참조될 컬럼명, A2.참조할 컬럼명
FROM 테이블 A1, 테이블 A2
WHERE A1.참조될 컬럼명=A2.참조할 컬럼명;

--ANSI--
SELECT A1.컬럼1, A1.참조될 컬럼명, A2.참조할 컬럼
FROM 테이블 A1
JOIN 테이블 A2 ON(A1.참조될 컬럼명=A2.참조할 컬럼명);
```

이를 SCOTT 계정에서 주어진 EMP 테이블을 이용해서 매니저에 대한 정보를 나타내보도록 하자!

```sql
--ORACLE--

SELECT          E.EMPNO AS 사번,
                E.ENAME AS 사원명,
                E.JOB AS 직급,
                E.MGR AS 매니저사번,
                M.ENAME AS 매니저이름,
                M.JOB AS 매니저직급
FROM EMP E, EMP M
WHERE E.MGR=M.EMPNO;

--ANSI--
SELECT          E.EMPNO AS 사번,
                E.ENAME AS 사원명,
                E.JOB AS 직급,
                E.MGR AS 매니저사번,
                M.ENAME AS 매니저이름,
                M.JOB AS 매니저직급
FROM EMP E
JOIN EMP M ON(E.MGR=M.EMPNO);
```

셀프조인을 하기 위해서는 매니저 사번을 나타내는 MGR과 전직원들의 사번을 나타내는 EMPNO를 연결해주어야 한다

다만, 오라클 방식과 ANSI 방식의 차이로 인해 위와 같이 두 가지 방식으로 작성할 수 있는 것이다.

결과는 두 방식 동일하게, 한 직원에 대한 매니저의 정보가 연결되어 출력되는 것을 확인해볼 수 있다

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/%EC%85%80%ED%94%84%EC%A1%B0%EC%9D%B8_%EC%98%A4%EB%9D%BC%ED%81%B4%EB%B0%A9%EC%8B%9D.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/%EC%85%80%ED%94%84%EC%A1%B0%EC%9D%B8_%EC%98%A4%EB%9D%BC%ED%81%B4%EB%B0%A9%EC%8B%9D.PNG?raw=true)

셀프조인-오라클 방식

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/%EC%85%80%ED%94%84%EC%A1%B0%EC%9D%B8_ANSI%EB%B0%A9%EC%8B%9D.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/%EC%85%80%ED%94%84%EC%A1%B0%EC%9D%B8_ANSI%EB%B0%A9%EC%8B%9D.PNG?raw=true)

셀프조인-ANSI 방식

- 셀프 조인(자체 조인)도 일치하는 데이터를 기준으로 조인하였기 때문에 일종의 **"등가조인"**으로 볼 수 있음

### Outer Join

- 둘 중 어느 한 쪽에 값이 없어도 포함시켜서 출력할 수 있는 방식

↔️ Inner Join

1. Left Outer Join

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/LEFT_OUTER_JOIN_%EA%B0%9C%EB%85%90.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/LEFT_OUTER_JOIN_%EA%B0%9C%EB%85%90.png?raw=true)

왼쪽 외부 조인 벤다이어그램

- 왼쪽 외부 조인은 FROM 절을 기준으로 두 개의 테이블이 있을 때,

     왼쪽에 있는 테이블의 내용은 무조건!! 포함되게 하고, 오른쪽 테이블 내용이 왼쪽 테이블에 

겹치지 않는다면 NULL 로 채운다!

오라클 방식과 ANSI 방식 각각을 적용한 예를 확인해보고 넘어가보자

```sql
--ORACLE--
SELECT EMPNO, ENAME, E.DEPTNO, D.DNAME
FROM EMP E, DEPT D
WHERE E.DEPTNO=D.DEPTNO(+);

--ANSI
SELECT EMPNO, ENAME, E.DEPTNO, D.DNAME
FROM EMP E
LEFT JOIN DEPT D ON(E.DEPTNO=D.DEPTNO);
```

위의 예로 보아, 각 방식은

```sql
--ORACLE--
SELECT 컬럼명
FROM 테이블1, 테이블2
WHERE 테이블1.컬럼=테이블2.컬럼(+); (무조건 포함시키는 테이블 기준의 반대편에 +)

--ANSI--
SELECT 컬럼명
FROM 테이블1
LEFT JOIN 테이블2 ON 또는 USING~;
```

임을 알 수 있다. 참고로 보통 OUTER JOIN은 LEFT OUTER JOIN을 의미하는 경우가 많다!

본론으로 돌아와서, 왼쪽은 무조건 포함시키기 때문에, 아래와 같은 결과를 확인해볼 수 있다

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/LEFT_OUTER_JOIN_%EC%99%BC%EC%AA%BD%20%EC%99%B8%EB%B6%80%20%EC%A1%B0%EC%9D%B8.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/LEFT_OUTER_JOIN_%EC%99%BC%EC%AA%BD%20%EC%99%B8%EB%B6%80%20%EC%A1%B0%EC%9D%B8.PNG?raw=true)

LEFT OUTER JOIN

2. RIGHT OUTER JOIN

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/RIGHT%20OUTER%20JOIN%20%EA%B0%9C%EB%85%90.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/RIGHT%20OUTER%20JOIN%20%EA%B0%9C%EB%85%90.png?raw=true)

Right Outer Join-오른쪽 외부 조인

- 오른쪽 외부 조인은 FROM 절을 기준으로 두 개의 테이블이 있을 때,

     오른쪽에 있는 테이블의 내용은 무조건!! 포함되게 하고, 왼쪽 테이블 내용이 오른쪽 테이블에 

겹치지 않는다면 NULL 로 채운다!

오라클 방식과 ANSI 방식 각각을 적용한 예를 확인해보고 넘어가보자

```sql
--ORACLE--
SELECT EMPNO, ENAME, E.DEPTNO, D.DNAME
FROM EMP E, DEPT D
WHERE E.DEPTNO(+)=D.DEPTNO;

--ANSI
SELECT EMPNO, ENAME, E.DEPTNO, D.DNAME
FROM EMP E
RIGHT JOIN DEPT D ON(E.DEPTNO=D.DEPTNO);
```

이를 간단하게 정리해보면 각 방식에 대한 RIGHT OUTER JOIN은 아래와 같이 사용됨을 확인해보고 넘어갈 수 있다

```sql
--ORACLE--
SELECT 컬럼명
FROM 테이블1, 테이블2
WHERE 테이블1.컬럼(+)=테이블2.컬럼;--기준이 되는 테이블의 반태편에 +

--ANSI--
SELECT 컬럼명
FROM 테이블1 RIGHT JOIN 테이블2 ON 또는 USING~;
```

결과를 추측해보자면, 왼쪽 테이블 내에 오른쪽 테이블의 범주값들이 존재하지 않는다면 우선 오른쪽 테이블 값들은 다 넣고 나머지를 NULL로 채울 것이라고 생각해볼 수 있다.

이제 결과를 확인해보면

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/RIGHT%20OUTER%20JOIN%20%EC%98%A4%EB%A5%B8%EC%AA%BD%20%EC%99%B8%EB%B6%80%20%EC%A1%B0%EC%9D%B8.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/RIGHT%20OUTER%20JOIN%20%EC%98%A4%EB%A5%B8%EC%AA%BD%20%EC%99%B8%EB%B6%80%20%EC%A1%B0%EC%9D%B8.PNG?raw=true)

RIGHT OUTER JOIN

와 같이 추측한 바와 같음을 확인해볼 수 있다

3. FULL OUTER JOIN; 전체 외부 조인

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/full%20outer%20join%20%EA%B0%9C%EB%85%90.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/full%20outer%20join%20%EA%B0%9C%EB%85%90.png?raw=true)

FULL OUTER JOIN

- FULL OUTER JOIN은 두 테이블의 합집합처럼 모두 포함한다고 생각하면 된다!
- 다만, ORACLE에서는 해당 방식이 지원되지 않지만, ANSI에서는

```sql
FROM 테이블명1 FULL JOIN 테이블명2 ON 또는 USING
```

형태로 사용이 가능하다

하나의 예제와 그 결과를 확인해보자

```sql
SELECT E.EMPNO, E.ENAME, E.JOB, E.MGR,
            E.HIREDATE, E.SAL, E.COMM,
            DEPTNO, D.DNAME, D.LOC
FROM EMP E FULL JOIN DEPT D USING(DEPTNO);
```

위와 같이 ANSI로 전체 외부 조인을 하게 되면, 왼쪽과 오른쪽 테이블 모두를 포함하는 결과가

아래와 같이 출력되게 된다

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/FULL%20OUTER%20JOIN%20_ANSI.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/FULL%20OUTER%20JOIN%20_ANSI.PNG?raw=true)

### (Interrupt!) SQL-99 표준문법("ANSI" 혹은 "ISO/ANSI" 방식)의 조인 표준문법("ANSI" 혹은 "ISO/ANSI" 방식)의 조인

- JOIN~ USING

: 두 테이블 간 컬럼명이 같고 동일 데이터를 담은 컬럼이 같은 하나의 컬럼이 존재할때,

Natural Join과 동일하게, 앞에 테이블의 별칭이 붙지 않고 사용이 가능한 형태의 문법

USING을 사용할 때에는 () 내부에 컬럼에 대해서 테이블명.컬럼 으로 접근하지 않도록!

(그렇지 않으면 ORA-25154: column part of USING clause cannot have qualifier 에러가 발생한다!)

🌟 **[Usage]**🌟

```sql
SELECT A.컬럼1, A.컬럼2, 컬럼3
FROM 테이블1 JOIN 테이블2 USING(컬럼3);
```

⚠️지금, SCOTT 계정의 EMP 테이블과 DEPT 테이블에는 ' DEPTNO'라는 "같은 데이터를 갖는 컬럼인데, 이름이 같은 컬럼"이 존재하므로 지금 상황에 잘 맞는 예제일 것 같네요!

```sql
SELECT E.EMPNO, E.ENAME, E.JOB, E.MGR,
            E.HIREDATE, E.SAL, E.COMM,
            DEPTNO, D.DNAME, D.LOC
FROM EMP E  JOIN DEPT D USING(DEPTNO)
ORDER BY DEPTNO, E.EMPNO;
```

이런 쿼리문을 수행하게 된다면, 두 테이블이 연결된 형태로 아래와 같은 결과를 확인해볼 수 있습니다! ON을 사용하여도 동일한 결과가 나올 것이므로, ON에서는 사용방법 및 쿼리만 연습해보도록 하죠!

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/ANSI-JOIN.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/ANSI-JOIN.PNG?raw=true)

- JOIN ~ ON : 가장 범용적으로 사용됨

🌟 **[Usage]**🌟

```sql
SELECT A.컬럼1, A.컬럼2, A.컬럼3, B.컬럼4
FROM 테이블1 JOIN 테이블2 ON(A.컬럼3=B.컬럼3.1);
```

위에서 실행해본 예제와 동일한 결과를 얻을 수 있는 쿼리를 작성해보겠습니다!

```sql
SELECT E.EMPNO, E.ENAME, E.JOB, E.MGR,
            E.HIREDATE, E.SAL, E.COMM,
            E.DEPTNO, D.DNAME, D.LOC
FROM EMP E  JOIN DEPT D ON(E.DEPTNO=D.DEPTNO)
ORDER BY DEPTNO, E.EMPNO;
```

- Natural Join : 컬럼명이 동일한 컬럼을 자동으로 찾아서 등가조인을 해주는 조인

🌟 **[Usage]**🌟

```sql
SELECT A.컬럼1, A.컬럼2, 컬럼3
FROM 테이블1 NATURAL JOIN 테이블2;--컬럼3이 자동으로 컬럼명과 데이터가 동일한 컬럼으로
--인식되어 등가조인됨
```

간단하게 동일한 테이블로 EMP와 DEPT 테이블을 이용해서 이번에는 JOIN~ON이나 JOIN~USING을 사용하지 않고 NATURAL JOIN을 FROM 절에 툭! 놓으면 자동으로 데이터 의미로도, 컬럼명으로도 동일한 컬럼을 자동으로 인식하여 등가조인해준다!

```sql
SELECT E.EMPNO, E.ENAME, E.JOB, E.MGR,
            E.HIREDATE, E.SAL, E.COMM,
            DEPTNO, D.DNAME, D.LOC
FROM EMP E NATURAL JOIN DEPT D
ORDER BY DEPTNO, E.EMPNO;
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/ANSI-%EC%9E%90%EC%97%B0%EC%A1%B0%EC%9D%B8.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Database/Oracle/Basic/Join/ANSI-%EC%9E%90%EC%97%B0%EC%A1%B0%EC%9D%B8.PNG?raw=true)

자연조인 Natural Join-ISO/ANSI
