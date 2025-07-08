<h2 id="1-운영체제-관점-program--process--thread">1. 운영체제 관점: Program / Process / Thread</h2>
<h3 id="program-프로그램">Program (프로그램)</h3>
<p><strong>정의</strong>: 실행 가능한 파일 형태로 저장장치에 저장된 명령어들의 집합</p>
<p><strong>특징</strong>:</p>
<ul>
<li>정적(Static)인 개념</li>
<li>디스크나 SSD와 같은 보조기억장치에 저장된 실행 파일</li>
<li>아직 실행되지 않은 상태의 코드와 데이터</li>
<li>하나의 프로그램으로 여러 개의 프로세스를 생성할 수 있음</li>
</ul>
<p><strong>예시</strong>: </p>
<ul>
<li><code>notepad.exe</code> (메모장 실행 파일)</li>
<li><code>chrome.exe</code> (크롬 브라우저 실행 파일)</li>
<li><code>python.exe</code> (파이썬 인터프리터 실행 파일)</li>
</ul>
<h3 id="process-프로세스">Process (프로세스)</h3>
<p><strong>정의</strong>: 실행 중인 프로그램의 인스턴스로, 운영체제로부터 자원을 할당받은 작업 단위</p>
<p><strong>특징</strong>:</p>
<ul>
<li>동적(Dynamic)인 개념</li>
<li>독립적인 메모리 공간을 가짐 (코드, 데이터, 힙, 스택 영역)</li>
<li>각자 고유한 Process ID(PID)를 가짐</li>
<li>프로세스 간 통신은 IPC(Inter-Process Communication)를 통해 이루어짐</li>
<li>하나의 프로세스가 오류로 종료되어도 다른 프로세스에 영향을 주지 않음</li>
</ul>
<p><strong>구성 요소</strong>:</p>
<ul>
<li><strong>PCB(Process Control Block)</strong>: 프로세스 상태 정보를 저장하는 구조체</li>
<li><strong>메모리 공간</strong>: 독립적인 가상 메모리 주소 공간</li>
<li><strong>자원</strong>: 파일, 네트워크 연결, 장치 등</li>
</ul>
<p><strong>프로세스 상태</strong>:</p>
<ul>
<li><strong>New</strong>: 프로세스가 생성 중인 상태</li>
<li><strong>Ready</strong>: CPU 할당을 기다리는 상태</li>
<li><strong>Running</strong>: CPU를 할당받아 실행 중인 상태</li>
<li><strong>Waiting</strong>: I/O 작업 완료를 기다리는 상태</li>
<li><strong>Terminated</strong>: 실행이 완료되어 종료된 상태</li>
</ul>
<h3 id="thread-스레드">Thread (스레드)</h3>
<p><strong>정의</strong>: 프로세스 내에서 실행되는 가장 작은 실행 단위</p>
<p><strong>특징</strong>:</p>
<ul>
<li>경량 프로세스(Light Weight Process)라고도 불림</li>
<li>같은 프로세스 내의 스레드들은 메모리 공간을 공유</li>
<li>독립적인 스택과 레지스터를 가짐</li>
<li>스레드 간 통신은 메모리 공유를 통해 쉽게 이루어짐</li>
<li>컨텍스트 스위칭 비용이 프로세스보다 적음</li>
</ul>
<p><strong>메모리 구조</strong>:</p>
<ul>
<li><strong>공유 영역</strong>: 코드, 데이터, 힙 영역</li>
<li><strong>독립 영역</strong>: 스택, 레지스터</li>
</ul>
<p><strong>장점</strong>:</p>
<ul>
<li>빠른 생성과 종료</li>
<li>효율적인 자원 사용</li>
<li>응답성 향상</li>
<li>병렬 처리 가능</li>
</ul>
<p><strong>단점</strong>:</p>
<ul>
<li>동기화 문제 (Race Condition, Deadlock)</li>
<li>하나의 스레드 오류가 전체 프로세스에 영향</li>
<li>디버깅의 복잡성</li>
</ul>
<h2 id="2-객체지향-프로그래밍-관점-class--object--method">2. 객체지향 프로그래밍 관점: Class / Object / Method</h2>
<h3 id="class-클래스">Class (클래스)</h3>
<p><strong>정의</strong>: 객체를 생성하기 위한 설계도 또는 템플릿</p>
<p><strong>특징</strong>:</p>
<ul>
<li>추상적인 개념</li>
<li>속성(Attribute)과 메서드(Method)를 정의</li>
<li>객체가 어떤 데이터를 가지고 어떤 동작을 할지 명세</li>
<li>코드의 재사용성과 모듈성을 제공</li>
</ul>
<p><strong>구성 요소</strong>:</p>
<ul>
<li><strong>속성(Field/Property)</strong>: 객체의 상태를 나타내는 변수</li>
<li><strong>메서드(Method)</strong>: 객체의 동작을 정의하는 함수</li>
<li><strong>생성자(Constructor)</strong>: 객체를 초기화하는 특별한 메서드</li>
<li><strong>소멸자(Destructor)</strong>: 객체가 소멸될 때 호출되는 메서드</li>
</ul>
<p><strong>예시 (Java)</strong>:</p>
<pre><code class="language-java">public class Car {
    // 속성
    private String brand;
    private int speed;

    // 생성자
    public Car(String brand) {
        this.brand = brand;
        this.speed = 0;
    }

    // 메서드
    public void accelerate() {
        speed += 10;
    }

    public void brake() {
        speed = Math.max(0, speed - 10);
    }
}</code></pre>
<h3 id="object-객체">Object (객체)</h3>
<p><strong>정의</strong>: 클래스를 바탕으로 생성된 실체(Instance)</p>
<p><strong>특징</strong>:</p>
<ul>
<li>클래스의 구체적인 구현체</li>
<li>실제 메모리에 할당되는 실체</li>
<li>고유한 식별자(Identity)를 가짐</li>
<li>상태(State)와 행동(Behavior)을 가짐</li>
<li>다른 객체와 메시지를 주고받으며 상호작용</li>
</ul>
<p><strong>객체의 3요소</strong>:</p>
<ol>
<li><strong>Identity (식별성)</strong>: 객체를 구별할 수 있는 고유성</li>
<li><strong>State (상태)</strong>: 객체의 속성 값들</li>
<li><strong>Behavior (행동)</strong>: 객체가 수행할 수 있는 동작들</li>
</ol>
<p><strong>예시</strong>:</p>
<pre><code class="language-java">// 객체 생성
Car myCar = new Car(&quot;Toyota&quot;);  // myCar는 Car 클래스의 객체
Car yourCar = new Car(&quot;Honda&quot;); // yourCar는 Car 클래스의 또 다른 객체

