<h2 id="정적-ip-주소-vs-동적-ip-주소-백엔드-개발자를-위한-핵심-정리">정적 IP 주소 vs 동적 IP 주소: 백엔드 개발자를 위한 핵심 정리</h2>
<p>네트워크에서 호스트(컴퓨터, 서버 등)에게 IP를 할당하는 방식은 크게 <strong>정적(Static) 할당 방식</strong>과 <strong>동적(Dynamic) 할당 방식</strong>으로 나뉩니다. 백엔드 시스템을 구성할 때 이 두 가지 방식의 차이점을 이해하는 것은 매우 중요합니다.</p>
<hr />
<h2 id="1-정적-ip-주소-할당-static-ip-allocation">1. 정적 IP 주소 할당 (Static IP Allocation)</h2>
<p><strong>정적 할당 방식</strong>은 호스트에게 IP 주소를 <strong>수동으로 직접 설정</strong>하는 것을 의미합니다.</p>
<p>일반적으로 IP를 정적으로 할당하기 위해서는 다음 정보가 필요합니다.</p>
<ul>
<li>부여하고자 하는 IP 주소</li>
<li>네트워크의 서브넷 마스크 (Subnet Mask)</li>
<li>게이트웨이 (Gateway) 주소</li>
<li>DNS 서버 주소</li>
</ul>
<blockquote>
<p><strong>정적 할당의 한계:</strong>
만약 모든 IP 주소를 정적으로만 할당한다면, 호스트(장비)의 수가 많아질수록 IP 할당 작업이 매우 번거로워질 수 있습니다. 또한, 관리자가 실수로 <strong>중복된 IP를 입력하는 등 실수를 유발</strong>할 가능성이 큽니다.</p>
</blockquote>
<hr />
<h2 id="2-동적-ip-주소-할당-dynamic-ip-allocation">2. 동적 IP 주소 할당 (Dynamic IP Allocation)</h2>
<p><strong>동적 할당 방식</strong>은 정적 할당의 번거로움을 해결하기 위해 등장했습니다. 이름 그대로 <strong>IP를 자동으로 할당</strong>하는 방식이며, 주로 <strong>DHCP(Dynamic Host Configuration Protocol)</strong>라는 프로토콜을 사용합니다.</p>
<ul>
<li><strong>작동 방식:</strong> DHCP는 네트워크 내에서 <strong>사용하지 않는 IP를 찾아 호스트에게 '임대(Lease)'</strong> 해주는 방식으로 작동합니다.</li>
<li><strong>주요 특징:</strong> 동적 할당 방식을 사용하는 경우, IP 주소가 고정적이지 않으며 <strong>임대 기간 만료 등에 따라 바뀔 가능성</strong>이 존재합니다.</li>
<li><strong>버전:</strong> IPv4 환경에서는 DHCPv4, IPv6 환경에서는 DHCPv6가 사용됩니다.</li>
</ul>
<hr />
<h2 id="3-dhcp는-어떻게-ip를-할당할까-dora-4단계">3. DHCP는 어떻게 IP를 할당할까? (DORA 4단계)</h2>
<p>DHCP를 이용한 IP 주소 할당 과정은 <strong>호스트(클라이언트)</strong>와 <strong>DHCP 서버(보통 라우터)</strong> 간의 통신으로 이루어집니다. 이 과정은 <strong>DORA</strong>라고 불리는 4단계로 요약할 수 있습니다.</p>
<h3 id="1-discover-탐색">1) Discover (탐색)</h3>
<p>호스트는 &quot;DHCP 서버를 찾습니다&quot;라는 <strong>Discover 메시지</strong>를 네트워크 전체에 <strong>브로드캐스팅(Broadcast)</strong>하여 DHCP 서버를 찾습니다.</p>
<h3 id="2-offer-제안">2) Offer (제안)</h3>
<p>Discover 메시지를 수신한 DHCP 서버는 &quot;이 IP 주소를 사용하세요&quot;라는 <strong>Offer 메시지</strong>를 호스트에게 전송합니다. 이 메시지에는 호스트에게 할당해 줄 <strong>IP 주소</strong>와 <strong>임대 기간(Lease Time)</strong>이 포함되어 있습니다.</p>
<h3 id="3-request-요청">3) Request (요청)</h3>
<p>호스트는 여러 서버로부터 Offer를 받을 수 있으며, 그중 하나를 선택하여 &quot;이 IP 주소를 사용하겠습니다&quot;라는 <strong>Request 메시지</strong>를 다시 <strong>브로드캐스팅</strong>합니다.</p>
<h3 id="4-acknowledgment-승인">4) Acknowledgment (승인)</h3>
<p>호스트의 요청을 받은 DHCP 서버는 &quot;요청을 승인합니다&quot;라는 <strong>ACK(Acknowledgment) 메시지</strong>를 호스트에게 전송하여 IP 임대를 최종 승인합니다.</p>
<hr />
<h2 id="4-ip-할당-완료-및-갱신">4. IP 할당 완료 및 갱신</h2>
<p>위의 4가지 과정이 모두 끝나면, 클라이언트는 할당받은 IP 주소를 자신의 IP 주소로 설정하고 정해진 임대 기간 동안 사용할 수 있습니다.</p>
<p>임대 기한이 만료되면 DHCP 과정을 다시 반복해야 하지만, 보통 만료되기 전에 <strong>DHCP 임대 갱신(DHCP Lease Renewal)</strong> 과정을 통해 임대 기간을 연장할 수 있습니다.</p>
<hr />
<h2 id="5-백엔드-서버에-정적-ip를-사용하는-이유">5. 백엔드 서버에 정적 IP를 사용하는 이유</h2>
<p>그렇다면 DHCP를 통한 동적 할당이 편리함에도 불구하고, 왜 백엔드 서버(웹 서버, API 서버, 데이터베이스 서버 등)는 <strong>정적 IP를 사용하는 것이 일반적</strong>일까요?</p>
<p>가장 핵심적인 이유는 <strong>'신뢰성'</strong>과 <strong>'접근성'</strong>입니다.</p>
<ul>
<li><p><strong>고정된 주소의 필요성 (신뢰성):</strong> 백엔드 서버는 클라이언트나 다른 서비스가 요청을 보낼 수 있도록 <strong>항상 동일한 주소</strong>에 위치해야 합니다. 만약 서버의 IP가 DHCP에 의해 계속 바뀐다면, 클라이언트는 서버를 찾을 수 없게 됩니다. 이는 매일 주소가 바뀌는 상점과 같습니다.</p>
</li>
<li><p><strong>DNS 및 도메인 연결:</strong> 우리는 보통 <code>api.example.com</code>과 같은 도메인 이름을 사용하여 서버에 접속합니다. DNS(Domain Name System)는 이 도메인 이름을 서버의 IP 주소로 변환해주는 역할을 합니다. 서버 IP가 정적이면, 이 DNS 레코드를 한 번만 설정해두면 됩니다. 하지만 IP가 동적으로 바뀐다면, 바뀔 때마다 DNS 레코드를 수동으로 또는 복잡한 (DDNS) 설정으로 갱신해야 하는 큰 문제가 발생합니다.</p>
</li>
<li><p><strong>서비스 간 의존성:</strong> 현대의 백엔드 아키텍처(예: 마이크로서비스)에서는 여러 서버가 서로 통신합니다. 예를 들어, 웹 서버는 데이터베이스 서버의 IP 주소를 알고 있어야 데이터를 요청할 수 있습니다. 만약 데이터베이스 서버의 IP가 동적으로 변경된다면, 웹 서버는 연결에 실패하고 전체 서비스가 중단될 것입니다.</p>
</li>
<li><p><strong>보안 및 방화벽 설정:</strong> 보안을 위해 방화벽에서는 특정 IP 주소의 접근만 허용하는 규칙(Access Control List)을 설정하는 경우가 많습니다. (예: &quot;오직 '1.2.3.4' IP를 가진 관리자 서버만 DB 서버에 접근할 수 있다.&quot;) 서버의 IP가 정적이어야만 이러한 IP 기반 보안 규칙을 안정적으로 운영할 수 있습니다.</p>
</li>
</ul>
<blockquote>
<p><strong>요약:</strong> 동적 IP는 네트워크에 '접속'하는 것이 목적인 <strong>클라이언트(사용자 PC, 스마트폰)</strong>에 적합하고, 정적 IP는 '서비스를 제공'하는 것이 목적인 <strong>서버</strong>에 필수적입니다.</p>
</blockquote>