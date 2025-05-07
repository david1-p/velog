<h3 id="간단-요약">간단 요약</h3>
<p>항목|INNER JOIN|서브쿼리 (SUBQUERY)
|-|-|-|
동작 방식|두 테이블을 행 기준으로 묶어서 데이터를 조합 | 쿼리 안에 또 다른 SELECT문을 포함시켜서 조건으로 사용
가독성 | 명시적으로 연결된 관계를 표현 |간단한 조건 필터링에 좋음
성능 | 조인이 더 빠른 경우 많음 (특히 인덱스 있을 때) | 단순하거나 1회성 필터링일 때 간편
결과 표현 | 여러 컬럼을 함께 조회 가능 |주로 하나의 컬럼값을 조건으로 비교</p>
<hr />
<h3 id="예제로-이해해보기">예제로 이해해보기</h3>
<p>Q) 부서 이름과 그 부서의 사원 이름을 함께 출력하라</p>
<h4 id="1-inner-join-예시">1. Inner Join 예시</h4>
<pre><code class="language-sql">SELECT e.ename, d.dname
FROM emp e
INNER JOIN dept d ON e.deptno = d.deptno;
-- emp와 dept를 조인해서 한 줄에 사원 + 부서 정보 출력 </code></pre>
<h4 id="2-subquery-예시단순-비교용-덜-일반적">2. Subquery 예시(단순 비교용, 덜 일반적)</h4>
<pre><code class="language-sql">SELECT ename,
       (SELECT dname FROM dept d WHERE d.deptno = e.deptno) AS dname
FROM emp e;

-- SELECT 안에 또 SELECT가 들어간 형태
-- 각각의 ename에 대해 부서 이름을 찾아서 넣는 것</code></pre>
<h4 id="3-그럼-언제-사용하나">3. 그럼 언제 사용하나?</h4>
<p>상황|추천 방식|이유
|-|:-:|-|
여러 테이블의 데이터를 같이 보고 싶을 때 | JOIN | 더 직관적이고 빠름
조건으로 특정 값만 필터링할 때 | 서브쿼리 |간단하고 가독성 좋음
집계(Aggregation) 결과를 조건으로 쓸 때 | 서브쿼리 |HAVING 전에 조건 필터링 가능
복잡한 계층 구조, 중첩 조건 | 서브쿼리 |유연함</p>
<hr />
<h3 id="예제를-통한-성능-차이">예제를 통한 성능 차이</h3>
<h4 id="테이블-구조의-예시">테이블 구조의 예시</h4>
<pre><code class="language-sql">-- 부서 테이블
CREATE TABLE dept (
  deptno NUMBER PRIMARY KEY,
  dname VARCHAR2(20),
  loc VARCHAR2(20)
);

-- 사원 테이블
CREATE TABLE emp (
  empno NUMBER PRIMARY KEY,
  ename VARCHAR2(20),
  deptno NUMBER,
  sal NUMBER,
  FOREIGN KEY (deptno) REFERENCES dept(deptno)
);</code></pre>
<h4 id="inner-join-쿼리-일반적으로-더-빠름">INNER JOIN 쿼리 (일반적으로 더 빠름)</h4>
<pre><code class="language-sql">SELECT e.ename, d.dname
FROM emp e
JOIN dept d ON e.deptno = d.deptno;</code></pre>
<p>•    조인을 통해 사원과 부서 정보를 한 번에 가져옴
•    deptno에 인덱스가 있다면 속도 빠름
•    DB 옵티마이저가 해시 조인, 중첩 루프 등 최적 전략을 자동 선택함</p>
<h4 id="subquery-느릴-수-있음">SUBQUERY (느릴 수 있음)</h4>
<pre><code class="language-sql">SELECT e.ename,
       (SELECT d.dname FROM dept d WHERE d.deptno = e.deptno) AS dname
FROM emp e;</code></pre>
<p>• emp의 각 행마다 dept 테이블을 서브쿼리로 다시 탐색
&nbsp;&nbsp;&nbsp;&nbsp;→ 즉, N개의 사원에 대해 N번의 dept 탐색
• 많을수록 성능 저하 발생 가능</p>