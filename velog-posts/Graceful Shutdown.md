<h1 id="graceful-shutdown-안정적인-서버-종료를-위한-전략과-spring-boot-내부-동작">Graceful Shutdown: 안정적인 서버 종료를 위한 전략과 Spring Boot 내부 동작</h1>
<p>백엔드 애플리케이션을 개발하고 운영하다 보면 '배포'는 피할 수 없는 일상적인 작업입니다. 새로운 기능을 추가하거나 버그를 수정하기 위해 기존 프로세스를 종료하고 새로운 프로세스를 실행합니다. 이때 중요한 것은 <strong>&quot;어떻게 종료하느냐&quot;</strong> 입니다.</p>
<p>애플리케이션이 정상적으로 시작되는 것만큼이나 정상적으로 종료되는 과정 또한 서비스의 안정성에 큰 영향을 미칩니다. 이번 글에서는 Graceful Shutdown(우아한 종료)의 필요성과 OS 시그널의 차이, Spring Boot 환경에서의 구체적인 동작 원리, 그리고 <strong>실무 운영 환경(로드밸런서, 쿠버네티스)에서의 고려사항</strong>까지 알아보겠습니다.</p>
<h2 id="1-graceful-shutdown이란">1. Graceful Shutdown이란?</h2>
<p>Graceful Shutdown(우아한 종료)이란 애플리케이션이 종료 신호를 받았을 때 즉시 전원을 끄듯 멈추는 것이 아니라, <strong>현재 처리 중인 작업들을 모두 안전하게 마무리하고 리소스를 정리한 뒤 종료하는 방식</strong>을 의미합니다.</p>
<p>서버 애플리케이션 관점에서 보면 다음과 같은 절차를 따릅니다.</p>
<ol>
<li>종료 신호(SIGTERM 등)를 감지합니다.</li>
<li>로드 밸런서나 연결된 네트워크 레이어로부터 들어오는 <strong>새로운 요청을 차단</strong>합니다.</li>
<li>이미 서버 내에서 <strong>처리 중이던 요청들은 끝까지 수행</strong>합니다.</li>
<li>모든 처리가 완료되거나 타임아웃 시간이 지나면 프로세스를 종료합니다.</li>
</ol>
<h3 id="왜-필요한가">왜 필요한가?</h3>
<p>만약 요청을 처리하는 도중에 프로세스가 즉각적으로(Hard Shutdown) 종료된다면 다음과 같은 문제가 발생할 수 있습니다.</p>
<ul>
<li><strong>트랜잭션 비정상 종료:</strong> 데이터베이스에 쓰기 작업 중 연결이 끊겨 데이터 무결성이 깨질 수 있습니다.</li>
<li><strong>데이터 손실:</strong> 메모리상에 존재하던 미처 저장되지 못한 데이터가 유실될 수 있습니다.</li>
<li><strong>사용자 경험 저하:</strong> 클라이언트는 정상적인 응답 대신 <code>Connection Reset</code> 오류나 <code>500 Internal Server Error</code>를 받게 됩니다.</li>
</ul>
<h2 id="2-종료-시그널-sigterm-vs-sigkill">2. 종료 시그널: SIGTERM vs SIGKILL</h2>
<p>리눅스 및 유닉스 환경에서 프로세스를 종료할 때 사용하는 <code>kill</code> 명령어는 프로세스에 특정 시그널(Signal)을 보냅니다. Graceful Shutdown을 위해서는 <code>SIGTERM</code>과 <code>SIGKILL</code>의 차이를 명확히 알아야 합니다.</p>
<h3 id="sigkill--9">SIGKILL (-9)</h3>
<ul>
<li><strong>의미:</strong> 프로세스 강제 종료</li>
<li><strong>동작:</strong> 프로세스가 종료되기 전에 수행해야 할 정리 작업(Cleanup)을 전혀 실행하지 않고 커널 레벨에서 즉시 프로세스를 제거합니다. 애플리케이션이 시그널을 핸들링할 수 없으므로 Graceful Shutdown이 불가능합니다.</li>
</ul>
<!-- end list -->

<pre><code class="language-bash">kill -9 {PID}</code></pre>
<h3 id="sigterm--15">SIGTERM (-15)</h3>
<ul>
<li><strong>의미:</strong> 프로세스 종료 요청 (Termination Signal)</li>
<li><strong>동작:</strong> 프로세스에게 &quot;종료해달라&quot;는 신호를 보냅니다. 프로세스는 이 시그널을 핸들링할 수 있어, 종료 전 필요한 로직(리소스 정리, 진행 중인 작업 완료 등)을 수행할 수 있습니다.</li>
<li><strong>Graceful Shutdown:</strong> 가능합니다. 배포 스크립트 등에서 별도의 옵션 없이 <code>kill</code> 명령어를 사용하면 기본적으로 이 시그널이 전송됩니다.</li>
</ul>
<!-- end list -->

