<h1 id="jvm-gc는-어떻게-동작하는가-대상-판단부터-수거-방식까지">[JVM] GC는 어떻게 동작하는가: 대상 판단부터 수거 방식까지</h1>
<p>GC(Garbage Collection)는 JVM의 핵심 기능 중 하나로, 힙(Heap) 영역에 동적으로 할당된 메모리 중 더 이상 필요 없는 객체(Garbage)를 찾아 제거하는 자동 메모리 관리 기법입니다.</p>
<p>이 포스트에서는 JVM이 어떤 객체를 '쓰레기'로 판단하는지, 그리고 그렇게 판단된 객체를 어떤 방식으로 '수거'하는지에 대해 자세히 알아보겠습니다.</p>
<hr />
<h2 id="1-gc-대상은-어떻게-판단할까요">1. GC 대상은 어떻게 판단할까요?</h2>
<p>JVM은 수많은 객체 중에서 어떤 객체를 '필요 없다'고 판단하고 수거 대상으로 삼을까요?</p>
<h3 id="핵심-기준-도달-가능성-reachability">핵심 기준: 도달 가능성 (Reachability)</h3>
<p>JVM은 <strong>'도달 가능성(Reachability)'</strong>이라는 개념을 사용해 객체가 사용 중인지 아닌지를 판단합니다.</p>
<ul>
<li><strong>도달 가능한(Reachable) 상태:</strong> 객체에 유효한 참조(Reference)가 하나라도 존재하는 경우입니다. 이 객체는 '살아있는' 객체로 간주합니다.</li>
<li><strong>도달 불가능한(Unreachable) 상태:</strong> 객체에 도달할 수 있는 유효한 참조가 하나도 존재하지 않는 경우입니다. 이 객체는 '죽은' 객체로 간주하며, <strong>GC의 수거 대상</strong>이 됩니다.</li>
</ul>
<h3 id="도달-가능성의-시작점-gc-root-root-set">'도달 가능성'의 시작점: GC Root (Root Set)</h3>
<p>JVM은 특정 객체가 '도달 가능한지'를 판단하기 위해 <strong>'GC Root(Root Set)'</strong>에서부터 참조 사슬을 추적하기 시작합니다. 힙 내부의 객체들끼리만 서로 참조하고 있는 것(순환 참조)만으로는 '도달 가능하다'고 보지 않습니다.</p>
<p>GC Root는 이 참조 사슬의 '시작점'이 되는 특별한 참조들입니다.</p>
<p><strong>주요 GC Root의 유형:</strong></p>
<ul>
<li><strong>1. 스택(Stack) 영역의 변수:</strong> 현재 실행 중인 메서드의 지역 변수, 파라미터 등 JRE 스택에서 참조하는 객체.</li>
<li><strong>2. 메서드(Method) 영역:</strong> 클래스의 정적(static) 변수가 참조하는 객체.</li>
<li><strong>3. 네이티브(Native) 스택:</strong> JNI(Java Native Interface) 호출로 인해 생성된 객체에 대한 참조.</li>
</ul>
<p>GC는 이 <strong>Root Set에서부터 시작</strong>하여, 참조 관계를 따라가며 도달할 수 있는 모든 객체를 표시(Mark)합니다. 이 과정이 끝난 후, <strong>표시되지 않은 모든 객체(즉, Root Set에서 도달할 수 없는 객체)가 GC 대상</strong>으로 식별됩니다.</p>
<h3 id="개발자가-gc에-관여할-수-있을까요">개발자가 GC에 관여할 수 있을까요?</h3>
<p>원칙적으로 GC는 자동으로 동작하지만, 개발자가 <code>java.lang.ref</code> 패키지의 특별한 참조 클래스를 사용하여 GC의 판단에 어느 정도 영향을 줄 수 있습니다.</p>
<ul>
<li><strong><code>SoftReference</code>:</strong> 감싸고 있는 원본 객체(Referent)에 대한 참조가 Root Set에서 끊겼을 때, <strong>힙 메모리가 부족한 경우에만</strong> GC 대상이 됩니다. (주로 캐시 구현에 사용)</li>
<li><strong><code>WeakReference</code>:</strong> 감싸고 있는 원본 객체에 대한 참조가 Root Set에서 끊겼을 때, 메모리 상태와 관계없이 <strong>다음 GC 사이클에서 바로</strong> GC 대상이 됩니다.</li>
</ul>
<hr />
<h2 id="2-gc는-실제로-어떻게-동작할까요">2. GC는 실제로 어떻게 동작할까요?</h2>
<p>GC 대상을 식별했다면, 이제 JVM은 이 객체들을 '수거'해야 합니다. 이 수거 작업은 여러 알고리즘을 기반으로 동작합니다.</p>
<h3 id="모든-것을-멈추는-시간-stop-the-world">모든 것을 멈추는 시간: Stop-The-World</h3>
<p>GC를 실행하기 위해 JVM은 애플리케이션 실행을 멈춥니다. 이를 <strong>'Stop-The-World'</strong>라고 합니다. GC가 실행되는 동안에는 GC 스레드를 제외한 모든 애플리케이션 스레드가 작업을 멈춥니다. Stop-The-World 시간이 길어질수록 애플리케이션의 응답 시간(Latency)이 길어지므로 이 시간을 최소화하는 것이 GC 튜닝의 핵심입니다.</p>
<h3 id="기본-알고리즘-mark-and-sweep">기본 알고리즘: Mark-and-Sweep</h3>
<p>가장 기본적인 GC 알고리즘입니다.</p>
<ol>
<li><strong>Mark (표시) 단계:</strong> 위에서 설명한 대로, GC Root에서 시작하여 '도달 가능한' 모든 객체를 식별하고 표시(Mark)합니다.</li>
<li><strong>Sweep (쓸기) 단계:</strong> 힙 전체를 스캔하면서 '표시되지 않은' 객체(즉, Unreachable 객체)를 찾아 메모리에서 제거합니다.</li>
</ol>
<ul>
<li><strong>단점: 메모리 파편화 (Fragmentation)</strong>
  Sweep 단계 후에 메모리 공간이 조각조각 나뉘는 '파편화'가 발생할 수 있습니다. 즉, 전체적인 여유 공간은 많아도, 큰 객체를 할당할 연속된 공간이 부족하여 <code>OutOfMemoryError</code>가 발생할 수 있습니다.</li>
