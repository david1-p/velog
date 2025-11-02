<h1 id="2025년-기준-도커docker와-쿠버네티스kubernetes-핵심-요약">[2025년 기준] 도커(Docker)와 쿠버네티스(Kubernetes) 핵심 요약</h1>
<hr />
<h2 id="1-도커-docker">1. 도커 (Docker)</h2>
<h3 id="도커란">도커란?</h3>
<p>도커는 애플리케이션을 <strong>컨테이너</strong>라는 격리된 환경에 패키징하여 실행하는 기술 및 플랫폼입니다. 컨테이너에는 애플리케이션 실행에 필요한 모든 환경(코드, 라이브러리, 설정)이 포함되어 있어, &quot;제 PC에서는 됐는데...&quot; 하는 문제를 원천적으로 해결합니다.</p>
<p>도커 사용법은 크게 <strong>도커 이미지</strong>를 다루는 것과 <strong>컨테이너</strong>를 다루는 것으로 나뉩니다.</p>
<h3 id="도커-이미지-docker-image">도커 이미지 (Docker Image)</h3>
<p>도커 이미지는 컨테이너를 실행하기 위한 <strong>읽기 전용 템플릿</strong>입니다.</p>
<ul>
<li>운영 체제의 최소한의 파일 시스템 (예: Alpine Linux)</li>
<li>애플리케이션 소스 코드 및 바이너리</li>
<li>애플리케이션 실행에 필요한 의존성(라이브러리, 도구)</li>
<li>실행 환경 설정 정보 (예: 어떤 명령어로 앱을 시작할지)</li>
</ul>
<p>이 모든 것을 포함하는 아카이브입니다. 이미지를 만드는 과정을 <strong>빌드(build)</strong>라고 하며, 컨테이너는 이 빌드된 이미지를 기반으로 실행됩니다.</p>
<h4 id="주요-이미지-명령어">주요 이미지 명령어</h4>
<p><strong>1. <code>docker image build</code> - 이미지 빌드</strong></p>
<ul>
<li><p>`-t: 이미지명과 태그(버전)를 지정합니다. '.'은 현재 디렉터리의 Dockerfile을 사용하라는 의미입니다.
ex) docker image build -t 내이미지명:태그명 .</p>
</li>
<li><p><code>-f</code>: 특정 파일명을 가진 Dockerfile을 지정할 수 있습니다.</p>
</li>
<li><p><code>--pull</code>: 빌드 시 캐시된 베이스 이미지를 사용하지 않고, 레지스트리에서 항상 새로 받아옵니다. (최신 보안 패치 적용 시 유용)</p>
</li>
</ul>
<p><strong>2. <code>docker image pull</code></strong> - 이미지 내려받기 (레지스트리에서 로컬로)</p>
<p><strong>3. <code>docker image ls</code></strong> - 로컬에 보유한 도커 이미지 목록 보기</p>
<p><strong>4. <code>docker image tag</code></strong> - 이미지에 추가 태그 붙이기 (예: <code>my-image:latest</code> -&gt; <code>my-image:v1.0</code>)</p>
<p><strong>5. <code>docker image push</code></strong> - 이미지를 외부 레지스트리(예: Docker Hub)에 공개(업로드)하기</p>
<p><strong>🚫 <code>docker search</code> 명령어에 대한 주의</strong></p>
<blockquote>
<p>CLI의 <code>docker search</code> 명령어는 사용이 권장되지 않습니다. 보안이 검증되지 않은 이미지가 많고 신뢰도를 파악하기 어렵습니다.</p>
<p><strong>(모범 사례)</strong>: Docker Hub, GCR, ECR 등 신뢰할 수 있는 웹 레지스트리에서 <strong>'Official Image'</strong> 또는 <strong>'Verified Publisher'</strong> 배지가 붙은 이미지를 검색하여 사용합니다.</p>
</blockquote>
<hr />
<h3 id="도커-컨테이너-docker-container">도커 컨테이너 (Docker Container)</h3>
<p>컨테이너는 도커 이미지를 <strong>실행한 인스턴스</strong>입니다. 이미지는 템플릿(설계도)이고, 컨테이너는 그 설계도로 만든 실제 동작하는 개체(집)입니다.</p>
<h4 id="컨테이너의-생애-주기-lifecycle">컨테이너의 생애 주기 (Lifecycle)</h4>
<p><strong>1) 실행 중 (Running)</strong>
<code>docker container run</code> 명령으로 이미지를 기반으로 컨테이너가 생성된 상태입니다. Dockerfile의 <code>CMD</code>나 <code>ENTRYPOINT</code>에 정의된 애플리케이션이 실행됩니다.</p>
<p><strong>2) 정지 (Stopped)</strong>
사용자가 명시적으로 정지(<code>stop</code>)시키거나, 컨테이너의 메인 애플리케이션이 종료되면 컨테이너는 '정지' 상태가 됩니다. 가상 환경은 동작하지 않지만, 종료 시점의 상태(데이터 변경분)가 디스크에 저장됩니다.</p>
<p><strong>3) 파기 (Destroyed)</strong>
<code>rm</code> 명령 등으로 파기된 상태입니다. 한 번 파기한 컨테이너는 다시 실행할 수 없습니다.</p>
<h4 id="주요-컨테이너-명령어">주요 컨테이너 명령어</h4>
<ul>
<li><code>docker container run</code>: 이미지로부터 컨테이너를 생성하고 실행합니다.</li>
<li><code>docker container ls</code>: 실행 중인 컨테이너 목록 보기 (<code>-a</code> 옵션으로 정지된 것 포함)</li>
<li><code>docker container stop</code>: 컨테이너를 정지합니다.</li>
<li><code>docker container restart</code>: 컨테이너를 재시작합니다.</li>
<li><code>docker container rm</code>: 컨테이너를 파기합니다.</li>
<li><code>docker container logs</code>: 컨테이너의 표준 출력을 확인합니다. (<code>-f</code> 옵션으로 실시간 추적)</li>
<li><code>docker container exec</code>: 실행 중인 컨테이너에서 추가 명령을 실행합니다. (예: <code>exec -it [이름] /bin/bash</code>)</li>
<li><code>docker container cp</code>: 호스트와 컨테이너 간 파일을 복사합니다.</li>
</ul>
<h4 id="리소스-정리-명령어">리소스 정리 명령어</h4>
<ul>
<li><code>docker container prune</code>: 실행 중이 아닌 모든 컨테이너를 삭제합니다.</li>
<li><code>docker image prune</code>: 태그가 붙지 않은(dangling) 모든 이미지를 삭제합니다.</li>
<li><code>docker system prune</code>: 사용하지 않는 컨테이너, 이미지, 볼륨, 네트워크 등 모든 도커 리소스를 일괄 삭제합니다.</li>
</ul>
<hr />
<h3 id="docker-compose-v2">Docker Compose (v2)</h3>
<p><code>docker compose</code>는 <strong>여러 컨테이너로 구성된 애플리케이션</strong>을 정의하고 실행하기 위한 도구입니다. (2019년의 <code>docker-compose</code> v1과 달리, 현재는 Docker CLI에 <code>docker compose</code> v2로 통합되었습니다.)</p>
<p><code>docker-compose.yml</code> 파일 하나로 웹 서버, 데이터베이스, 캐시 서버 등을 한꺼번에 관리할 수 있습니다.</p>
<ul>
<li><code>docker compose up</code>: <code>yml</code> 파일에 정의된 모든 컨테이너를 생성하고 시작합니다. (<code>-d</code>로 백그라운드 실행)</li>
<li><code>docker compose down</code>: <code>yml</code> 파일로 생성된 컨테이너, 네트워크, 볼륨을 중지하고 삭제합니다.</li>
<li><code>docker compose ps</code>: 현재 compose로 실행 중인 컨테이너 상태를 봅니다.</li>
<li><code>docker compose logs -f</code>: 모든 컨테이너의 로그를 실시간으로 봅니다.</li>
</ul>
<blockquote>
<p><strong>(참고)</strong>: 최신 <code>docker-compose.yml</code> 파일은 상단에 <code>version: &quot;3.x&quot;</code> 선언이 필수가 아닙니다.</p>
</blockquote>
<hr />
<hr />
<h2 id="2-쿠버네티스-kubernetes">2. 쿠버네티스 (Kubernetes)</h2>
<h3 id="쿠버네티스란">쿠버네티스란?</h3>
<p>쿠버네티스(K8s)는 <strong>컨테이너 오케스트레이션 도구</strong>입니다. &quot;오케스트레이션&quot;이란, 수십, 수백 개의 컨테이너를 대규모 프로덕션 환경에서 안정적으로 운영(배포, 스케일링, 복구)하는 작업을 자동화하는 것을 의미합니다.</p>
<ul>
<li><strong>주요 기능:</strong><ul>
<li><strong>서비스 디스커버리 및 로드 밸런싱</strong>: 컨테이너에 고유한 IP와 DNS 이름을 부여하고, 트래픽을 분산합니다.</li>
<li><strong>자동화된 롤아웃 및 롤백</strong>: 새 버전 배포 시 문제 생기면 자동으로 이전 버전으로 롤백합니다.</li>
<li><strong>자동화된 빈 패킹(Bin Packing)</strong>: 컨테이너가 필요한 리소스(CPU, Mem)를 계산하여 최적의 서버(노드)에 배치합니다.</li>
<li><strong>자가 치유 (Self-healing)</strong>: 실행 중이던 컨테이너가 죽으면 자동으로 재시작하거나 교체합니다.</li>
<li><strong>스케일링</strong>: 필요에 따라 컨테이너 개수를 자동으로 늘리거나 줄입니다.</li>
</ul>
</li>
</ul>
<h3 id="중요-쿠버네티스와-도커의-관계-2025년-기준">(중요) 쿠버네티스와 도커의 관계 (2025년 기준)</h3>
<p>과거에는 쿠버네티스가 도커 데몬을 직접 제어(Dockershim 경유)했지만, <strong>K8s v1.24 (2022년)부터 <code>Dockershim</code>이 완전히 제거되었습니다.</strong></p>
<ul>
<li><strong>현재</strong>: 쿠버네티스는 <strong>CRI(Container Runtime Interface)</strong>라는 표준을 따르는 런타임과 통신합니다. 대표적으로 <code>containerd</code>나 <code>CRI-O</code>가 있습니다.</li>
<li><strong>결론</strong>: 개발자는 <code>docker build</code>로 이미지를 만들 수 있습니다. 이 이미지는 <strong>CRI 호환 런타임이라면 어디서든 실행 가능</strong>하며, 쿠버네티스는 이 이미지를 <code>containerd</code>를 이용해 실행합니다. (참고: 도커 데스크톱도 내부적으로 <code>containerd</code>를 사용합니다.)</li>
</ul>
<h3 id="쿠버네티스-핵심-리소스">쿠버네티스 핵심 리소스</h3>
<p>쿠버네티스에서 다루는 모든 것을 '리소스'라고 부릅니다. 이 리소스들은 주로 <code>yaml</code> 매니페스트 파일로 정의됩니다.</p>
<table>
<thead>
<tr>
<th align="center">리소스</th>
<th align="left">용도</th>
</tr>
</thead>
<tbody><tr>
<td align="center"><strong>노드 (Node)</strong></td>
<td align="left">컨테이너가 배치되는 <strong>물리/가상 서버</strong> (워커 머신)</td>
</tr>
<tr>
<td align="center"><strong>네임스페이스 (Namespace)</strong></td>
<td align="left">쿠버네티스 클러스터 안의 <strong>가상 클러스터</strong> (논리적 분리)</td>
</tr>
<tr>
<td align="center"><strong>파드 (Pod)</strong></td>
<td align="left">K8s의 <strong>가장 작은 배포 단위</strong>. 하나 이상의 컨테이너 집합.</td>
</tr>
<tr>
<td align="center"><strong>레플리카세트 (ReplicaSet)</strong></td>
<td align="left">파드 복제본(Replica)의 개수를 항상 일정하게 유지.</td>
</tr>
<tr>
<td align="center"><strong>디플로이먼트 (Deployment)</strong></td>
<td align="left"><strong>(가장 중요)</strong> 배포의 기본 단위. 레플리카세트의 리비전(버전)을 관리하며 롤아웃/롤백 수행.</td>
</tr>
<tr>
<td align="center"><strong>서비스 (Service)</strong></td>
<td align="left">여러 파드에 대한 <strong>고정된 진입점(IP/DNS)</strong>을 제공. 로드 밸런싱 담당.</td>
</tr>
<tr>
<td align="center"><strong>인그레스 (Ingress)</strong></td>
<td align="left"><strong>L7 (HTTP/S)</strong>. 클러스터 외부 요청을 내부 서비스로 연결 (경로/도메인 기반 라우팅)</td>
</tr>
<tr>
<td align="center"><strong>Gateway API</strong></td>
<td align="left">인그레스의 차세대 표준. 더 강력하고 유연한 L7 라우팅 제공.</td>
</tr>
<tr>
<td align="center"><strong>컨피그맵 (ConfigMap)</strong></td>
<td align="left">설정 정보를 Key-Value 형태로 저장하여 파드에 주입.</td>
</tr>
<tr>
<td align="center"><strong>시크릿 (Secret)</strong></td>
<td align="left">API 키, DB 패워드 등 기밀 정보를 Base64 인코딩하여 저장.</td>
</tr>
<tr>
<td align="center">퍼시스턴트볼륨 (PV)</td>
<td align="left">파드가 사용할 영구 저장소(스토리지) 자체를 정의 (관리자)</td>
</tr>
<tr>
<td align="center">퍼시스턴트볼륨클레임 (PVC)</td>
<td align="left">스토리지 사용 요청 (개발자)</td>
</tr>
<tr>
<td align="center"><strong>스테이트풀세트 (StatefulSet)</strong></td>
<td align="left">상태(state)를 가지는 파드(e.g., DB)를 위한 리소스. (고유 ID, 순차적 배포)</td>
</tr>
<tr>
<td align="center"><strong>잡 (Job)</strong></td>
<td align="left">일회성 작업을 실행하고 <strong>정상적인 종료</strong>를 보장.</td>
</tr>
<tr>
<td align="center"><strong>크론잡 (CronJob)</strong></td>
<td align="left">리눅스 Cron처럼 스케줄링(예: 매일 밤 12시)되는 잡.</td>
</tr>
<tr>
<td align="center"><strong>PodSecurityAdmission (PSA)</strong></td>
<td align="left">K8s의 내장 보안 표준. 파드가 특정 보안 수준(e.g., privileged 금지)을 준수하도록 강제.</td>
</tr>
</tbody></table>
<hr />
<h3 id="파드-pod">파드 (Pod)</h3>
<p>파드는 쿠버네티스에서 생성하고 관리할 수 있는 <strong>가장 작은 배포 단위</strong>이며, 하나 이상의 컨테이너로 이루어집니다.</p>
<ul>
<li><strong>특징</strong>:<ul>
<li>같은 파드 안의 컨테이너는 <strong>같은 노드</strong>에 배치됩니다.</li>
<li><strong>네트워크와 스토리지(볼륨)를 공유</strong>합니다. (예: <code>localhost</code>로 통신 가능)</li>
</ul>
</li>
<li><strong>활용 예</strong>:<ul>
<li>(Sidecar 패턴) 메인 앱 컨테이너 + 로그 수집 컨테이너</li>
</ul>
</li>
</ul>
<blockquote>
<p><strong>(참고)</strong>: 실무에서는 <code>Pod</code>를 직접 생성(Naked Pod)하는 경우는 드물며, <code>Deployment</code>나 <code>StatefulSet</code>을 통해 관리하는 것이 표준입니다. <code>Pod</code> 매니페스트는 <code>Deployment</code>의 <code>spec.template</code> 부분에서 핵심적인 역할을 합니다.</p>
</blockquote>
<p>▼ <code>Pod</code> 매니페스트 예시 (<code>simple-pod.yaml</code>)</p>
<pre><code class="language-yaml">apiVersion: v1
kind: Pod
metadata:
  name: simple-nginx-pod
  labels:
    app: webserver
spec:
  containers:
    - name: nginx-container
      image: nginx:latest
      ports:
        - containerPort: 80</code></pre>
<hr />
<h3 id="배포-리소스-deployment-replicaset">배포 리소스 (Deployment, ReplicaSet)</h3>
<p><strong>레플리카세트 (ReplicaSet)</strong></p>
<blockquote>
<p>파드(kind: Pod)는 죽으면 그걸로 끝입니다. 고가용성을 위해 '레플리카세트'를 사용하면, 지정된 수만큼 파드의 개수를 항상 유지(self-healing)합니다.</p>
</blockquote>
<p><strong>디플로이먼트 (Deployment)</strong></p>
<blockquote>
<p><strong>(핵심)</strong> 실제 운영에서는 레플리카세트를 직접 다루기보다, 그 상위 리소스인 '디플로이먼트'를 사용합니다. 디플로이먼트는 <strong>애플리케이션 배포의 표준 단위</strong>입니다.</p>
<p>디플로이먼트는 내부적으로 레플리카세트를 관리하며, 앱을 <strong>업데이트(롤링 업데이트)</strong>하거나 <strong>롤백</strong>하는 등 리비전(버전) 관리를 담당합니다.</p>
</blockquote>
<p>▼ <code>Deployment</code> 매니페스트 예시 (<code>deployment.yaml</code>)</p>
<pre><code class="language-yaml">apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
  labels:
    app: my-web-app
spec:
  replicas: 3 # 3개의 파드를 실행
  selector:
    matchLabels:
      app: my-web-app # 이 label을 가진 Pod를 찾아서 관리
  template: # &lt;-- Pod 템플릿 (여기서 Pod를 정의)
    metadata:
      labels:
        app: my-web-app # Service가 이 label을 보고 파드를 찾음
    spec:
      containers:
        - name: my-app-container
          image: nginx:latest # 실제 사용할 컨테이너 이미지
          ports:
            - containerPort: 80</code></pre>
<hr />
<h3 id="🌐-네트워크-리소스-service-ingress-gateway">🌐 네트워크 리소스 (Service, Ingress, Gateway)</h3>
<p><strong>서비스 (Service)</strong></p>
<blockquote>
<p>'서비스'는 여러 파드 그룹에 접근할 수 있는 <strong>고유한 단일 진입점(Entrypoint)</strong>을 제공하는 리소스입니다. (내부 DNS 이름 및 고정 IP 제공)</p>
</blockquote>
<p><strong>1) ClusterIP (기본값)</strong></p>
<ul>
<li>쿠버네티스 클러스터 <strong>내부</strong> IP 주소에만 서비스를 공개합니다.</li>
<li>클러스터 내의 다른 파드들이 서비스 이름(DNS)으로 접근할 때 사용됩니다.</li>
</ul>
<p>▼ <code>Service</code> (ClusterIP) 매니페스트 예시 (<code>service-clusterip.yaml</code>)</p>
<pre><code class="language-yaml">apiVersion: v1
kind: Service
metadata:
  name: my-app-clusterip-svc
spec:
  type: ClusterIP # 클러스터 내부 IP만 할당 (기본값)
  selector:
    # 'app: my-web-app' label을 가진 파드에 트래픽 전달
    # (위 Deployment 예시의 template.metadata.labels와 일치)
    app: my-web-app 
  ports:
    - protocol: TCP
      port: 80       # 서비스가 80번 포트로 노출됨
      targetPort: 80 # 파드의 80번 포트로 트래픽 전달</code></pre>
<p><strong>2) NodePort</strong></p>
<ul>
<li>ClusterIP의 기능을 포함하며, 추가로 모든 <strong>노드(Node)</strong>의 특정 포트를 개방합니다.</li>
<li>외부에서 <code>[노드 IP]:[NodePort]</code>로 접근할 수 있게 됩니다. (주로 테스트용)</li>
</ul>
<p><strong>3) LoadBalancer</strong></p>
<ul>
<li>ClusterIP, NodePort의 기능을 포함합니다.</li>
<li>클라우드 플랫폼(GCP, AWS 등)에서 제공하는 <strong>외부 로드 밸런서</strong>와 연동됩니다.</li>
</ul>
<p>▼ <code>Service</code> (LoadBalancer) 매니페스트 예시 (<code>service-loadbalancer.yaml</code>)</p>
<pre><code class="language-yaml">apiVersion: v1
kind: Service
metadata:
  name: my-app-loadbalancer-svc
spec:
  type: LoadBalancer # 클라우드 로드밸런서와 연동 (외부 노출용)
  selector:
    app: my-web-app # 'app: my-web-app' label을 가진 파드에 트래픽 전달
  ports:
    - protocol: TCP
      port: 80       # 로드밸런서의 80번 포트
      targetPort: 80 # 파드의 80번 포트로 트래픽 전달</code></pre>
<p><strong>인그레스 (Ingress) &amp; 게이트웨이 API (Gateway API)</strong></p>
<blockquote>
<p><code>Service</code>가 L4에서 동작하는 반면, <code>Ingress</code>는 L7(HTTP/S) 레벨에서 <strong>경로/도메인 기반 라우팅</strong>을 제공합니다. 최근에는 <code>Ingress</code>의 한계를 극복한 차세대 표준인 <strong><code>Gateway API</code></strong>의 사용이 증가하고 있습니다.</p>
</blockquote>
<p>▼ <code>Ingress</code> 매니페스트 예시 (<code>ingress.yaml</code>)</p>
<pre><code class="language-yaml">apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-app-ingress
  annotations:
    # (예시) Nginx Ingress Controller를 사용할 경우
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
    - host: &quot;myapp.example.com&quot; # 이 도메인으로 오는 요청을 처리
      http:
        paths:
          - path: / # '/' 경로로 오는 모든 요청
            pathType: Prefix
            backend:
              service:
                # 위에서 만든 ClusterIP 서비스로 라우팅
                name: my-app-clusterip-svc 
                port:
                  number: 80</code></pre>
<hr />
<h3 id="⚙️-기타-주요-리소스">⚙️ 기타 주요 리소스</h3>
<p><strong>컨피그맵 (ConfigMap)</strong></p>
<blockquote>
<p><code>ConfigMap</code>은 설정 정보를 Key-Value 형태로 저장하여 파드에 환경변수나 파일로 주입합니다.</p>
</blockquote>
<p>▼ <code>ConfigMap</code> 매니페스트 예시 (<code>configmap.yaml</code>)</p>
<pre><code class="language-yaml">apiVersion: v1
kind: ConfigMap
metadata:
  name: my-app-config
data:
  # Key-Value 형태의 설정 데이터
  APP_COLOR: &quot;blue&quot;
  APP_MODE: &quot;production&quot;
  GREETING: &quot;Hello from ConfigMap!&quot;</code></pre>
<p><strong>시크릿 (Secret) &amp; 모범 사례</strong></p>
<blockquote>
<p><code>Secret</code> 리소스는 TLS 인증서, API 키, 패스워드 등 기밀 정보를 다룹니다. 데이터는 <strong>Base64로 인코딩</strong>되어 저장됩니다.</p>
<p><strong>(주의)</strong>: Base64는 암호화가 아닙니다. 누구나 디코딩할 수 있습니다.
<strong>(모범 사례)</strong>: 프로덕션 환경에서는 K8s <code>Secret</code>에 기밀 정보를 직접 저장하기보다, <strong><code>Vault</code></strong>나 <strong><code>AWS/GCP Secrets Manager</code></strong> 같은 외부 시크릿 관리 도구와 연동하여 파드가 실행될 때 동적으로 시크릿을 주입받는 방식을 사용합니다.</p>
</blockquote>
<p>▼ <code>Secret</code> 매니페스트 예시 (<code>secret.yaml</code>)</p>
<pre><code class="language-yaml">apiVersion: v1
kind: Secret
metadata:
  name: my-app-secret
type: Opaque # 가장 일반적인 Secret 타입
data:
  # 값들은 반드시 Base64로 인코딩되어야 함
  # &quot;admin&quot; -&gt; base64 -&gt; &quot;YWRtaW4=&quot;
  # &quot;my-db-password123&quot; -&gt; base64 -&gt; &quot;bXktZGItcGFzc3dvcmQxMjM=&quot;
  DATABASE_USER: &quot;YWRtaW4=&quot;
  DATABASE_PASSWORD: &quot;bXktZGItcGFzc3dvcmQxMjM=&quot;</code></pre>
<p><strong>잡 (Job) &amp; 크론잡 (CronJob)</strong></p>
<ul>
<li><strong>Job</strong>: 일회성 작업을 위한 리소스. 파드가 '정상 종료(Exit Code 0)'될 때까지 관리합니다.</li>
<li><strong>CronJob</strong>: 잡(Job)을 스케줄(예: <code>0 5 * * *</code>)에 따라 정기적으로 실행합니다.</li>
</ul>
<p>▼ <code>Job</code> 매니페스트 예시 (<code>job.yaml</code>)</p>
<pre><code class="language-yaml">apiVersion: batch/v1
kind: Job
metadata:
  name: my-onetime-job
spec:
  template: # Job이 실행할 Pod 템플릿
    spec:
      containers:
        - name: pi-calculator
          image: perl:latest
          # 2000자리 파이(pi) 계산 후 종료하는 일회성 작업
          command: [&quot;perl&quot;,  &quot;-Mbignum=bpi&quot;, &quot;-wle&quot;, &quot;print bpi(2000)&quot;]
      restartPolicy: Never # Job은 재시작(Restart)하지 않고 실패(Failure) 시 재시도(Backoff)
  backoffLimit: 4 # 4번까지 재시도</code></pre>
<p>▼ <code>CronJob</code> 매니페스트 예시 (<code>cronjob.yaml</code>)</p>
<pre><code class="language-yaml">apiVersion: batch/v1
kind: CronJob
metadata:
  name: my-nightly-backup
spec:
  schedule: &quot;0 2 * * *&quot; # 매일 새벽 2시 0분에 실행 (Cron 표현식)
  jobTemplate: # CronJob이 생성할 Job 템플릿
    spec:
      template:
        spec:
          containers:
            - name: backup-script
              image: busybox:latest
              command: [&quot;/bin/sh&quot;, &quot;-c&quot;, &quot;echo 'Running nightly backup...'&quot;]
          restartPolicy: OnFailure # 실패 시에만 재시작</code></pre>
<hr />
<h3 id="🚢-헬름-helm">🚢 헬름 (Helm)</h3>
<blockquote>
<p>헬름은 <strong>쿠버네티스의 패키지 관리자</strong>입니다.</p>
<p>하나의 앱을 배포하려면 <code>Deployment</code>, <code>Service</code>, <code>ConfigMap</code>, <code>Ingress</code> 등 수많은 리소스가 필요합니다. 헬름은 이 리소스들의 묶음을 <strong>'차트(Chart)'</strong>라는 패키지로 만들고, 환경별로 달라지는 설정값(DB 주소, 도메인 등)만 변수 처리하여 쉽게 배포/관리/업그레이드할 수 있게 도와줍니다.</p>
</blockquote>
<hr />
<h3 id="🔬-부하-테스트-locust">🔬 부하 테스트 (Locust)</h3>
<blockquote>
<p>도커는 부하 테스트에도 유용하게 사용됩니다. <strong>Locust</strong>는 파이썬으로 구현된 부하 테스트 도구로, 테스트 시나리오를 코드로 작성할 수 있습니다. 도커와 쿠버네티스를 이용해 Locust 컨테이너를 수십~수백 개로 스케일 아웃하여 대규모 분산 부하 테스트를 수행할 수 있습니다.</p>
</blockquote>