<pre><code class="language-bash">kill {PID}  # 기본값 -15 (SIGTERM)</code></pre>
<h2 id="3-spring-boot에서의-graceful-shutdown-설정">3. Spring Boot에서의 Graceful Shutdown 설정</h2>
<p>Spring Boot 2.3 버전부터는 설정을 통해 매우 간단하게 Graceful Shutdown을 적용할 수 있습니다.</p>
<h3 id="applicationproperties-설정">application.properties 설정</h3>
<pre><code class="language-properties"># Graceful Shutdown 활성화 (기본값: immediate)
server.shutdown=graceful

# 종료 대기 타임아웃 설정 (기본값: 30s)
spring.lifecycle.timeout-per-shutdown-phase=20s</code></pre>
<p><code>server.shutdown</code>을 <code>graceful</code>로 설정하면 Spring Boot의 내장 웹 서버는 종료 시그널을 받았을 때 새로운 요청을 받지 않고 기존 요청을 처리하기 위해 대기합니다.</p>
<h3 id="주의할-점-타임아웃의-종류">주의할 점: 타임아웃의 종류</h3>
<p>설정에서 <code>spring.lifecycle.timeout-per-shutdown-phase</code>는 Spring 컨테이너의 라이프사이클 빈(Bean)들이 종료되는 데 기다려주는 전체 시간을 의미합니다. 하지만 실제 운영 환경에서는 <strong>Tomcat 자체의 내부 Graceful Shutdown 타임아웃</strong>과 구분해서 이해할 필요가 있습니다. 일반적으로 Spring의 설정이 우선순위를 가지며 전체 종료 과정을 제어하지만, 아주 정밀한 튜닝이 필요한 경우 톰캣 레벨의 설정이 별도로 존재함을 인지해야 합니다.</p>
<h3 id="내장-웹-서버별-동작-차이">내장 웹 서버별 동작 차이</h3>
<p>Spring Boot는 여러 내장 웹 서버를 지원하며, 서버마다 Graceful Shutdown 구현에 차이가 있습니다.</p>
<ul>
<li><strong>Tomcat / Jetty:</strong> 표준적인 Graceful Shutdown 동작을 지원합니다. 네트워크 레이어에서 새로운 연결을 막고 기존 요청을 처리합니다.</li>
<li><strong>Netty (WebFlux):</strong> Reactor Netty의 라이프사이클에 맞춰 동작하며, 비동기 논블로킹 특성에 맞게 처리됩니다.</li>
<li><strong>Undertow:</strong> 과거 버전이나 특정 설정에 따라 공식적인 Graceful Shutdown 지원이 제한적이거나 중단된 경우가 있어, 사용 시 별도의 확인이 필요합니다.</li>
</ul>
<h2 id="4-실무-운영-환경-고려사항-load-balancer--kubernetes">4. 실무 운영 환경 고려사항 (Load Balancer &amp; Kubernetes)</h2>
<p>Graceful Shutdown 설정만으로는 '무중단 배포'를 완벽하게 보장하기 어렵습니다. 실제 서비스는 로드밸런서(LB) 뒤에 존재하기 때문입니다.</p>
<h3 id="로드밸런서의-연결-해제-deregistration">로드밸런서의 연결 해제 (Deregistration)</h3>
<p>Spring Boot가 종료를 시작(<code>SIGTERM</code> 수신)하면 새로운 요청을 거부하지만, 로드밸런서가 이를 인지하고 트래픽을 차단하기까지 <strong>시차</strong>가 발생할 수 있습니다.</p>
<ul>
<li><strong>AWS ALB/NLB:</strong> 대상 그룹(Target Group)에서 인스턴스를 제외하는 <code>Deregistration Delay</code> (기본 300초) 설정이 있습니다.</li>
<li><strong>Kubernetes:</strong> Pod가 종료될 때 <code>preStop</code> hook을 사용하여 로드밸런서(Service) 갱신 시간을 벌어주어야 합니다.</li>
</ul>
<p>따라서 이상적인 종료 시나리오는 다음과 같습니다.</p>
<ol>
<li><strong>Kubernetes/LB:</strong> 트래픽 차단 시작</li>
<li><strong>App:</strong> <code>preStop</code> 등을 이용해 잠시 대기 (기존 요청 처리 + LB 갱신 대기)</li>
<li><strong>App:</strong> <code>SIGTERM</code> 수신 -&gt; Spring Boot Graceful Shutdown 시작</li>
<li><strong>App:</strong> 잔여 작업 완료 후 종료</li>
</ol>
<p>이때, Kubernetes의 <code>terminationGracePeriodSeconds</code>는 Spring Boot의 <code>timeout-per-shutdown-phase</code>보다 넉넉하게 설정해야 프로세스가 강제 종료(<code>SIGKILL</code>) 당하는 것을 방지할 수 있습니다.</p>
<h2 id="5-내부-동작-원리-및-검증-tomcat-기준">5. 내부 동작 원리 및 검증 (Tomcat 기준)</h2>
<p>Spring Boot와 내장 Tomcat은 어떻게 이 기능을 구현했을까요? 내부 코드를 통해 동작 원리를 살펴보겠습니다.</p>
<h3 id="동작-테스트">동작 테스트</h3>
<p>요청 처리에 15초가 걸리는 API를 만들고, 요청 중에 서버를 종료하면 다음과 같은 로그를 확인할 수 있습니다.</p>
<pre><code class="language-text">INFO ... GracefulShutdown : Commencing graceful shutdown. Waiting for active requests to complete
INFO ... GracefulShutdownController : 요청 처리 완료 seq : (1)
INFO ... GracefulShutdown : Graceful shutdown complete</code></pre>
<p>서버 종료 신호가 들어왔음에도 <code>Waiting for active requests to complete</code> 메시지와 함께 기존 요청 처리가 완료될 때까지 기다렸다가 종료됩니다.</p>
<h3 id="코드-레벨-분석">코드 레벨 분석</h3>
<p>Spring Boot가 구동될 때 <code>server.shutdown=graceful</code> 설정이 되어 있다면, <code>TomcatWebServer</code>는 <code>GracefulShutdown</code> 객체를 생성하여 할당합니다.</p>
<pre><code class="language-java">final class GracefulShutdown {
    // ...
    private void doShutdown(GracefulShutdownCallback callback) {
        List&lt;Connector&gt; connectors = getConnectors();

        // 1. 커넥터 종료 (새로운 요청 차단)
        connectors.forEach(this::close); 

        try {
            for (Container host : this.tomcat.getEngine().findChildren()) {
                for (Container context : host.findChildren()) {
                    // 2. 활성화된 요청이 있는지 지속적으로 확인
                    while (isActive(context)) {
                        if (this.aborted) {
                            logger.info(&quot;Graceful shutdown aborted...&quot;);
                            callback.shutdownComplete(GracefulShutdownResult.REQUESTS_ACTIVE);
                            return;
                        }
                        Thread.sleep(50); // 50ms 주기로 체크
                    }
                }
            }
        } catch (InterruptedException ex) {
            Thread.currentThread().interrupt();
        }
        // ...
    }
}</code></pre>
<h4 id="1-connector-close의-의미">1. Connector Close의 의미</h4>
<p><code>connectors.forEach(this::close)</code>가 실행되면 Tomcat은 새로운 TCP 연결 수립을 거부합니다. 하지만 <strong>중요한 점은 기존 Keep-Alive 상태의 연결</strong>입니다. Tomcat은 새로운 요청은 막지만, 이미 맺어진 Keep-Alive 연결을 통해 들어와 처리 중인 요청은 끊지 않고 유지합니다. 이후 해당 요청 처리가 끝나면 연결을 자연스럽게 종료합니다.</p>
<h4 id="2-isactive는-무엇을-확인하는가">2. isActive()는 무엇을 확인하는가?</h4>
<p><code>isActive(context)</code> 메서드는 단순히 &quot;무언가 돌고 있다&quot;를 추측하는 것이 아닙니다. 구체적으로는 Tomcat 내부의 <strong><code>StandardWrapperValve</code></strong> 클래스가 관리하는 <strong><code>processingCount</code></strong> 값을 확인합니다.</p>
<ul>
<li><code>processingCount &gt; 0</code>: 현재 서블릿이 요청을 처리 중임</li>
<li><code>processingCount == 0</code>: 처리 중인 요청 없음 (종료 가능)</li>
</ul>
<p>즉, Spring Boot의 Graceful Shutdown은 이 카운트가 0이 될 때까지(혹은 타임아웃이 될 때까지) 루프를 돌며 대기하는 구조입니다.</p>
<h2 id="마치며">마치며</h2>
<p>Graceful Shutdown은 단순한 코드 설정 한 줄(<code>server.shutdown=graceful</code>)로 시작하지만, 그 뒤에는 OS 시그널, 웹 서버의 커넥션 관리, 그리고 인프라 레이어의 트래픽 제어까지 연결된 깊이 있는 기술이 숨어 있습니다.</p>
<p>안정적인 서비스를 운영하기 위해서는 코드 레벨의 설정뿐만 아니라, 로드밸런서와 배포 환경(K8s 등)의 종료 정책을 함께 고려하여 &quot;진정한 우아한 종료&quot;를 설계해야 합니다.</p>
<p>참고) 
<a href="https://velog.io/@byeongju/SpringBoot%EC%9D%98-Graceful-Shutdown">https://velog.io/@byeongju/SpringBoot%EC%9D%98-Graceful-Shutdown</a>
<a href="https://effectivesquid.tistory.com/entry/JVM%EC%9D%98-%EC%A2%85%EB%A3%8C%EC%99%80-Graceful-Shutdown">https://effectivesquid.tistory.com/entry/JVM%EC%9D%98-%EC%A2%85%EB%A3%8C%EC%99%80-Graceful-Shutdown</a>
<a href="https://www.baeldung.com/spring-boot-web-server-shutdown">https://www.baeldung.com/spring-boot-web-server-shutdown</a></p>