</ul>
<h3 id="파편화-해결-mark-and-compact">파편화 해결: Mark-and-Compact</h3>
<p>Mark-and-Sweep의 파편화 문제를 해결하기 위해 고안된 방식입니다.</p>
<ol>
<li><strong>Mark (표시) 단계:</strong> Mark-and-Sweep과 동일하게 동작합니다.</li>
<li><strong>Compact (압축) 단계:</strong> Sweep 대신, '살아남은' 객체들을 힙의 한쪽 끝으로 차곡차곡 이동시켜(압축) 연속된 메모리 공간을 확보합니다. 그 후, 나머지 빈 공간을 'Free' 영역으로 만듭니다.</li>
</ol>
<ul>
<li><strong>장점:</strong> 파편화 문제가 해결됩니다.</li>
<li><strong>단점:</strong> 객체를 이동시키는 '압축' 작업은 Stop-The-World 시간을 증가시키는 비싼 작업입니다.</li>
</ul>
<h3 id="현대-jvm의-선택-세대별-gc-generational-gc">현대 JVM의 선택: 세대별 GC (Generational GC)</h3>
<p>현대의 JVM(HotSpot 등)은 대부분 <strong>'세대별 GC'</strong> 방식을 사용합니다. 이는 다음 두 가지 '약한 세대 가설(Weak Generational Hypothesis)'에 기반합니다.</p>
<ol>
<li><strong>&quot;대부분의 객체는 금방 죽는다(Unreachable 상태가 된다).&quot;</strong></li>
<li><strong>&quot;오래 살아남은 객체는 젊은 객체를 거의 참조하지 않는다.&quot;</strong></li>
</ol>
<p>이 가설에 따라, 힙 영역을 두 세대로 나눕니다.</p>
<ul>
<li><p><strong>Young Generation (영 세대):</strong></p>
<ul>
<li>새롭게 생성된 객체들이 위치하는 영역입니다.</li>
<li>이 영역은 다시 <strong>Eden</strong> 영역과 두 개의 <strong>Survivor</strong> 영역(S0, S1)으로 나뉩니다.</li>
<li>Young Generation에서 발생하는 GC를 <strong>'Minor GC'</strong>라고 부릅니다.</li>
<li><strong>동작 방식 (Minor GC):</strong><ol>
<li>새 객체는 <strong>Eden</strong>에 할당됩니다.</li>
<li>Eden이 꽉 차면 Minor GC가 발생합니다. (Stop-The-World 발생)</li>
<li>Eden과 S0(또는 S1)에 있는 '살아남은' 객체들을 S1(또는 S0)로 <strong>복사(Copy)</strong>합니다.</li>
<li>Eden과 S0(또는 S1) 영역을 비웁니다.</li>
<li>이 과정을 반복하며, 특정 횟수(Age) 이상 살아남은 객체는 <strong>Old Generation</strong>으로 '승격(Promotion)'됩니다.</li>
</ol>
</li>
<li>Minor GC는 대부분의 객체가 금방 죽는다는 가설에 따라 매우 빠르고 빈번하게 발생합니다.</li>
</ul>
</li>
<li><p><strong>Old Generation (올드 세대 / Tenured):</strong></p>
<ul>
<li>Young Generation에서 오랫동안 살아남아 승격된 객체들이 위치하는 영역입니다.</li>
<li>이 영역에서 발생하는 GC를 <strong>'Major GC'</strong> 또는 <strong>'Full GC'</strong>라고 부릅니다.</li>
<li>Old Generation은 객체들이 오랫동안 살아남는 영역이므로, GC가 덜 빈번하게 발생합니다.</li>
<li>대신 한 번 발생하면 <strong>Mark-and-Sweep</strong> 또는 <strong>Mark-and-Compact</strong> 알고리즘을 사용하여 전체 영역을 정리하므로, Stop-The-World 시간이 Minor GC보다 훨씬 깁니다.</li>
</ul>
</li>
</ul>
<blockquote>
<p>세대별 GC는 금방 죽는 객체(Young)와 오래 사는 객체(Old)를 분리하여, GC의 효율을 극대화하고 Stop-The-World 시간을 최소화하는 전략입니다. 하지만 Old Generation이 꽉 찼을 때 발생하는 <strong>Full GC의 긴 Stop-The-World 시간</strong>은 대용량 힙(Heap) 환경에서 심각한 서비스 지연을 유발할 수 있습니다.</p>
</blockquote>
<hr />
<h2 id="3-stw를-줄이기-위한-노력-최신-gc의-진화">3. STW를 줄이기 위한 노력: 최신 GC의 진화</h2>
<p>기존 세대별 GC의 가장 큰 숙제는 'Full GC로 인한 긴 STW'였습니다. 힙 크기가 수십 GB, 수백 GB로 커지면서, 한 번의 Full GC가 몇 초에서 심하면 몇 분까지 애플리케이션을 멈추게 만들었습니다.</p>
<p>이 문제를 해결하기 위해 <strong>'예측 가능하고 짧은 STW'</strong>를 목표로 하는 새로운 GC들이 등장했습니다.</p>
<h3 id="1-g1-garbage-first-gc">1) G1 (Garbage-First) GC</h3>
<p>Java 9부터 기본 GC로 채택된 G1은 세대별 GC의 구조를 유지하면서도, Full GC의 개념을 사실상 없앤 GC입니다.</p>
<ul>
<li><p>** 핵심 아이디어:** &quot;힙 전체를 한 번에 청소하지 말고, <strong>작은 영역(Region)으로 쪼개서</strong> 쓰레기가 가장 많은(Garbage-First) 영역부터 <strong>예측 가능하게</strong> 청소하자!&quot;</p>
</li>
<li><p><strong>특징:</strong></p>
<ol>
<li><strong>Region 기반 힙:</strong> G1은 힙을 1MB ~ 32MB 사이의 수많은 작은 <strong>Region</strong>으로 분할합니다. (기존의 Young/Old 영역처럼 물리적으로 연속되지 않습니다.)</li>
<li><strong>동적인 세대:</strong> 각 Region은 Eden, Survivor, Old의 역할을 동적으로 맡습니다.</li>
<li><strong>Pause Time Goal (일시 정지 목표):</strong> 사용자가 <code>-XX:MaxGCPauseMillis=200</code> 처럼 <strong>목표 STW 시간</strong>을 설정할 수 있습니다. G1은 이 목표 시간을 지키기 위해 '청소할 Region의 수'를 조절합니다.</li>
</ol>
</li>
</ul>
<hr />
<ul>
<li><p><strong>동작 방식 (Mixed GC):</strong></p>
<ol>
<li><strong>Young GC:</strong> 기존의 Minor GC와 유사하게 Eden, Survivor Region에서 살아남은 객체를 다른 Region으로 복사(Evacuation)합니다. (STW 발생)</li>
<li><strong>Concurrent Marking:</strong> Young GC가 발생하는 동안, G1은 애플리케이션과 <strong>동시에(Concurrent)</strong> 전체 힙을 스캔하여 '살아있는 객체'를 파악합니다. 이 정보를 통해 '쓰레기만 가득 찬' Old Region(Garbage-First의 대상)을 식별합니다.</li>
<li><strong>Mixed GC:</strong> G1의 핵심입니다. STW가 발생하면, <strong>모든 Young Region</strong> + <strong>가장 쓰레기가 많은 Old Region 몇 개</strong>를 함께 청소합니다.</li>
<li>살아남은 객체들을 새로운 빈 Region으로 <strong>복사(Copy)</strong>하여 자연스럽게 <strong>압축(Compaction)</strong>을 완료합니다.</li>
</ol>
</li>
<li><p><strong>결론:</strong> G1은 Full GC(Mark-Sweep-Compact)를 피하고, 짧은 STW의 'Mixed GC'를 여러 번 수행하여 Old Generation을 점진적으로 정리합니다. 덕분에 파편화 문제가 해결되고, STW 시간을 예측 가능한 수준(수십 ~ 수백 ms)으로 관리할 수 있게 되었습니다.</p>
</li>
</ul>
<h3 id="2-zgc-z-garbage-collector--shenandoah-gc">2) ZGC (Z Garbage Collector) &amp; Shenandoah GC</h3>
<p>G1이 STW를 '짧게' 만드는 데 집중했다면, ZGC와 Shenandoah는 STW를 <strong>'거의 0'</strong>에 가깝게 만드는 것을 목표로 합니다. 힙 크기가 수백 GB, 수 TB가 되어도 STW 시간을 <strong>1ms 미만</strong>으로 유지하는 것이 목표입니다.</p>
<ul>
<li><p>** 핵심 아이디어:** &quot;Mark(표시), Sweep(제거), 심지어 <strong>Compact(압축)까지</strong> 모든 작업을 애플리케이션과 <strong>동시에(Concurrent)</strong> 진행하자!&quot;</p>
</li>
<li><p><strong>특징:</strong></p>
<ul>
<li><strong>초저지연 (Ultra-Low-Latency):</strong> 힙 크기와 관계없이 일관되게 짧은 STW를 제공합니다.</li>
<li><strong>동시 압축 (Concurrent Compaction):</strong> GC가 객체의 주소를 'A'에서 'B'로 옮기는(압축) 순간에도, 애플리케이션 스레드는 멈추지 않고 계속 실행됩니다.</li>
</ul>
</li>
<li><p><strong>어떻게 이것이 가능할까? (Load Barriers)</strong>
  이 GC들의 핵심 기술은 <strong>'로드 배리어(Load Barrier)'</strong>입니다.</p>
