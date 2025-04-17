<blockquote>
<p>개발을 하다 보면 다양한 문제에 직면하게 되고, 이를 해결하기 위해 여러 프로그래밍 패러다임이나 디자인 패턴을 선택하게 됩니다. 이 글에서는 절차형 프로그래밍의 개념과 활용, 프록시 서버의 역할과 사용사례, 옵저버 패턴 구현 방법, 그리고 MVC와 MVVM 패턴의 차이에 대해 정리해보았습니다.</p>
</blockquote>
<h2 id="1-절차형-프로그래밍이란">1. 절차형 프로그래밍이란?</h2>
<p> 절차형(Procedural) 프로그래밍은 로직이 수행되어야 할 연속적인 계산 과정으로 이루어져 있으며, 일이 진행되는 흐름대로 코드만 작성하면 되는 구조입니다. 절차형 프로그래밍은 계산이 많은 작업에 적합합니다. 예를 들어 머신러닝 파이프라인 같은 경우 연속적인 데이터 처리 흐름이 중요하므로 절차형 접근이 효율적일 수 있습니다.</p>
<h3 id="11-절차형-프로그래밍의-장점">1.1 절차형 프로그래밍의 장점</h3>
<ul>
<li>코드 가독성이 좋고 단순하다</li>
<li>실행 속도가 빠르다</li>
</ul>
<h3 id="12-절차형-프로그래밍의-단점">1.2 절차형 프로그래밍의 단점</h3>
<ul>
<li>모듈화가 어렵다</li>
<li>유지보수가 힘들다</li>
</ul>
<h3 id="13-절차형-프로그래밍의-예시-java">1.3 절차형 프로그래밍의 예시 (Java)</h3>
<pre><code class="language-java">public class Calculator {
    public static void main(String[] args) {
        int num1 = 10;
        int num2 = 5;

        int result = num1 + num2; // 연속적인 절차로 계산 수행
        System.out.println(&quot;결과: &quot; + result);
    }
}</code></pre>
<ul>
<li>흐름대로 작성되는 간단한 코드 </li>
<li>구조는 간단하지만, 기능이 많아지면 복잡도가 급격히 증가한다. </li>
</ul>
<h2 id="2-옵저버-패턴">2. 옵저버 패턴</h2>
<p>옵저버(Observer) 패턴은 객체 간의 일대다 의존성을 정의하여, 어떤 객체의 상태가 변경되었을 때 그에 의존하는 모든 객체에 알림이 가도록 하는 패턴입니다. 구현 방법 중 하나는 프록시 (Proxy)객체 사용하는 것입니다. </p>
<p>옵저버 패턴을 구현할 때 프록시 객체를 사용하면 유용합니다. 프록시를 통해 객체의 속성 변경이나 메서드 호출을 감지하고, 이를 <strong>미리 등록된 옵저버(구독자)</strong>에게 알릴 수 있습니다.</p>
<h3 id="21-프록시-서버란">2.1 프록시 서버란?</h3>
<p>프록시 서버는 클라이언트와 실제 서버 사이에 위치하여, 중계 역할을 수행하는 서버입니다. 이를 통해 다양한 이점을 얻을 수 있습니다.</p>
<h3 id="22-프록시-서버의-역할">2.2 프록시 서버의 역할</h3>
<ul>
<li>캐싱: 응답을 미리 저장하여 성능 개선</li>
<li>로깅 및 데이터 분석</li>
<li>보안 강화: 클라이언트가 직접 실제 서버에 접근하지 못하게 함</li>
<li>포트 감춤, DDOS 방어, CDN 구성 가능</li>
</ul>
<h3 id="23-프록시-서버-예시nginx">2.3 프록시 서버 예시(Nginx)</h3>
<pre><code class="language-nginx">server {
    listen 80;

    location / {
        proxy_pass http://localhost:3000; # Node.js 서버로 요청 전달
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
</code></pre>
<ul>
<li>Nginx를 Node.js 서버 앞단에 두어 버퍼 오버플로우를 예방</li>
<li>Cloudflare를 통해 캐싱, DDOS 방어, 로그 분석 수행</li>
</ul>
<h3 id="24-옵저버-패턴의-예시javascript">2.4 옵저버 패턴의 예시(Javascript)</h3>
<pre><code class="language-javascript">class Subject {
  constructor() {
    this.observers = [];
    this.state = 0;
  }

  subscribe(observer) {
    this.observers.push(observer);
  }

  setState(newState) {
    this.state = newState;
    this.notify();
  }

  notify() {
    this.observers.forEach(observer =&gt; observer.update(this.state));
  }
}

class Observer {
  update(state) {
    console.log(`옵저버 알림: 상태가 ${state}로 변경됨`);
  }
}

// 사용
const subject = new Subject();
const observer1 = new Observer();
const observer2 = new Observer();

subject.subscribe(observer1);
subject.subscribe(observer2);

subject.setState(1);
</code></pre>
<h2 id="3-mvc-vs-mvvm-패턴">3. MVC vs MVVM 패턴</h2>
<h3 id="31-mvc-패턴">3.1 MVC 패턴</h3>
<p><strong>MVC(Model-View-Controller)</strong>는 앱의 구성요소를 세 가지 역할로 분리하여 개발 효율을 높이는 디자인 패턴입니다.</p>
<ul>
<li>Model: 데이터 및 비즈니스 로직</li>
<li>View: 사용자 인터페이스</li>
<li>Controller: 입력 처리 및 로직 연결</li>
</ul>
<h3 id="311-mvc-패턴의-장점">3.1.1 MVC 패턴의 장점</h3>
<ul>
<li>역할 분리로 개발 생산성 향상</li>
<li>확장성, 재사용성 높음</li>
</ul>
<h3 id="312-mvc-패턴의-단점">3.1.2 MVC 패턴의 단점</h3>
<ul>
<li>앱이 복잡해질수록 모델과 뷰의 관계가 복잡해질 수 있음</li>
</ul>
<h3 id="313-mvc-패턴의-예시java">3.1.3 MVC 패턴의 예시(Java)</h3>
<pre><code class="language-java">// Model
class User {
    private String name;
    public User(String name) { this.name = name; }
    public String getName() { return name; }
}

// View
class UserView {
    public void printUserDetails(String userName) {
        System.out.println(&quot;User: &quot; + userName);
    }
}

// Controller
class UserController {
    private User model;
    private UserView view;

    public UserController(User model, UserView view) {
        this.model = model;
        this.view = view;
    }

    public void updateView() {
        view.printUserDetails(model.getName());
    }
}

// 사용
public class MVCPatternDemo {
    public static void main(String[] args) {
        User model = new User(&quot;David&quot;);
        UserView view = new UserView();
        UserController controller = new UserController(model, view);

        controller.updateView();
    }
}</code></pre>
<ul>
<li>각 구성 요소의 역할이 명확하게 나뉨</li>
<li>모델, 뷰, 컨트롤러 각각 독립적으로 관리 가능</li>
</ul>
<h3 id="32-mvvm-패턴">3.2 MVVM 패턴</h3>
<p><strong>MVVM(Model-View-ViewModel)</strong>은 MVC에서 Controller 대신 ViewModel을 사용하는 구조입니다.</p>
<ul>
<li>ViewModel: View를 추상화한 계층으로, 뷰의 상태를 관리</li>
<li>Command &amp; Data Binding: 뷰와 뷰모델 간 양방향 바인딩이 가능</li>
</ul>
<h3 id="321-mvvm-패턴의-장점">3.2.1 MVVM 패턴의 장점</h3>
<ul>
<li>UI 코드의 재사용성 증가</li>
<li>테스트 용이성 높음</li>
<li>유지보수성 향상</li>
</ul>
<h3 id="322-mvvm-패턴의-단점">3.2.2 MVVM 패턴의 단점</h3>
<ul>
<li>간단한 UI에는 오히려 과한 설계가 될 수 있음</li>
<li>데이터 바인딩이 필수로 요구되어 프레임워크나 라이브러리의 바인딩 지원 여부에 따라 구현난이도와 비용이 증가</li>
<li>ViewModel이 빠르게 비대화</li>
<li>표준화된 구조가 없어서 개발자마다 구현하는 방식이 다름</li>
<li>XML 코드가 복잡하고 난해해질 수 있음</li>
</ul>
<h3 id="마무리">마무리</h3>
<blockquote>
<p>프로그래밍 패러다임이나 디자인 패턴은 상황에 따라 적절히 선택하고 조합하는 것이 핵심입니다. 절차형이든 함수형이든, MVC든 MVVM이든 정답은 없습니다. 내가 해결해야 할 문제와 시스템의 성격을 이해하고 그에 맞는 도구를 쓰는 것이 가장 좋은 접근입니다.</p>
</blockquote>
<p>참고 <a href="https://velog.io/@ryuhyewon/MVVM-%EA%B0%9C%EB%85%90%EA%B3%BC-%EC%9E%A5%EB%8B%A8%EC%A0%90">https://velog.io/@ryuhyewon/MVVM-%EA%B0%9C%EB%85%90%EA%B3%BC-%EC%9E%A5%EB%8B%A8%EC%A0%90</a></p>