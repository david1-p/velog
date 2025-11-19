<h1 id="spring-transactional의-동작-원리와-실무-필독-주의사항-a-to-z">[Spring] @Transactional의 동작 원리와 실무 필독 주의사항 (A to Z)</h1>
<p>스프링 백엔드 개발을 하다 보면 가장 많이 사용하는 어노테이션 중 하나가 바로 <code>@Transactional</code>입니다. 이를 <strong>선언적 트랜잭션 관리(Declarative Transaction Management)</strong>라고 부릅니다.</p>
<p>우리가 비즈니스 로직에만 집중할 수 있도록, 복잡한 트랜잭션 시작과 종료 로직을 마법처럼 처리해주는 이 기능은 내부적으로 어떻게 동작할까요? 그리고 실무에서 흔히 겪는 &quot;어? 왜 롤백이 안 되지?&quot; 하는 상황은 왜 발생하는 걸까요?</p>
<p>오늘은 스프링 트랜잭션의 동작 흐름과 핵심 주의사항을 정리해 보겠습니다.</p>
<hr />
<h2 id="1-트랜잭션-aop-동작-원리-3가지-핵심-요소">1. 트랜잭션 AOP 동작 원리 (3가지 핵심 요소)</h2>
<p>@Transactional의 동작 과정을 이해하려면 다음 3가지 요소를 알아야 합니다.</p>
<ol>
<li><strong>트랜잭션 AOP 프록시 (Transaction AOP Proxy)</strong></li>
<li><strong>트랜잭션 매니저 (Transaction Manager)</strong></li>
<li><strong>트랜잭션 동기화 매니저 (Transaction Synchronization Manager)</strong></li>
</ol>
<h3 id="전체-동작-흐름">전체 동작 흐름</h3>
<p>클라이언트가 <code>@Transactional</code>이 붙은 서비스 메서드를 호출했을 때의 흐름은 다음과 같습니다.</p>
<ol>
<li><strong>요청 진입 (Proxy):</strong> 클라이언트가 메서드를 호출하면, 실제 서비스 객체 대신 <strong>트랜잭션 AOP 프록시</strong>가 먼저 호출을 가로챕니다.</li>
<li><strong>트랜잭션 시작 (Manager):</strong> 프록시는 <strong>트랜잭션 매니저</strong>에게 트랜잭션 시작을 요청합니다.</li>
<li><strong>커넥션 획득:</strong> 트랜잭션 매니저는 <code>DataSource</code>를 통해 DB 커넥션을 생성(획득)하고, <code>auto-commit</code>을 <code>false</code>로 설정하여 트랜잭션을 시작합니다.</li>
<li><strong>커넥션 보관 (Sync Manager):</strong> 트랜잭션 매니저는 시작된 커넥션을 <strong>트랜잭션 동기화 매니저</strong>에 보관합니다. (이때 <code>ThreadLocal</code>을 사용하여 멀티스레드 환경에서도 안전하게 보관됩니다.)</li>
<li><strong>비즈니스 로직 실행:</strong> 프록시는 실제 서비스 로직을 호출합니다. 내부에서 리포지토리(DAO)가 실행될 때, <strong>트랜잭션 동기화 매니저에 보관된 커넥션</strong>을 꺼내서 DB 작업을 수행합니다.</li>
<li><strong>트랜잭션 종료:</strong> 비즈니스 로직이 끝나면 프록시로 제어권이 돌아옵니다.<ul>
<li><strong>성공 시:</strong> 트랜잭션 매니저에게 <strong>커밋(Commit)</strong> 요청</li>
<li><strong>예외 발생 시:</strong> 트랜잭션 매니저에게 <strong>롤백(Rollback)</strong> 요청</li>
</ul>
</li>
<li><strong>리소스 정리:</strong> 트랜잭션 매니저는 커넥션을 제거하고, DB 커넥션을 종료(또는 풀에 반환)합니다.</li>
</ol>
<h3 id="핵심-개념-짚고-가기">핵심 개념 짚고 가기</h3>
<ul>
<li><strong>트랜잭션 매니저 (<code>PlatformTransactionManager</code>):</strong> JDBC, JPA 등 기술마다 다른 트랜잭션 관리 코드를 추상화한 인터페이스입니다. 개발자는 기술이 바뀌어도 서비스 코드를 수정할 필요가 없습니다.</li>
<li><strong>트랜잭션 동기화 매니저:</strong> 트랜잭션이 시작된 커넥션을 비즈니스 로직 전반에 걸쳐 유지해줍니다. 덕분에 파라미터로 <code>Connection</code>을 계속 넘겨주지 않아도 됩니다.</li>
</ul>
<hr />
<h2 id="2-코드로-보는-프록시-동작-의사-코드">2. 코드로 보는 프록시 동작 (의사 코드)</h2>
<p>이해를 돕기 위해 스프링이 생성하는 프록시 코드를 단순화해보면 아래와 같습니다.</p>
<p><strong>1) 우리가 작성하는 서비스 코드</strong></p>
<pre><code class="language-java">@Service
public class MemberService {

