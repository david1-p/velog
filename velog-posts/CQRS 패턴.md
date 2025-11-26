<h1 id="cqrs-패턴이란-명령과-조회의-책임을-분리하자">CQRS 패턴이란? 명령과 조회의 책임을 분리하자</h1>
<p>백엔드 시스템을 개발하다 보면 하나의 도메인 모델이 점점 비대해지는 경험을 하게 됩니다. 비즈니스 로직을 처리하기 위한 복잡한 객체 그래프와, 단순히 화면에 뿌려주기 위한 조회용 데이터가 뒤섞이면서 유지보수가 어려워지죠.</p>
<p>오늘은 이러한 복잡성을 해결하고 시스템을 유연하게 만드는 아키텍처 패턴인 <strong>CQRS (Command Query Responsibility Segregation)</strong>에 대해 알아보겠습니다.</p>
<h2 id="1-cqrs란-무엇인가">1. CQRS란 무엇인가?</h2>
<p><strong>CQRS</strong>는 <strong>Command Query Responsibility Segregation</strong>의 약자로, 단어 그대로 <strong>'명령(Command)과 조회(Query)의 책임을 분리하는 패턴'</strong>을 의미합니다.</p>
<p>우리가 만드는 시스템의 기능은 크게 두 가지로 나뉩니다.</p>
<ol>
<li><strong>명령 (Command):</strong> 시스템의 상태를 변경하는 작업 (예: 주문 생성, 주문 취소, 결제 승인)</li>
<li><strong>조회 (Query):</strong> 시스템의 상태를 반환하는 작업 (예: 주문 리스트 조회, 내 정보 보기)</li>
</ol>
<p><img alt="" src="https://velog.velcdn.com/images/david1-p/post/240494e5-ff08-47ec-ac56-b4ff49be8f6d/image.png" /></p>
<p>보통은 하나의 모델(Entity)로 이 두 가지 기능을 모두 처리하려 합니다. 하지만 CQRS는 <strong>&quot;상태를 변경하는 모델과 상태를 조회하는 모델을 분리하자&quot;</strong>는 것이 핵심입니다.</p>
<h3 id="핵심-개념-모델의-분리">핵심 개념: 모델의 분리</h3>
<ul>
<li><strong>명령 모델 (Write Model):</strong> 실제 도메인 로직을 수행합니다. 객체 지향적으로 설계되어 있으며, 데이터의 정합성과 불변성을 보장하는 데 집중합니다.</li>
<li><strong>조회 모델 (Read Model):</strong> 화면(UI)이나 리포트에 보여주기 위한 모델입니다. 복잡한 로직 없이 조회 성능과 편의성에 최적화되어 있습니다.</li>
</ul>
<hr />
<h2 id="2-왜-cqrs를-사용해야-할까요-장점">2. 왜 CQRS를 사용해야 할까요? (장점)</h2>
<p>CQRS를 도입하면 다음과 같은 이점을 얻을 수 있습니다.</p>
<h3 id="1-단일-책임-원칙과-유지보수성-향상">1) 단일 책임 원칙과 유지보수성 향상</h3>
<p>비즈니스 로직(명령)과 단순 조회(쿼리)가 분리되므로 코드가 깔끔해집니다.</p>
<ul>
<li>명령 쪽은 복잡한 비즈니스 정책과 도메인 규칙을 구현하는 데 집중할 수 있습니다.</li>
<li>조회 쪽은 화면에 필요한 데이터를 가장 효율적으로 가져오는 데만 집중할 수 있습니다.</li>
</ul>
<h3 id="2-기술-선택의-유연성-polyglot">2) 기술 선택의 유연성 (Polyglot)</h3>
<p>명령과 조회의 목적이 다르기 때문에, 각 모델에 맞는 <strong>최적의 기술</strong>을 자유롭게 선택할 수 있습니다. 꼭 특정 기술을 써야 하는 것은 아니며, 프로젝트 상황에 맞춰 조합할 수 있습니다.</p>
<ul>
<li><strong>명령 (Command):</strong> 도메인 모델링과 트랜잭션 처리가 강력한 기술<ul>
<li>예: <strong>JPA</strong>, Hibernate 등</li>
</ul>
</li>
<li><strong>조회 (Query):</strong> 복잡한 조인이나 통계성 쿼리, 조회 성능에 유리한 기술<ul>
<li>예: <strong>Querydsl</strong>, <strong>MyBatis</strong>, <strong>JdbcTemplate</strong>, 혹은 <strong>JPA DTO Projection</strong></li>
</ul>
</li>
</ul>
<p>심화 단계에서는 기술뿐만 아니라 <strong>저장소(DB)</strong> 자체를 분리하기도 합니다. (예: 명령은 MySQL, 조회는 Redis나 ElasticSearch 사용)</p>
<hr />
<h2 id="3-cqrs-구현-예제-spring-boot--java">3. CQRS 구현 예제 (Spring Boot &amp; Java)</h2>
<p>가장 현실적이고 많이 사용되는 <strong>'단일 DB 내에서 논리적으로 모델을 분리하는 방식'</strong>을 가정해보겠습니다.</p>
<h3 id="3-1-명령command-모델">3-1. 명령(Command) 모델</h3>
<p>명령 모델은 데이터의 일관성을 지키고 비즈니스 로직을 수행하는 데 집중합니다.
단순한 상태 변경뿐만 아니라, <strong>결제 금액 검증, 재고 수량 체크, 배송 상태 확인 등 도메인의 규칙(Invariant)</strong>을 강제하는 역할을 수행합니다.</p>
<pre><code class="language-java">// [Command] Order Entity (도메인 로직 포함)
@Entity
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Order {

    @Id @GeneratedValue
    private Long id;
    private String status;
    // ... 기타 필드들

    // 비즈니스 로직: 주문 취소
    public void cancel() {
        if (this.status.equals(&quot;SHIPPED&quot;)) {
            throw new IllegalStateException(&quot;이미 배송된 상품은 취소가 불가능합니다.&quot;);
        }
        this.status = &quot;CANCELLED&quot;;
    }
}

