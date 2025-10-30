<h1 id="cpu-스케줄링에-대해서">CPU 스케줄링에 대해서</h1>
<blockquote>
<p>CPU 스케줄링은 운영체제(OS)가 여러 프로세스(실행 중인 프로그램)들에게 <strong>공정하고 합리적으로 CPU 자원을 배분</strong>하는 과정을 의미합니다.</p>
</blockquote>
<blockquote>
<p>만약 CPU 스케줄링이 없다면 어떻게 될까요?
하나의 프로세스가 CPU를 무한정 독점할 수 있습니다. 당장 처리해야 할 중요한 작업(예: 사용자 로그인 요청)이, 급하지 않은 백그라운드 작업(예: 로그 파일 압축) 때문에 무한정 기다려야 할 수도 있습니다. 즉, 시스템은 무질서한 상태가 되고 응답성과 효율성이 극도로 떨어지게 됩니다.</p>
</blockquote>
<p>CPU 스케줄링은 크게 '선점형'과 '비선점형' 두 가지 방식으로 나뉩니다.</p>
<hr />
<h2 id="1-스케줄링의-두-가지-핵심-방식">1. 스케줄링의 두 가지 핵심 방식</h2>
<h3 id="1-비선점형-스케줄링-non-preemptive">1) 비선점형 스케줄링 (Non-preemptive)</h3>
<p><strong>비선점형 스케줄링</strong>은 하나의 프로세스가 CPU 자원을 사용하기 시작하면, 해당 프로세스가 스스로 종료되거나 I/O 대기 상태로 전환되기 전까지는 다른 프로세스가 CPU를 차지할 수 없는 방식입니다.</p>
<ul>
<li><strong>특징:</strong><ul>
<li><strong>장점:</strong> 흐름을 예측하기 쉽고, 문맥 교환(Context Switching) 비용이 적습니다.</li>
<li><strong>단점:</strong> 응답 시간이 길어질 수 있습니다. 실행 시간이 긴 프로세스 하나가 전체 시스템의 반응성을 저해할 수 있습니다. (예: FCFS, SJF)</li>
</ul>
</li>
</ul>
<h3 id="2-선점형-스케줄링-preemptive">2) 선점형 스케줄링 (Preemptive)</h3>
<p><strong>선점형 스케줄링</strong>은 프로세스가 CPU를 사용하고 있더라도, 운영체제가 필요에 따라(예: 더 높은 우선순위의 작업이 들어오거나, 할당된 시간이 다 되거나) 해당 프로세스로부터 자원을 <strong>강제로 빼앗아</strong> 다른 프로세스에 할당할 수 있는 방식입니다.</p>
<ul>
<li><strong>특징:</strong><ul>
<li><strong>장점:</strong> 응답 시간이 빠르고 대화형 시스템에 유리합니다.</li>
<li><strong>단점:</strong> 잦은 문맥 교환으로 인한 오버헤드가 발생할 수 있으며, 여러 프로세스가 자원을 동시에 접근하려 할 때 <strong>경쟁 상태(Race Condition)</strong>가 발생할 수 있습니다. (예: RR, SRT, MLQ)</li>
</ul>
</li>
</ul>
<blockquote>
<h4 id="💡-보충-문맥-교환-context-switching-비용이란">💡 [보충] 문맥 교환 (Context Switching) 비용이란?</h4>
<p>문맥 교환은 현재 실행 중인 프로세스의 상태(예: 레지스터 값, 프로그램 카운터)를 PCB(Process Control Block)에 저장하고, 다음에 실행할 프로세스의 상태를 PCB에서 불러와 CPU에 적재하는 과정을 말합니다.</p>
<p>이 과정 자체는 <strong>CPU가 실질적인 작업을 처리하는 시간이 아니기 때문에 '오버헤드(Overhead)'</strong> 즉, 비용으로 간주됩니다. 선점형 스케줄링은 이 문맥 교환이 빈번하게 발생할 수 있으므로, 이 비용을 고려해야 합니다.</p>
</blockquote>
<hr />
<h2 id="2-핵심-cpu-스케줄링-알고리즘">2. 핵심 CPU 스케줄링 알고리즘</h2>
<p>이제 가장 대표적인 CPU 스케줄링 알고리즘들을 살펴보겠습니다.</p>
<h3 id="1-fcfs-first-come-first-served">1) FCFS (First Come First Served)</h3>
<ul>
<li><strong>방식:</strong> 비선점형</li>
<li><strong>설명:</strong> 준비 큐(Ready Queue)에 도착한 순서대로 CPU를 할당받는, 가장 단순한 방식입니다. (은행 창구에서 번호표 뽑고 기다리는 것과 같습니다.)</li>
<li><strong>문제점:</strong> <strong>호위 효과 (Convoy Effect)</strong><ul>
<li>실행 시간이 매우 긴 프로세스(A)가 CPU를 먼저 점유하면, 뒤따르는 실행 시간이 짧은 프로세스들(B, C)이 A가 끝날 때까지 하염없이 기다려야 하는 현상입니다.</li>
<li><em>예시:</em> A(30초), B(2초), C(1초) 순으로 도착 시, B는 30초, C는 32초를 기다려야 합니다. B와 C의 평균 대기 시간은 매우 길어집니다.</li>
</ul>
</li>
</ul>
<h3 id="2-sjf-shortest-job-first">2) SJF (Shortest Job First)</h3>
<ul>
<li><strong>방식:</strong> 비선점형</li>
<li><strong>설명:</strong> 준비 큐에 있는 프로세스 중 <strong>CPU 실행 시간이 가장 짧은 프로세스</strong>에게 CPU를 먼저 할당합니다.</li>
<li><strong>장점:</strong> 호위 효과를 해결하고, 시스템의 평균 대기 시간을 최소화할 수 있습니다.</li>
<li><strong>문제점:</strong><ol>
<li><strong>기아 현상 (Starvation):</strong> 실행 시간이 긴 프로세스는, 실행 시간이 짧은 프로세스들이 계속 큐에 도착할 경우 무한정 대기하게 되어 CPU를 할당받지 못할 수 있습니다.</li>
<li><strong>실행 시간 예측의 어려움:</strong> 가장 큰 현실적 한계입니다. OS는 프로세스가 <strong>미래에 얼마나 오래 실행될지</strong> 정확히 알 수 없습니다. (보통 과거의 실행 기록을 바탕으로 <em>예측</em>합니다.)</li>
</ol>
</li>
</ul>
<h3 id="3-srt-shortest-remaining-time">3) SRT (Shortest Remaining Time)</h3>
<ul>
<li><strong>방식:</strong> 선점형</li>
<li><strong>설명:</strong> SJF 알고리즘의 선점형 버전입니다. 현재 실행 중인 프로세스의 <strong>남은 실행 시간</strong>보다 더 짧은 실행 시간을 가진 프로세스가 준비 큐에 도착하면, 즉시 CPU를 빼앗아 새 프로세스에 할당합니다.</li>
<li><strong>특징:</strong> SJF의 장점을 가지면서 응답성을 높였습니다. 하지만 SJF와 동일하게 '기아 현상'과 '실행 시간 예측'의 문제를 안고 있습니다.</li>
</ul>
<h3 id="4-라운드-로빈-round-robin-rr">4) 라운드 로빈 (Round Robin, RR)</h3>
<ul>
<li><strong>방식:</strong> 선점형</li>
<li><strong>설명:</strong> FCFS에 <strong>타임 슬라이스 (Time Slice) 또는 타임 퀀텀 (Time Quantum)</strong> 개념을 도입한 방식입니다. 모든 프로세스는 정해진 타임 슬라이스만큼 CPU를 사용하고, 시간이 만료되면 준비 큐의 맨 뒤로 이동합니다.</li>
<li><strong>특징:</strong><ul>
<li>모든 프로세스가 공평하게 CPU 시간을 보장받아 응답 시간이 빠릅니다. 현대 시분할 시스템의 근간이 됩니다.</li>
<li><strong>타임 슬라이스 크기</strong>가 중요합니다.<ul>
<li><strong>너무 크면:</strong> FCFS와 비슷해져 호위 효과가 발생하고 응답성이 떨어집니다.</li>
<li><strong>너무 작으면:</strong> 문맥 교환이 너무 빈번하게 발생하여 오버헤드가 커집니다.</li>
</ul>
</li>
</ul>
</li>
</ul>
<hr />
<h2 id="3-고급-스케줄링과-기아-현상-해결">3. 고급 스케줄링과 '기아 현상' 해결</h2>
<p>SJF, SRT 등 우선순위를 기반으로 하는 스케줄링은 '기아 현상'이라는 고질적인 문제를 안고 있습니다. 이를 해결하기 위한 고급 기법들을 알아봅니다.</p>
<h3 id="1-다단계-큐-multilevel-queue">1) 다단계 큐 (Multilevel Queue)</h3>
<ul>
<li><strong>설명:</strong> 우선순위별로 준비 큐를 여러 개 두는 방식입니다.</li>
<li><strong>작동 방식:</strong><ol>
<li>우선순위가 가장 높은 큐(예: 시스템 프로세스 큐)에 있는 프로세스들을 먼저 처리합니다.</li>
<li>가장 높은 큐가 비어있을 때만, 그 다음 우선순위 큐(예: 대화형 작업 큐)의 프로세스를 처리합니다.</li>
</ol>
</li>
<li><strong>장점:</strong> 프로세스 유형별(예: 대화형 vs. 배치)로 다른 스케줄링 정책(예: 상위 큐는 RR, 하위 큐는 FCFS)을 적용할 수 있습니다.</li>
<li><strong>문제점:</strong> 여전히 하위 큐의 프로세스들은 상위 큐에 작업이 계속 들어오면 기아 현상을 겪을 수 있습니다.</li>
</ul>
<h3 id="2-기아-현상의-해결책-에이징-aging">2) '기아 현상'의 해결책: 에이징 (Aging)</h3>
<p>SJF, SRT, 다단계 큐 등에서 발생하는 기아 현상을 해결하기 위한 대표적인 기법이 바로 <strong>에이징(Aging)</strong>입니다.</p>
<blockquote>
<p><strong>에이징 (Aging):</strong> 오랫동안 준비 큐에서 대기한 프로세스의 우선순위를 <strong>시간이 지남에 따라 점차 높여주는</strong> 기법입니다.</p>
</blockquote>
<p>아무리 우선순위가 낮은 작업이라도, 충분히 오래 기다리면 결국 우선순위가 높아져 언젠가는 실행될 기회를 보장받게 됩니다.</p>
<h3 id="3-다단계-피드백-큐-multilevel-feedback-queue-mlfq">3) 다단계 피드백 큐 (Multilevel Feedback Queue, MLFQ)</h3>
<p>다단계 큐의 기아 현상 문제를 에이징 기법을 적용해 해결한, 가장 정교하고 현대적인 스케줄링 방식 중 하나입니다.</p>
<ul>
<li><strong>작동 방식:</strong><ol>
<li>새 프로세스는 일단 <strong>가장 높은 우선순위 큐</strong>에 들어갑니다. (빠른 응답 기대)</li>
<li>해당 큐의 타임 슬라이스 내에 작업이 끝나지 않으면, 한 단계 <strong>낮은 우선순위 큐</strong>로 강등됩니다. (CPU를 오래 쓰는 작업은 우선순위가 낮아짐)</li>
<li>낮은 우선순위 큐일수록 보통 더 긴 타임 슬라이스를 할당받습니다.</li>
<li><strong>(에이징 적용)</strong> 만약 가장 낮은 큐에서 너무 오래 대기한 프로세스가 있다면, 다시 <strong>상위 큐로 승급</strong>시켜 기아 현상을 방지합니다.</li>
</ol>
</li>
</ul>
<p>MLFQ는 CPU 실행 시간을 예측할 필요 없이, 실행 패턴(짧은 작업/긴 작업)에 따라 동적으로 프로세스의 우선순위가 조정되므로 매우 효율적이고 유연합니다.</p>
<hr />
<h2 id="4-마치며-개발자가-스케줄링을-알아야-하는-이유">4. 마치며: 개발자가 스케줄링을 알아야 하는 이유</h2>
<p>CPU 스케줄링은 운영체제 깊숙한 곳에서 일어나지만, 백엔드 애플리케이션의 <strong>응답 시간(Latency)</strong>과 <strong>처리량(Throughput)</strong>에 직접적인 영향을 줍니다.</p>
<ul>
<li><strong>SJF</strong> 계열은 <strong>처리량</strong>을 극대화하려 하지만 기아 현상이 발생할 수 있습니다.</li>
<li><strong>RR</strong> 계열은 <strong>응답성</strong>을 보장하려 하지만 문맥 교환 비용이 발생합니다.</li>
<li><strong>MLFQ</strong>는 이 둘의 균형을 맞추려는 현대적인 해법입니다.</li>
</ul>
<blockquote>
<p>어떤 스케줄링 알고리즘이 사용되느냐에 따라, 애플리케이션의 특정 요청이 왜 늦게 응답하는지, 혹은 서버 리소스가 특정 작업에 몰리는 이유를 어렴풋이나마 짐작할 수 있게 됩니다. 이는 시스템의 성능 병목을 파악하고 최적화하는 데 중요한 기초 지식이 되어줄 것입니다.</p>
</blockquote>