    @Transactional
    public void join(Member member) {
        // 비즈니스 로직
        memberRepository.save(member); 
        // 여기서는 별도의 커넥션 파라미터가 없어도
        // 동기화 매니저에 있는 커넥션을 사용하여 저장함
    }
}</code></pre>
<p><strong>2) 스프링이 만들어내는 프록시 코드 (핵심논리)</strong> </p>
<pre><code class="language-java">public class MemberServiceProxy { // 실제 서비스와 같은 인터페이스 구현 또는 상속

    private final MemberService target; // 실제 비즈니스 로직 객체
    private final PlatformTransactionManager transactionManager;

    public void join(Member member) {
        // 1. 트랜잭션 시작
        TransactionStatus status = transactionManager.getTransaction(new DefaultTransactionDefinition());

        try {
            // 2. 실제 비즈니스 로직 호출
            target.join(member); 

            // 3. 성공 시 커밋
            transactionManager.commit(status);

        } catch (Exception e) {
            // 4. 예외 발생 시 롤백?! (주의: 예외 종류에 따라 다름)
            transactionManager.rollback(status);
            throw e;
        }
    }
}</code></pre>
<p><strong>요약</strong></p>
<p>스프링 트랜잭션 AOP는 <strong>&quot;프록시가 문지기 역할을 하여 트랜잭션을 여닫고, 트랜잭션 매니저가 기술을 추상화하며, 동기화 매니저가 커넥션을 배달해준다&quot;</strong>라고 기억하면 됩니다.</p>
<hr />
<h2 id="spring-트랜잭션-aop-이건-알고-쓰자-전파-속성과-프록시의-함정">[Spring] 트랜잭션 AOP, 이건 알고 쓰자! (전파 속성과 프록시의 함정)</h2>
<p>앞서 살펴본 것처럼 스프링 트랜잭션은 <strong>프록시(Proxy)</strong>를 통해 동작합니다. 이 &quot;프록시 방식&quot;이기 때문에 발생하는 중요한 특징과 주의할 점들이 있습니다. 실무에서 가장 많이 마주치는 이슈인 <strong>트랜잭션 전파(Propagation)</strong>와 <strong>내부 호출(Self-Invocation)</strong> 문제를 알아보겠습니다.</p>
<hr />
<h3 id="1-트랜잭션-전파-propagation-트랜잭션끼리-만나면">1. 트랜잭션 전파 (Propagation): 트랜잭션끼리 만나면?</h3>
<p>서비스 로직이 복잡해지면 트랜잭션이 적용된 메서드가 또 다른 트랜잭션 메서드를 호출하는 경우가 생깁니다. 이때 트랜잭션은 어떻게 동작할까요? 합쳐질까요, 아니면 새로 생길까요? 이를 결정하는 것이 <strong>전파 속성(Propagation)</strong>입니다.</p>
<h4 id="기본값-required-기존-트랜잭션에-참여">기본값: REQUIRED (기존 트랜잭션에 참여)</h4>
<p>별다른 설정을 하지 않으면 디폴트는 <code>REQUIRED</code>입니다.</p>
<ul>
<li><strong>동작:</strong> 이미 진행 중인 트랜잭션이 있으면 그 트랜잭션에 &quot;참여&quot;하고, 없으면 &quot;새로 생성&quot;합니다.</li>
<li><strong>특징:</strong> 하나의 <strong>물리 트랜잭션(DB 커넥션)</strong> 안에서 여러 논리 트랜잭션이 묶이는 형태입니다. 만약 내부 트랜잭션에서 예외가 터져 롤백되면, 외부 트랜잭션까지 모두 롤백됩니다.</li>
</ul>
<h4 id="독립적인-실행-requires_new-항상-새로운-트랜잭션">독립적인 실행: REQUIRES_NEW (항상 새로운 트랜잭션)</h4>
<p>로그를 남기는 기능처럼, 본 로직이 실패해서 롤백되더라도 로그는 반드시 남겨야 하는 경우가 있습니다. 이때는 트랜잭션을 분리해야 합니다.</p>
<ul>
<li><strong>동작:</strong> 진행 중인 트랜잭션이 있더라도 잠시 중단(Suspend)시키고, <strong>완전히 새로운 물리 트랜잭션(새로운 커넥션)</strong>을 엽니다.</li>
<li><strong>특징:</strong> 두 트랜잭션은 서로 영향을 주지 않습니다. 내부가 롤백되어도 외부는 커밋될 수 있습니다.</li>
</ul>
<pre><code class="language-java">@Transactional(propagation = Propagation.REQUIRES_NEW)
public void saveLog(Log log) {
    // 외부 트랜잭션의 성공/실패와 무관하게 별도의 커넥션으로 동작
    logRepository.save(log);
}</code></pre>
<h2 id="2-프록시의-치명적-함정-내부-호출-self-invocation">2. 프록시의 치명적 함정: 내부 호출 (Self-Invocation)</h2>
<p><code>@Transactional</code>을 썼는데 트랜잭션이 적용되지 않는 가장 흔한 실수가 바로 <strong>'같은 클래스 내의 메서드 호출'</strong>입니다.</p>
<h3 id="문제-상황">문제 상황</h3>
<p><strong>(여기에 문제 상황 코드 예시를 넣어주세요)</strong></p>
<p>위 코드에서 <code>createOrder()</code>를 호출하면, 내부에서 <code>saveBill()</code>을 호출할 때 <strong>트랜잭션이 적용되지 않습니다.</strong></p>
<h3 id="왜-안될까">왜 안될까?</h3>
<p>이유는 앞서 배운 <strong>프록시 동작 원리</strong> 때문입니다.</p>
<ol>
<li>클라이언트는 프록시 객체의 <code>createOrder()</code>를 호출합니다.</li>
<li><code>createOrder()</code>에는 <code>@Transactional</code>이 없으므로 프록시는 그냥 실제 객체(Target)의 <code>createOrder()</code>를 호출합니다.</li>
<li>실제 객체 내부에서 <code>saveBill()</code>을 호출할 때, 이것은 <code>this.saveBill()</code>입니다. 즉, <strong>프록시를 거치지 않고 자기 자신의 메서드를 직접 호출</strong>하는 것입니다.</li>
<li>프록시를 통과하지 않았으므로 트랜잭션을 시작하는 코드(AOP)가 실행되지 않습니다.</li>
</ol>
<h3 id="해결-방법">해결 방법</h3>
<p>가장 깔끔하고 권장되는 해결책은 <strong>별도의 클래스로 분리</strong>하는 것입니다.</p>
<pre><code>@Service
public class OrderService {

