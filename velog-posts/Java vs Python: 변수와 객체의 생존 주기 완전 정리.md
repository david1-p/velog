<blockquote>
<p>변수는 이름표, 객체는 실제 물건이다.”
스코프(scope)와 객체(object)를 분리해서 이해하면 메모리 관리가 보인다.</p>
</blockquote>
<h2 id="목차">목차</h2>
<ol>
<li>왜 이 글을 썼나</li>
<li>Java: 변수별 수명 ― 정확히 어디서 틀리기 쉬운가?</li>
<li>Python(CPython): 객체가 사라지는 두 단계</li>
<li>실습 - CPython에서 객체 소멸 로그 찍어보기</li>
<li>한눈에 보기!</li>
<li>정리 &amp; 기억할 키워드</li>
</ol>
<hr />
<h3 id="왜-이-글을-썼나">왜 이 글을 썼나</h3>
<blockquote>
<p>“지역변수가 끝나면 객체도 끝난다?”
“불변 객체는 한 번 만들면 프로그램 종료까지 남는다?”</p>
</blockquote>
<p>프로그래밍 Q&amp;A에서 꾸준히 오해가 반복되는 주제다.
여기에 Java 와 Python(CPython) 을 나란히 두고 객체와 변수의 생존 주기를 정리했다.</p>
<hr />
<h3 id="java-변수별-수명--정확히-어디서-틀리기-쉬운가">Java: 변수별 수명 ― 정확히 어디서 틀리기 쉬운가?</h3>
<p>구분|저장 위치|살아 있는 조건|흔한 오해
|---|---|---|---|
클래스 변수(static field)|메서드영역(HotSpot 기준)|Class 객체가 ClassLoader에 탑재되는 동안(JVM 종료까지 남는 경우가 많음)|“메서드(콜스택)가 끝나면 사라진다”
인스턴스 변수|힙(Heap)|해당 객체에 강한 참조가 1개 이상 |“메서드 끝나면 사라진다”
지역 변수|스택(Stack Frame)|메서드 실행 중|여기서만 대체로 맞음 — 다만 참조 변수가 가리키던 객체는 다른 곳에서 참조 중이면 생존</p>
<blockquote>
<p>Point!
변수와 객체를 분리해서 보자.
스택에 있던 참조 변수가 사라져도, 힙에 있던 객체는 여전히 살아 있을 수 있다.</p>
</blockquote>
<hr />
<h3 id="pythoncpython-객체가-사라지는-두-단계">Python(CPython): 객체가 사라지는 두 단계</h3>
<p><span style="font-size: 20px ;"><strong>1. 참조 카운팅</strong></span></p>
<ul>
<li>모든 객체에 refcount 저장</li>
<li>refcount → 0 되는 즉시 메모리 반환</li>
</ul>
<p><span style="font-size: 20px ;"><strong>2. 세대별 순환-GC</strong></span></p>
<ul>
<li>서로 물고 있는 순환 참조는 refcount가 0이 되지 않음</li>
<li>주기적으로 세대(0, 1, 2)를 스캔해 순환 그래프 탐색 후 회수</li>
</ul>
<p>스코프 위치 |예시 | 언제 사라질까?|
|--|--|--|
지역 스코프 |함수 안 obj|함수 리턴 → 참조 0 → 즉시 free
인스턴스/클래스 속성 |self.attr, Cls.attr |속성을 지닌 객체가 참조되는 한
모듈 전역 |config = {...} | 모듈이 sys.modules에 남아 있는 한 (보통 인터프리터 종료까지)
인터닝 상수|0, &quot;&quot;, True|CPython 내부 캐시에 보존, 인터프리터 종료 시 해제</p>
<blockquote>
<p>“불변 == 영구”가 아니다!
작은 정수·짧은 문자열이 우연히 오래 남는 건 인터닝 캐시 덕분이다.
참조가 0이면 불변 객체라도 당연히 소멸한다.</p>
</blockquote>
<hr />
<h3 id="실습--cpython에서-객체-소멸-로그-찍어보기">실습 – CPython에서 객체 소멸 로그 찍어보기</h3>
<pre><code class="language-python">import gc

class Demo:
    def __init__(self, name): self.name = name
    def __del__(self):
        print(f&quot;[GC] {self.name} collected&quot;)

def make():            # 지역 스코프
    a = Demo(&quot;temp&quot;)   # refcount = 1
    b = a              # refcount = 2
    print(&quot;함수 끝…&quot;)

make()                 # a, b 모두 스코프 밖 → refcount 0
gc.collect()           # 순환 참조 없지만, 강제로 GC</code></pre>
<p>예상 출력</p>
<pre><code>함수 끝…
[GC] temp collected</code></pre><ul>
<li>a, b 참조가 동시에 사라져 refcount → 0</li>
<li>순환 참조가 있었다면 gc.collect() 호출 시점까지 남을 수 있다.</li>
</ul>
<hr />
<h3 id="한눈에-보기">한눈에 보기!</h3>
<p>항목|Java|Python (CPython)|
|--|--|--|
메모리 관리|Tracing GC (Mark-Sweep, G1 등)|참조 카운팅 + 순환-GC
“즉시 소멸” 가능?|❌  (GC 싸이클 기다림)|✅  (refcount 0)
순환 참조 처리|기본 GC가 해결|별도 순환-GC 패스
불변 객체 최적화|String 상수 풀, class constant pool|작은 int·짧은 str 인터닝
finalize / <strong>del</strong>|finalize() (Java 9 이후 deprecated)|<strong>del</strong>() (주의해서 사용)</p>
<hr />
<h3 id="정리--기억할-키워드">정리 &amp; 기억할 키워드</h3>
<ul>
<li>변수(scope)와 객체(lifetime)는 별개!</li>
<li>Java : ClassLoader + GC 가 객체 생명을 결정</li>
<li>CPython : refcount 0 → 즉시 free, 순환 참조는 generational GC</li>
<li>“불변 == 영구” 아님. 캐싱(Interning) 때문에 오래 살아 보일 뿐</li>
<li>객체 소멸이 필요하면 컨텍스트 매니저(Python), try-with-resources(Java) 등을 우선 고려</li>
</ul>