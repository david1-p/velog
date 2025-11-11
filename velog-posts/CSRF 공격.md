<blockquote>
<p>파이널 프로젝트를 할 때, 보안 관련해서 CSRF 대비 로직을 구현한 것을 본 적이 있다. 생소한 개념이라 알아보고자 한다. </p>
</blockquote>
<h1 id="csrf-공격이란-무엇이며-어떻게-방어할까요">CSRF 공격이란 무엇이며, 어떻게 방어할까요?</h1>
<h2 id="1-csrf-cross-site-request-forgery란">1. CSRF (Cross-Site Request Forgery)란?</h2>
<p>사이트 간 요청 위조(Cross-site Request Forgery, <strong>CSRF</strong>) 공격은 사용자가 자신의 의지와 상관없이 <strong>공격자가 의도한 행위를 특정 웹사이트에 요청</strong>하도록 하는 것을 의미합니다.</p>
<p>즉, 사용자가 <code>david1p.com</code>에 로그인하여 인증 쿠키를 발급받은 상태에서, 공격자가 만든 악성 사이트에 접속했을 때, 해당 사이트가 사용자의 쿠키를 도용하여 <code>david1p.com</code>에 사용자가 원치 않는 요청(결제, 비밀번호 변경 등)을 보내는 공격입니다.</p>
<hr />
<h2 id="2-csrf-공격-시나리오">2. CSRF 공격 시나리오</h2>
<p>CSRF 공격이 어떻게 이루어지는지 <code>david1p.com</code>이라는 가상의 사이트를 예로 들어 구체적인 시나리오를 살펴보겠습니다.</p>
<ol>
<li><strong>로그인 및 쿠키 발급</strong>: 사용자가 <strong><code>david1p.com</code></strong> 서비스에 정상적으로 로그인을 수행합니다.</li>
<li><strong>쿠키 저장</strong>: 서버는 해당 사용자에 대한 인증 정보(예: 세션 ID)를 <code>Set-Cookie</code> 헤더에 담아 응답합니다. 사용자의 브라우저는 이 쿠키를 저장하고, 이후 <strong><code>david1p.com</code></strong> 에 요청을 보낼 때마다 해당 쿠키를 자동으로 함께 전달합니다.</li>
<li><strong>악성 페이지 접속 유도</strong>: 공격자는 악성 스크립트가 담긴 페이지(예: <code>attacker.com</code>)를 만들어 사용자에게 접속하도록 유도합니다. (이메일, 커뮤니티 게시글 링크 등)</li>
<li><strong>악성 요청 전송</strong>: 사용자가 <code>david1p.com</code>에 로그인한 상태(쿠키가 브라우저에 저장된 상태)에서 <code>attacker.com</code>에 접속하면, 페이지 내의 악성 스크립트가 자동으로 실행되어 <code>david1p.com</code> 서버로 강제로 요청을 전송합니다.</li>
<li><strong>서버의 오인</strong>: 이때 브라우저는 정책에 따라 <code>david1p.com</code>으로 요청을 보낼 때 <strong>저장되어 있던 쿠키(세션 ID)를 자동으로 함께</strong> 실어 보냅니다. 서버 입장에서는 이 요청이 정상적인 사용자가 보낸 것인지, <code>attacker.com</code>을 통해 위조된 것인지 구분할 수 없습니다. 쿠키가 유효하므로 서버는 이 요청을 정상적인 사용자의 요청으로 오인하고 처리하게 됩니다.</li>
</ol>
<blockquote>
<p><strong>공격 예시 1: <code>GET</code> 요청을 이용한 공격</strong></p>
<p><code>GET</code> 요청은 <code>img</code> 태그의 <code>src</code> 속성을 이용하는 것만으로도 쉽게 위조할 수 있습니다.</p>
<pre><code class="language-html">&gt; &lt;img src=&quot;[https://david1p.com/member/changePassword?newValue=1234](https://david1p.com/member/changePassword?newValue=1234)&quot; style=&quot;display:none;&quot; /&gt;</code></pre>
<p>사용자가 공격자 사이트에 방문하는 것만으로도, 브라우저는 <code>img</code> 태그를 해석하여 <code>david1p.com</code> 서버로 비밀번호 변경 요청을 (쿠키와 함께) 전송하게 됩니다.</p>
</blockquote>
<blockquote>
<p><strong>공격 예시 2: <code>POST</code> 요청을 이용한 공격 (더 위험)</strong></p>
<p>결제, 회원 탈퇴 등 더 심각한 변경을 유발하는 <code>POST</code> 요청도 위조할 수 있습니다. 공격자는 사용자가 볼 수 없는 <code>iframe</code> 내에 다음과 같은 <code>form</code>을 심어두고, 페이지가 로드될 때 JavaScript로 자동 제출(submit)시킬 수 있습니다.</p>
<pre><code class="language-html">&lt;iframe style=&quot;display:none;&quot;&gt;
  &lt;form id=&quot;csrf_form&quot; action=&quot;[https://david1p.com/payment/transfer](https://david1p.com/payment/transfer)&quot; method=&quot;POST&quot;&gt;
    &lt;input type=&quot;hidden&quot; name=&quot;to_account&quot; value=&quot;attacker_account_number&quot; /&gt;
    &lt;input type=&quot;hidden&quot; name=&quot;amount&quot; value=&quot;1000000&quot; /&gt;
  &lt;/form&gt;
  &lt;script&gt;
    document.getElementById(&quot;csrf_form&quot;).submit();
  &lt;/script&gt;
&lt;/iframe&gt;</code></pre>
<p>사용자가 이 페이지에 접속하는 순간, 자신도 모르게 100만 원을 공격자 계좌로 이체하는 <code>POST</code> 요청이 (쿠키와 함께) <code>david1p.com</code> 서버로 전송됩니다.</p>
</blockquote>
<hr />
<h2 id="3-xss-vs-csrf-간단-비교">3. XSS vs. CSRF (간단 비교)</h2>
<p>많은 입문자가 두 공격을 혼동합니다. 목적과 방식을 간단히 비교하면 다음과 같습니다.</p>
<ul>
<li><strong>XSS (Cross-Site Scripting)</strong>: 공격자가 신뢰받는 사이트(<code>david1p.com</code>)에 <strong>악성 스크립트(Script)를 삽입</strong>하는 공격입니다. 사용자의 브라우저에서 이 스크립트가 실행되어 사용자의 세션 쿠키, 개인 정보 등을 탈취합니다. (신뢰할 수 있는 <em>사이트</em>에서 <em>악성 코드</em>가 실행됨)</li>
<li><strong>CSRF (Cross-Site Request Forgery)</strong>: 공격자가 사용자의 <strong>인증(쿠키)을 도용</strong>하여, 사용자가 의도하지 않은 <strong>요청(Request)을 서버로 강제 전송</strong>하는 공격입니다. (신뢰할 수 있는 <em>사용자</em>의 브라우저에서 <em>위조된 요청</em>이 전송됨)</li>
</ul>
<p>간단히 말해, <strong>XSS는 사용자의 브라우저를 신뢰</strong>하여 코드를 실행하는 것이고, <strong>CSRF는 서버가 사용자의 요청을 신뢰</strong>한다는 점을 악용합니다.</p>
<hr />
<h2 id="4-csrf-공격-방어-방법">4. CSRF 공격 방어 방법</h2>
<p>CSRF 공격은 기본적으로 <strong>교차 출처(Cross-Origin) 상황에서의 요청을 신뢰하지 않고, 현재 요청이 정말 사용자의 의도에 의해 발생한 것인지 확인</strong>하는 방식으로 방어할 수 있습니다.</p>
<h3 id="1-referer-헤더-검증">1. Referer 헤더 검증</h3>
<ul>
<li><strong>작동 원리</strong>: <code>Referer</code> 요청 헤더는 현재 요청을 보낸 페이지의 주소(출처)를 담고 있습니다. 서버 측에서 이 <code>Referer</code> 헤더 값과 서버의 도메인(<code>Host</code> 헤더)을 비교하여, 일치하지 않으면(즉, <code>attacker.com</code>에서 보낸 요청이면) 요청을 거부(예외 발생)할 수 있습니다.</li>
<li><strong>한계</strong>: <code>Referer</code> 헤더는 브라우저 설정이나 프록시 등에 의해 누락될 수 있으며, 이론적으로 조작될 가능성도 있다는 한계가 있습니다.</li>
</ul>
<h3 id="2-anti-csrf-토큰-ssr세션-기반">2. Anti-CSRF 토큰 (SSR/세션 기반)</h3>
<p>템플릿 엔진(JSP, Thymeleaf, Pug, EJS 등)을 사용하는 SSR(서버 사이드 렌더링) 환경에서 가장 널리 쓰이는 강력한 방어책입니다.</p>
<ol>
<li><p>서버는 페이지를 생성(렌더링)하기 이전에 <strong>사용자 세션에 임의의 난수 값인 'CSRF 토큰'을 저장</strong>합니다.</p>
</li>
<li><p>사용자에게 페이지를 응답할 때, 중요한 요청(예: 폼 전송)을 발생시키는 <code>form</code> 태그 내부에 해당 CSRF 토큰값을 가진 <code>input</code> 태그를 숨겨서 추가합니다.</p>
<pre><code class="language-html">&lt;form action=&quot;/member/changePassword&quot; method=&quot;POST&quot;&gt;
    &lt;input type=&quot;hidden&quot; name=&quot;csrf_token&quot; value=&quot;csrf_token_12341234_random_value&quot; /&gt;
    &lt;button type=&quot;submit&quot;&gt;비밀번호 변경&lt;/button&gt;
&lt;/form&gt;</code></pre>
</li>
<li><p>사용자가 폼을 제출하여 실제 요청이 서버로 전달될 때, 서버는 <strong>폼 데이터에 포함된 <code>csrf_token</code> 값</strong>과 <strong>사용자 세션 내부에 저장된 CSRF 토큰</strong>의 일치 여부를 판단합니다.</p>
</li>
<li><p>두 값이 일치해야만 요청을 정상적으로 처리합니다.</p>
</li>
</ol>
<p>(공격자는 <code>attacker.com</code>에서 <code>david1p.com</code>으로 요청을 위조할 수는 있지만, 사용자의 세션에 저장된 이 임의의 토큰값은 알 수 없으므로 <code>csrf_token</code> 값을 맞춰서 보낼 수 없습니다.)</p>
<h3 id="3-double-submit-cookie-spa토큰-기반">3. Double Submit Cookie (SPA/토큰 기반)</h3>
<p>React, Vue, Angular와 같은 SPA(Single Page Application) 환경에서는 서버 세션을 사용하지 않고 JWT 같은 토큰을 주로 사용합니다. 이 경우 세션에 CSRF 토큰을 저장할 수 없으므로 다른 방식이 필요합니다.</p>
<ol>
<li><strong>토큰 발행</strong>: 사용자가 로그인할 때, 서버는 응답 헤더(예: <code>Authorization</code>)로 JWT 토큰을 발행하는 것과 <strong>별개</strong>로, CSRF 방어용 토큰을 <strong>쿠키</strong>로 함께 발행합니다. (이 쿠키는 <code>HttpOnly=false</code>로 설정하여 JS가 읽을 수 있게 해야 합니다.)</li>
<li><strong>클라이언트의 2중 전송</strong>: 클라이언트(JavaScript)는 API 요청 시, 브라우저가 자동으로 보내는 쿠키 외에도, <strong>JS로 쿠키에 저장된 CSRF 토큰 값을 읽어</strong> <code>HTTP Header</code> (예: <code>X-CSRF-TOKEN</code>)에 명시적으로 담아 함께 전송합니다.</li>
<li><strong>서버 검증</strong>: 서버는 요청이 도착하면 <strong>쿠키에 담긴 CSRF 토큰</strong>과 <strong>HTTP 헤더에 담긴 CSRF 토큰</strong> 값이 일치하는지 검증합니다.</li>
</ol>
<p>(SOP(동일 출처 정책)로 인해 공격자의 사이트 <code>attacker.com</code>에서는 <code>david1p.com</code>의 쿠키 값을 JS로 읽을 수 없으므로, 헤더에 토큰을 담아 보낼 수 없습니다.)</p>
<h3 id="4-samesite-쿠키-속성-사용">4. SameSite 쿠키 속성 사용</h3>
<p>쿠키 자체의 전송 정책을 제어하는 가장 강력하고 현대적인 방법입니다. 쿠키에 <code>SameSite</code> 속성을 설정하여, 크로스 사이트 요청 시 쿠키가 전송되는 것을 브라우저단에서 제어합니다.</p>
<ul>
<li><code>Set-Cookie: sessionId=...; SameSite=Strict</code><ul>
<li><strong><code>Strict</code></strong>: 같은 사이트(Same-Site)에서 보낸 요청에만 쿠키를 전송합니다. 가장 강력하지만, 다른 도메인에서 내 사이트로 링크를 타고 들어오는 경우 등에도 쿠키가 전송되지 않아 불편할 수 있습니다.</li>
<li><strong><code>Lax</code></strong>: <code>Strict</code>보다 다소 완화된 정책. <code>GET</code> 요청과 같은 일부 교차 출처 요청은 허용하지만, <code>POST</code> 폼 전송 등 CSRF 공격에 주로 사용되는 요청에서는 쿠키를 전송하지 않습니다. (최신 브라우저들의 기본값이 <code>Lax</code>인 경우가 많습니다.)</li>
</ul>
</li>
</ul>
<h3 id="5-sop와-cors-정책-활용">5. SOP와 CORS 정책 활용</h3>
<p>브라우저의 기본 보안 정책인 <strong>SOP(Same-Origin Policy, 동일 출처 정책)</strong>를 기본으로 활용하고, <strong>CORS(Cross-Origin Resource Sharing, 교차 출처 리소스 공유)</strong> 설정을 통해 신뢰할 수 있는 출처의 요청만 명시적으로 허용하는 방식도 CSRF를 포함한 다양한 교차 출처 공격을 방어하는 데 도움이 됩니다.</p>