    private final BillService billService; // 별도 서비스 주입

    public void createOrder() {
        // ... 로직 ...
        // 외부 객체의 메서드를 호출하므로 프록시를 거침 -&gt; 트랜잭션 적용 OK ⭕️
        billService.saveBill(); 
    }
}

@Service
public class BillService {
    @Transactional
    public void saveBill() {
        // ...
    }
}</code></pre><hr />
<h2 id="3-소소한-성능-팁-readonly--true">3. 소소한 성능 팁: readOnly = true</h2>
<p>데이터를 조회만 하는 메서드나 서비스에는 가급적 읽기 전용 모드를 사용하는 것이 좋습니다.</p>
<pre><code class="language-java">@Service
@Transactional(readOnly = true) // 기본적으로 읽기 전용으로 설정
public class MemberService {

    // 조회: 최적화 적용됨
    public Member findOne(Long id) { ... }

    // 변경: 쓰기 권한 필요하므로 오버라이딩
    @Transactional 
    public void join(Member member) { ... }
}

* **JPA 사용 시:** 영속성 컨텍스트가 스냅샷을 만들지 않고, 변경 감지(Dirty Checking)를 수행하지 않아 메모리와 성능이 절약됩니다.
* **DB 부하 분산:** DB가 Master-Slave 구조일 때, Slave(읽기 전용) DB로 커넥션을 연결하도록 설정할 수도 있습니다.</code></pre>
<hr />
<h2 id="정리하며">정리하며</h2>
<p>스프링의 선언적 트랜잭션(<code>@Transactional</code>)은 매우 강력하지만, 그 기반 기술인 <strong>AOP와 프록시의 동작 원리</strong>를 이해하지 못하면 예상치 못한 버그를 만날 수 있습니다.</p>
<ol>
<li>트랜잭션은 <strong>프록시</strong>가 시작하고 종료한다.</li>
<li>프록시를 거치지 않는 <strong>내부 호출(Self-Invocation)</strong>은 트랜잭션이 적용되지 않는다.</li>
<li>필요에 따라 <strong>전파 속성</strong>을 조절하거나 <strong>readOnly</strong> 최적화를 활용하자.</li>
</ol>