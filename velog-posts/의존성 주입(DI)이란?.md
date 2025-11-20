<p>백엔드 개발, 특히 스프링(Spring) 프레임워크를 학습하다 보면 <strong>의존성 주입(Dependency Injection, 이하 DI)</strong>이라는 단어를 반드시 마주하게 됩니다. 객체지향 프로그래밍에서 '유연한 설계'를 하기 위해 필수적인 개념입니다.</p>
<p>DI의 핵심 개념과 다양한 주입 방식, 그리고 왜 <strong>생성자 주입</strong>이 권장되는지에 대해 정리해 보겠습니다.</p>
<hr />
<h2 id="1-의존성dependency이란">1. 의존성(Dependency)이란?</h2>
<p>프로그래밍에서 <strong>의존성</strong>이란 무엇일까요?
아주 간단하게 말해 <strong>&quot;A 객체가 어떤 작업을 수행하기 위해 B 객체를 필요로 하는 상황&quot;</strong>을 말합니다.</p>
<blockquote>
<p><strong>&quot;A는 B에 의존한다.&quot;</strong></p>
</blockquote>
<p>코드로 보면 A 클래스 내부에서 B 클래스의 메서드를 호출하거나 사용하고 있는 상태입니다. 이때 가장 흔히 발생하는 문제는 <strong>A가 B를 직접 생성(<code>new</code>)할 때</strong> 발생합니다.</p>
<pre><code class="language-java">public class MemberService {
    // MemberService가 MemoryRepository를 직접 생성 (의존)
    private final MemoryRepository repository = new MemoryRepository();

    public void join() {
        repository.save();
    }
}</code></pre>
<p>위 코드의 문제점은 무엇일까요?
만약 의존하고 있는 객체를 다른 객체로 바꿔야 한다면, 이를 사용하고 있는 <strong>A 객체의 코드도 직접 수정</strong>해야 합니다. 이를 <strong>강한 결합(Tight Coupling)</strong>이라고 합니다. 유연성이 떨어지고 유지보수가 힘든 구조입니다.</p>
<hr />
<h2 id="2-의존성-주입di의-개념">2. 의존성 주입(DI)의 개념</h2>
<p><strong>의존성 주입(DI)</strong>은 객체(A)가 의존하는 다른 객체(B)를 직접 생성하지 않고, <strong>외부(C)에서 생성해서 넘겨주는(주입하는) 방식</strong>입니다.</p>
<ul>
<li><strong>기존:</strong> A가 B를 직접 생성 (A -&gt; B)</li>
<li><strong>DI 적용:</strong> 외부의 제3자(C)가 B를 생성한 뒤 A에게 줌 (C -&gt; A &lt;- B)</li>
</ul>
<p>이때 '외부의 제3자'는 보통 프레임워크(예: 스프링 컨테이너)가 됩니다. 이를 통해 A 객체는 B가 어떻게 생성되는지 알 필요 없이, 자신의 역할에만 집중할 수 있게 됩니다. 이를 <strong>제어의 역전(IoC, Inversion of Control)</strong>이라고도 부릅니다.</p>
<pre><code class="language-java">public class MemberService {

    // 구체적인 클래스(MemoryRepository)가 아닌 인터페이스(Repository)에 의존
    private final Repository repository;

    // 외부에서 생성된 객체를 생성자를 통해 주입받음
    public MemberService(Repository repository) {
        this.repository = repository;
    }

    public void join() {
        repository.save();
    }
}</code></pre>
<p>이제 이 객체는 어떤 구현체가 오든 상관없습니다. 외부에서 무엇을 주입해 주느냐에 따라 동작이 달라집니다. 코드 변경 없이 다양한 실행 구조를 만들 수 있게 된 것이죠.</p>
<hr />
<h2 id="3-의존성-주입의-3가지-방식">3. 의존성 주입의 3가지 방식</h2>
<p>의존성 주입은 <strong>주입을 받는 위치</strong>에 따라 크게 세 가지로 나뉩니다.</p>
<h3 id="1-생성자-주입-constructor-injection">1) 생성자 주입 (Constructor Injection)</h3>
<p>생성자를 통해 의존성을 주입받는 방식입니다.</p>
<pre><code class="language-java">public class OrderService {
    private final DiscountPolicy discountPolicy;

    public OrderService(DiscountPolicy discountPolicy) {
        this.discountPolicy = discountPolicy;
    }
}</code></pre>
<ul>
<li><strong>특징:</strong> 객체가 생성될 때 딱 한 번 호출되므로 <strong>의존 관계가 변하지 않거나, 필수적인 경우</strong>에 사용합니다.</li>
</ul>
<h3 id="2-setter-주입-setter-injection">2) Setter 주입 (Setter Injection)</h3>
<p>Setter 메서드(수정자)를 통해 의존성을 주입받는 방식입니다.</p>
<pre><code class="language-java">public class OrderService {
    private DiscountPolicy discountPolicy;

    public void setDiscountPolicy(DiscountPolicy discountPolicy) {
        this.discountPolicy = discountPolicy;
    }
}</code></pre>
<ul>
<li><strong>특징:</strong> <strong>선택적이거나 변경 가능성이 있는 의존 관계</strong>에 사용합니다. 다만, 주입받지 않아도 객체가 생성될 수 있어 주의가 필요합니다.</li>
</ul>
<h3 id="3-메서드-주입-method-injection">3) 메서드 주입 (Method Injection)</h3>
<p>메서드 실행 시 인자로 주입받거나, 일반 메서드를 통해 주입받는 방식입니다. 실행할 때마다 의존 대상이 변하는 특수한 경우에 사용되나, 자주 사용되지는 않습니다.</p>
<blockquote>
<p><strong>참고:</strong> <code>@Autowired</code>를 필드에 바로 붙이는 <strong>필드 주입</strong>도 있지만, 외부에서 변경이 불가능해 테스트하기 어렵다는 단점 때문에 최근에는 지양하는 추세입니다.</p>
</blockquote>
<hr />
<h2 id="4-왜-생성자-주입을-써야-할까">4. 왜 '생성자 주입'을 써야 할까?</h2>
<p><strong>생성자 주입을 강력하게 권장</strong>합니다. 다음과 같은 장점들이 있기 때문입니다.</p>
<h3 id="①-불변성immutability-보장">① 불변성(Immutability) 보장</h3>
<p>대부분의 의존 관계는 애플리케이션 종료 시점까지 변하면 안 됩니다.</p>
<ul>
<li>Setter 주입은 메서드를 <code>public</code>으로 열어두어야 하므로, 누군가 실수로 변경할 위험이 있습니다.</li>
<li>생성자 주입은 객체 생성 시 딱 1번만 호출되므로, 이후에 호출될 일이 없어 안전합니다.</li>
</ul>
<h3 id="②-final-키워드-사용-가능">② final 키워드 사용 가능</h3>
<p>생성자 주입을 사용하면 필드에 <code>final</code> 키워드를 사용할 수 있습니다. 이 덕분에 생성자에서 혹시라도 값이 설정되지 않는 오류를 <strong>컴파일 시점</strong>에 바로 막아줍니다</p>
<pre><code class="language-java">private final Repository repository;</code></pre>
<h3 id="③-테스트-코드-작성-용이">③ 테스트 코드 작성 용이</h3>
<p>순수한 자바 코드로 단위 테스트를 작성할 때, 생성자 주입을 사용하면 컴파일러가 필요한 의존성을 알려줍니다.</p>
<ul>
<li>Setter 주입이나 필드 주입은 의존성 없이 객체를 생성할 수 있어, 테스트 도중 <code>NullPointerException</code>이 발생할 수 있습니다.</li>
<li>생성자 주입은 의존성을 넣지 않으면 객체 생성이 불가능하므로, 테스트 시 누락 실수를 방지합니다.</li>
</ul>
<h3 id="④-순환-참조-방지">④ 순환 참조 방지</h3>
<p>A가 B를 참조하고, B가 다시 A를 참조하는 순환 참조가 발생했을 때, 생성자 주입은 서버 구동 시점에 에러를 발생시켜 애플리케이션이 실행되지 않도록 막아줍니다. (문제를 조기에 발견 가능)</p>
<hr />
<h2 id="5-정리">5. 정리</h2>
<p>의존성 주입(DI)은 객체 간의 결합도를 낮춰 <strong>유연하고 변경에 용이한 설계</strong>를 가능하게 합니다.</p>
<ul>
<li><strong>핵심:</strong> 객체 내부에서 직접 <code>new</code> 하지 말고, 외부에서 주입받자.</li>
<li><strong>방식:</strong> 생성자 주입, Setter 주입, 메서드 주입 등이 있다.</li>
<li><strong>결론:</strong> <strong>가급적 '생성자 주입'을 사용하자.</strong> (불변성, 테스트 용이성, 안정성 때문)</li>
</ul>