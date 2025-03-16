<h1 id="1-list-style">1. list-style</h1>
<pre><code>    목록 태그(&lt;ul&gt;, &lt;ol&gt;, &lt;li&gt;)의 점 관련 속성 

    선택자 &lt;ul&gt; &lt;li&gt; 상관없이 적용 가능</code></pre><h3 id="1-list-style-type">1) list-style-type</h3>
<pre><code>    &lt;ul&gt;, &lt;ol&gt;의 모양 disc, circle, square, none, decimal(1부터 시작하는 10진수)</code></pre><h3 id="2-list-style-image">2) list-style-image</h3>
<pre><code>    type 대신 이미지로 대체 

    ex) list-style-image: url()</code></pre><h3 id="3-list-style-position">3) list-style-position</h3>
<pre><code>    목록 들여쓰기 (점의 위치)

    outside: 기본값, li영역 외부
    inside: li영역 내부(왼쪽)</code></pre><h3 id="4-list-style-한방-단어">4) list-style (한방 단어)</h3>
<pre><code>    list-style: type image position</code></pre><h1 id="2-색-관련-css">2. 색 관련 CSS</h1>
<pre><code>    기본값: 글자는 검정색, 나머지는 투명(transparent)</code></pre><h3 id="1-속성">1) 속성</h3>
<pre><code>    * background-color: 배경색

    * color: 배경색을 제외한 모든 파트 관련 색(글자색, 테두리색)

    * border-color: 테두리색</code></pre><h3 id="2-속성값">2) 속성값</h3>
<pre><code>    a) 16진수 표기법: #0~f까지의 방식으로 표기 

                    * 6자리 - 두 자리씩 끊어서 빨초파 

                              #ff0000 - 빨간색
                              #00ff00 - 초록색
                              #0000ff - 파란색
                              #ffffff - 흰색
                              #000000 - 검은색

                    * 3자리 - 한 자리씩 끊어서 빨초파

                              #f00 - 빨간색
                              #0f0 - 초록색
                              #00f - 파란색
                              #fff - 흰색
                              #000 - 검은색

    b) rgb: 0~255까지의 색값으로 표기 

            ex) rgb(빨,초,파)

    c) rgba: 0~255까지의 색값으로 표기 

             ex) rgba(빨,초,파,투명도)
                  투명도: 기본값=1, 0 ~ 1 사이의 실수

    d) 색이름: red, green, blue</code></pre><h1 id="3-여백">3. 여백</h1>
<h3 id="1-margin-요소의-바깥쪽-영역-여백">1) margin: 요소의 바깥쪽 영역 여백</h3>
<pre><code>       margin-top, right, bottom, left 순          </code></pre><h3 id="2-padding-요소의-안쪽-영역-여백">2) padding: 요소의 안쪽 영역 여백</h3>
<pre><code>       padding-top, right, bottom, left 순</code></pre><h1 id="4-테두리">4. 테두리</h1>
<h3 id="1-border-width-테두리-선-두께-단위-px">1) border-width: 테두리 선 두께, 단위-px</h3>
<h3 id="2-border-style-선-종류">2) border-style: 선 종류</h3>
<pre><code>      solid: 실선

      dashed: 절취선

      dotted: 점선

      double: 이중선

      none: 선 없애기</code></pre><h3 id="3-border-color-테두리-색">3) border-color: 테두리 색</h3>
<h3 id="4-border-한방-단어">4) border (한방 단어)</h3>
<pre><code>      border: width style color</code></pre><h3 id="5-border-radius-테두리-둥글게-깎기">5) border-radius (테두리 둥글게 깎기)</h3>
<pre><code>      단위 : px, % </code></pre><h1 id="5-background">5. background</h1>