// 각 객체는 독립적인 상태를 가짐
myCar.accelerate();  // myCar의 속도만 증가
yourCar.brake();     // yourCar의 속도만 감소</code></pre>
<h3 id="method-메서드">Method (메서드)</h3>
<p><strong>정의</strong>: 객체의 동작을 정의하는 함수</p>
<p><strong>특징</strong>:</p>
<ul>
<li>객체의 행동을 구현</li>
<li>객체의 상태를 변경하거나 조회</li>
<li>다른 객체와의 상호작용을 담당</li>
<li>매개변수를 받고 반환값을 가질 수 있음</li>
</ul>
<p><strong>메서드 종류</strong>:</p>
<ol>
<li><strong>Instance Method</strong>: 특정 객체에 대해 호출되는 메서드</li>
<li><strong>Static Method</strong>: 클래스 차원에서 호출되는 메서드</li>
<li><strong>Abstract Method</strong>: 구현이 없는 추상 메서드</li>
<li><strong>Final Method</strong>: 오버라이드할 수 없는 메서드</li>
</ol>
<p><strong>접근 제어자</strong>:</p>
<ul>
<li><strong>public</strong>: 어디서든 접근 가능</li>
<li><strong>private</strong>: 같은 클래스 내에서만 접근 가능</li>
<li><strong>protected</strong>: 같은 패키지 또는 상속받은 클래스에서 접근 가능</li>
<li><strong>default</strong>: 같은 패키지 내에서만 접근 가능</li>
</ul>
<p><strong>예시</strong>:</p>
<pre><code class="language-java">public class BankAccount {
    private double balance;

    // Instance Method
    public void deposit(double amount) {
        if (amount &gt; 0) {
            balance += amount;
        }
    }

    // Instance Method
    public double getBalance() {
        return balance;
    }

    // Static Method
    public static double calculateInterest(double principal, double rate) {
        return principal * rate;
    }
}</code></pre>
<h2 id="개념-간-관계-정리">개념 간 관계 정리</h2>
<h3 id="운영체제-관점">운영체제 관점</h3>
<ul>
<li><strong>Program → Process</strong>: 프로그램이 실행되면 프로세스가 됨</li>
<li><strong>Process → Thread</strong>: 프로세스 내에서 여러 스레드가 동시에 실행됨</li>
<li><strong>관계</strong>: 1개 프로그램 → N개 프로세스 → M개 스레드</li>
</ul>
<h3 id="객체지향-관점">객체지향 관점</h3>
<ul>
<li><strong>Class → Object</strong>: 클래스를 바탕으로 객체를 생성 (인스턴스화)</li>
<li><strong>Object → Method</strong>: 객체가 메서드를 호출하여 동작 수행</li>
<li><strong>관계</strong>: 1개 클래스 → N개 객체 → M개 메서드 호출</li>
</ul>
<h3 id="두-관점의-유사성">두 관점의 유사성</h3>
<ul>
<li><strong>설계 vs 실행</strong>: Program/Class는 설계, Process/Object는 실행 중인 실체</li>
<li><strong>단위</strong>: Thread/Method는 실제 작업을 수행하는 최소 단위</li>
<li><strong>추상화</strong>: 두 관점 모두 복잡한 시스템을 계층적으로 추상화하여 관리</li>
</ul>