<ol>
<li><p><strong>문제 상황:</strong> GC가 객체 <code>O</code>를 <code>A</code> 주소에서 <code>B</code> 주소로 이동시켰습니다.</p>
</li>
<li><p><strong>동시에:</strong> 애플리케이션 스레드가 <code>O</code>의 옛날 주소 <code>A</code>를 읽으려고 합니다.</p>
</li>
<li><p><strong>해결 (Load Barrier):</strong> JVM(JIT 컴파일러)이 객체 참조를 읽는 모든 코드에 '배리어(방어막)' 코드를 삽입합니다. 이 배리어는 객체를 읽을 때마다 &quot;이 객체가 혹시 이사(Relocation) 중인가?&quot;를 체크합니다.</p>
</li>
<li><p>만약 이사 중이거나 이사가 끝났다면, 애플리케이션 스레드에게 새 주소 <code>B</code>를 알려줍니다.</p>
<p>ZGC는 <strong>컬러드 포인터(Colored Pointers)</strong>, Shenandoah는 <strong>브룩스 포인터(Brooks Pointers)</strong>라는 각자의 방식으로 이 '로드 배리어'를 구현하여, GC와 애플리케이션이 동시에 힙에 접근할 수 있도록 합니다.</p>
</li>
</ol>
</li>
<li><p><strong>결론:</strong> ZGC와 Shenandoah는 STW가 거의 없는 대신, 애플리케이션의 모든 '참조 읽기' 작업에 약간의 오버헤드(배리어 체크)를 추가합니다. 따라서 실시간 응답이 매우 중요한 대용량(수백 GB 이상) 힙을 다루는 시스템에 가장 적합합니다.</p>
</li>
</ul>
<hr />
<h2 id="4-요약">4. 요약</h2>
<ol>
<li><strong>GC 대상 판단:</strong> <strong>GC Root</strong>에서 시작하여 <strong>'도달 가능성(Reachability)'</strong>을 따져, 도달할 수 없는(Unreachable) 객체를 수거 대상으로 판단합니다.</li>
<li><strong>기본 동작:</strong> <strong>Stop-The-World</strong>를 통해 애플리케이션을 멈춘 후, Mark-and-Sweep(파편화 단점)이나 Mark-and-Compact(압축 비용 단점) 같은 알고리즘으로 메모리를 정리합니다.</li>
<li><strong>세대별 GC:</strong> <strong>Young/Old</strong> 세대로 힙을 분리하여, <strong>Minor GC</strong>를 자주, <strong>Major/Full GC</strong>를 가끔 수행함으로써 효율을 높였습니다.</li>
<li><strong>최신 GC (G1):</strong> 힙을 <strong>Region</strong>으로 분할하고, <strong>Mixed GC</strong>를 통해 짧은 STW를 유지하며 점진적으로 힙 전체를 압축/정리합니다. (대부분의 환경에서 기본값)</li>
<li><strong>최신 GC (ZGC/Shenandoah):</strong> <strong>Load Barrier</strong> 기술을 사용하여 <strong>압축(Compaction)까지 동시(Concurrent)</strong>에 처리, STW를 1ms 미만으로 줄이는 것을 목표로 합니다. (초대용량 힙, 초저지연 시스템용)</li>
</ol>