// [Command] Service
@Service
@Transactional
@RequiredArgsConstructor
public class OrderCommandService {

    private final OrderRepository orderRepository; // JPA Repository

    public void cancelOrder(Long orderId) {
        // 1. 도메인 모델 조회
        Order order = orderRepository.findById(orderId)
                .orElseThrow(() -&gt; new IllegalArgumentException(&quot;Order not found&quot;));

        // 2. 상태 변경 (도메인 로직 수행)
        order.cancel();

        // 3. Dirty Checking으로 자동 업데이트
    }
}</code></pre>
<h3 id="3-2-조회query-모델">3-2. 조회(Query) 모델</h3>
<p>조회 모델은 도메인 로직 없이, <strong>쿼리 최적화 및 화면 표현(View)</strong>에 집중합니다.
단순히 데이터를 가져오는 것을 넘어, 여러 테이블을 조인해서 미리 계산된 결과를 반환하거나(Aggregation), 화면에 딱 맞는 DTO 형태로 데이터를 제공하여 조회 성능을 높입니다.</p>
<pre><code class="language-java">// [Query] OrderData (단순 조회용 DTO)
@Getter
@Setter
public class OrderData {
    private Long orderId;
    private String customerName;
    private String productName;
    private int totalPrice;
    private String status;
}

// [Query] Service
@Service
@Transactional(readOnly = true)
@RequiredArgsConstructor
public class OrderQueryService {

    private final OrderMapper orderMapper; // MyBatis Mapper

    public OrderData getOrderDetails(Long orderId) {
        // 복잡한 조인 쿼리 등을 최적화된 SQL로 직접 실행하여 DTO로 반환
        return orderMapper.findOrderDataById(orderId);
    }
}</code></pre>
<hr />
<h2 id="4-cqrs-적용-시-주의할-점-단점">4. CQRS 적용 시 주의할 점 (단점)</h2>
<p>CQRS는 강력하지만 모든 곳에 무조건 적용해야 하는 것은 아닙니다. </p>
<h3 id="1-구현-복잡도-증가">1) 구현 복잡도 증가</h3>
<p>단순한 CRUD 시스템에 CQRS를 적용하면, 오히려 파일 수가 늘어나고 구조가 불필요하게 복잡해질 수 있습니다. 얻을 수 있는 이점과 구현 비용을 잘 비교해야 합니다.</p>
<h3 id="2-데이터-동기화-문제-db-분리-시">2) 데이터 동기화 문제 (DB 분리 시)</h3>
<p>만약 성능을 극대화하기 위해 명령 DB와 조회 DB를 물리적으로 분리한다면, <strong>데이터 동기화</strong> 이슈가 발생합니다.</p>
<ul>
<li>명령 모델의 변경 사항을 조회 모델로 전파하는 과정에서 시차(Lag)가 발생하여 <strong>'결과적 일관성(Eventual Consistency)'</strong> 문제가 생길 수 있습니다.</li>
</ul>
<hr />
<h2 id="5-결론-언제-cqrs를-써야-할까">5. 결론: 언제 CQRS를 써야 할까?</h2>
<p>단순히 &quot;좋아 보여서&quot; 도입하기보다는, 시스템이 다음과 같은 신호를 보낼 때 도입을 고려해야 합니다.</p>
<ol>
<li><strong>도메인 복잡도가 높은 경우:</strong> DDD(도메인 주도 설계)를 적용할 만큼 비즈니스 로직이 복잡할 때.</li>
<li><strong>트래픽의 불균형:</strong> 읽기(Read) 요청이 쓰기(Write) 요청보다 압도적으로 많아, 조회 성능 최적화가 시급할 때.</li>
<li><strong>모델의 괴리:</strong> 화면에 필요한 데이터 형태와 실제 도메인 엔티티의 구조 차이가 너무 커져서 변환 로직이 비대해질 때.</li>
<li><strong>확장성(Scale-out) 이슈:</strong> 조회 기능과 쓰기 기능의 부하가 달라 각각 다르게 스케일링해야 할 때.</li>
</ol>
<p>CQRS라고 해서 반드시 DB를 쪼개거나 메시지 큐를 도입해야 하는 것은 아닙니다. <strong>코드 레벨에서 명령과 조회의 책임을 나누는 것(논리적 CQRS)</strong>만으로도 복잡도를 낮추는 훌륭한 시작이 될 수 있습니다.</p>