<h1 id="htmlcss">HTML/CSS</h1>
<h2 id="1-인라인레벨-inline">1. 인라인레벨 (Inline)</h2>
<pre><code> &lt;span&gt;
 해당 레벨의 가로는 컨텐츠의 가로사이즈, 세로는 컨텐츠의 세로사이즈 영역만 차지함.</code></pre><h3 id="1-텍스트-태그">1) 텍스트 태그</h3>
<pre><code>&lt;q&gt; 태그 : 인용구 쌍따옴표(&quot;&quot;)
&lt;b&gt;,&lt;strong&gt; 태그 : 볼드체
&lt;u&gt;,&lt;ins&gt; 태그 : 밑줄
&lt;del&gt; 태그 : 중앙선
&lt;i&gt;, &lt;em&gt; 태그 : 글자 기울이기, 이탤릭체
&lt;mark&gt; 태그 : 배경 노란색
&lt;sub&gt; 태그 : 글자를 작게 하여 아래로 배치
&lt;sup&gt; 태그: 글자를 작게 하여 위로 배치</code></pre><h3 id="2-a-태그-링크-태그">2) A 태그 (링크 태그)</h3>
<pre><code>기본 폰트 밑줄 파랑 (히스토리 있으면 보라색으로 변함)
글씨 뒷 영역까지 영역화됨.

- 필수 속성 
    href: 클릭했을 때 이동할 경로 지정(img 태그와 동일)
          &quot;#&quot; 임시링크, 현재 페이지 내부경로 지정

         ex) &lt;a href=&quot;http://www.naver.com&quot; target=&quot;_blank&quot;&gt;&lt;/a&gt;네이버&lt;/a&gt;

            &lt;a href=&quot;&quot;&gt;
               &lt;img src=&quot;./img/1.jpg&quot; alt=&quot;이미지에 대한 설명&quot;&gt;
            &lt;/a&gt; 

- 옵션 속성
    target: 클릭하여 페이지 이동방법
            _self: 기본값, 현재창
            _blank: 새 탭에서 열기 

    ex) &lt;a href=&quot;&quot;&gt;
            &lt;div&gt;메뉴&lt;/div&gt;
        &lt;/a&gt;

        이 경우 a 태그는 inline레벨이라 컨텐츠 영역만큼 작동하지만.
        포함하고 있는 &lt;div&gt;는 block레벨이므로 메뉴라는 글씨의 우측 빈공간을 클릭해도 링크가 작동.</code></pre><h3 id="3-select-태그">3) Select 태그</h3>
<pre><code>&lt;select&gt;: 여러 개의 옵션을 리스트처럼 나열하고 그 중에서 선택할 수 있게 하는 태그 
          부모로만 사용 가능함. 
&lt;option&gt;: &lt;select&gt;의 자식/자손으로만 사용 가능 

- 속성 - 

1) selected: option에 적용됨. checked나 autofocus처럼 처음부터 선택되어 나타남
2) multiple: select에 적용됨. 여러 개 선택가능하지만 디자인상 이쁘지 않아 잘 안씀.
3) name: select에 적용됨.
4) value: option에 적용됨.

&lt;optgroup&gt;: &lt;option&gt;들을 그룹핑

ex) &lt;optgroup label=&quot;포켓몬&quot;&gt;
        &lt;option value=&quot;피카츄&quot;&gt;피카츄&lt;/option&gt;
        &lt;option value=&quot;라이츄&quot;&gt;라이츄&lt;/option&gt;
        &lt;option value=&quot;파이리&quot;&gt;파이리&lt;/option&gt;
    &lt;/optgroup&gt;    </code></pre><h2 id="2-블록레벨-block">2. 블록레벨 (Block)</h2>
<pre><code>&lt;div&gt;
기본으로 디스플레이 가로 끝까지 세로는 컨텐츠 영역만큼 영역을 차지함.
별도 사이즈를 주면 적용함.</code></pre><h3 id="1-텍스트-태그-heading-tag">1) 텍스트 태그 (Heading tag)</h3>
<pre><code>&lt;h1&gt; ~ &lt;h6&gt;: 웹표준, 제목 태그, 위아래 margin 있음. 
&lt;p&gt;: 단락 태그 (위아래 margin 있음)
&lt;br&gt;: 줄바꿈. 픽셀을 알 수가 없어서 실무에서는 잘 안씀, &lt;div&gt;를 주로 사용함. 
&lt;hr&gt;: 실선 
&lt;blockquote&gt;: 좌우에 여백이 들어가서 들여쓰기 
* figure와 차이가 없음 
cf) &lt;blockquote&gt;는 대부분 텍스트
    &lt;figure&gt;은 이미지

&lt;pre&gt;: 좌우에 여백이 들어가진 않지만 빈칸/엔터값이 그대로 출력, &lt;div&gt;로 대체.</code></pre><h3 id="2-목록-태그">2) 목록 태그</h3>
<pre><code>&lt;ul&gt; unordered list: 순서가 필요없는 목록

    - 속성 - ⇒  type: disc (기본값, 검은 점)
                     circle (흰 원)
                     square (검은 사각형)
                     none (점 표시 안함)
        *  &lt;ul&gt; 중복으로 사용시 기본값은 disc &gt; circle &gt; square 순으로 정해짐

        cf) ul 위아래 여백, ul-li 여백 생각 
            여백 대칭으로 생각, 3개 블록 병렬일때 왼쪽 오른쪽 생각    

&lt;ol&gt; ordered list: 순서가 필요한 목록

          ⇒  type: 1 (기본값, 정수) 
                      A/a (영어 대문자 / 소문자)
                    I/i (로마 대문자 / 소문자)
                    start (시작점 변경, ex) &quot;3&quot; 3부터 시작)
                    reversed (시작점부터 거꾸로 표기, li의 개수와 같거나 크게 정해야 됨. 
                             0과 음수가 나오면 안됨.)


&lt;li&gt;: &lt;ul&gt;/&lt;ol&gt; 자식으로 사용(li 밑에 ul 사용 가능)



&lt;dl&gt; Description-List: dd/dt의 부모로만 사용가능 (거의 안씀)
&lt;dt&gt; Description-Term: 왼쪽 정렬 , 제목
&lt;dd&gt; Description-Description: 들여쓰기, 내용</code></pre><h3 id="3-테이블-태그-row--column---웹표준">3) 테이블 태그 (row / column) - 웹표준</h3>
<pre><code>&lt;table&gt;: 부모로만 사용 가능 
&lt;thead&gt;, &lt;tbody&gt;, &lt;tfoot&gt;: table의 자식으로만 사용 가능 
&lt;tr&gt;: &lt;table&gt;, &lt;thead&gt;, &lt;tbody&gt;, &lt;tfoot&gt; 자식으로만 사용 가능
&lt;th&gt;: tr의 자식으로만 사용 가능 / 한 칸을 의미하며 주로 제목에 쓰인다. 
       Bold체와 가운데 정렬이 기본값.
&lt;td&gt;: tr의 자식으로만 사용 가능 / 한 칸을 의미하며 기본 셀이다. 
       일반 두께와 왼쪽 정렬이 기본값.

- 옵셔널 요소 - 

 1) caption: 표 제목 
               실제 코드의 위치와 상관없이 표 위 가운데에 출력
              table 태그의 자식으로만 사용됨

 2) colgroup: 여러 열들을 묶어서 CSS 적용 
     col: 각 열

    ex) 4열인 경우 4개의 col 태그를 써야 하며, 
        &lt;colgroup&gt;
            &lt;col style =&quot;width: 100px&quot;&gt;
            &lt;col style =&quot;width: 200px&quot;&gt;
            &lt;col style =&quot;width: 300px&quot;&gt;
            &lt;col style =&quot;width: 400px

- 속성 - 

1) border: (table용) 테두리 두께 / 단위: 정수, 기본값: 0
2) cellspacing: (table용) 컨텐츠의 외부영역 여백 / 단위: 정수, 기본값: 2
3) cellpadding: (table용) 컨텐츠의 내부영역 여백 / 단위: 정수, 기본값: 2
4) span : 칸을 늘림 / 단위: 정수, 
          colspan: 옆으로 늘림 / 같은 tr 상의 칸을 없애줘야 함. 
          rowspan: 아랫줄로 늘림 / 아래 줄에 있는 th/td를 없애줘야 함. </code></pre><h3 id="4-figure">4) figure</h3>
<pre><code>&lt;blockquote&gt;처럼 좌우 여백이 있어서 들여쓰기가 됨. 
&lt;figcaption&gt;: figure의 제목 

cf) caption과의 차이점 - 실제 코드 위치에 배치, 왼쪽 정렬 </code></pre><h3 id="5-fieldset">5) fieldset</h3>
<pre><code>구역을 표시해줌 부모로만 가능 
&lt;legend&gt;: &lt;fieldset&gt;의 자식으로만 사용 가능하며 fieldset의 타이틀</code></pre><h3 id="6-form">6) form</h3>
<pre><code>* form 안에 있는 요소들(정보) 중에서 name 속성을 가지고 있는 요소들의 실제값을 서버로 전송
** 정보를 전송할 때는 반드시 sumbit 태그를 사용해야 함. 

- 속성 - 

1) action: 정보를 보낼 페이지 경로(a, img 태그와 비슷)
           정해지지 않았다면 #, &lt;submit&gt; 버튼을 누르는 순간 동작

2) method: 정보를 보내는 방식 
           get: 기본값, URL에 정보를 다 표시하여 전달하는 방식
           post: URL에 숨겨서 전달하는 방식 (민감정보 보낼때 쓰임)

3) target: 정보전달시 현재탭에서 이동할 것인지, 새탭에서 이동할 것인지 결정 
           _self: 기본값
           _blank: 새탭

4) autocomplete: 기록이 있는 경우 자동완성
           on: 기본값, 이전 히스토리를 이용하여 리스트를 보여줌
           off: 자동완성 기능 끔.

ex) &lt;form action=&quot;#&quot; name=&quot;naver&quot; method=&quot;get&quot; target=&quot;_blank&quot; autocomplete=&quot;off&quot;&gt;</code></pre><h2 id="3-인라인-블록-inline-block">3. 인라인 블록 (Inline-block)</h2>
<pre><code>카테고리상 inline 레벨에 속하며, 
inline의 컨텐츠 크기만큼 크기를 가지며 block레벨의 특정 수치를 주면 적용되는 특성이 합쳐짐</code></pre><h3 id="1-img-태그">1) img 태그</h3>
<pre><code>닫는 태그가 없으며 
별도로 사이즈를 명시하지 않으면 이미지 원본 사이즈대로 출력됨.
가로 또는 세로 한쪽 사이즈를 기입하면 반대쪽 사이즈가 자동적으로 비율에 맞춰 조정됨.

- 속성 - 

&lt;img src=&quot;이미지 경로&quot; alt=&quot;이미지 설명&quot;&gt;

1) src : 필수 속성 (경로)
2) alt : 이미지 설명(img를 출력할 수 없는 경우 이미지 대신 출력된다)
         이 속성은 모든 사람들이 똑같은 정보를 얻을 수 있게 해야 한다는 웹접근성의 대표적인 예이다.
         일반 기기가 아닌 시각장애인용 기기는 콘텐츠를 음성으로 읽어준다. 
         텍스트는 읽어주지만 이미지를 읽어주지 못하므로 
         이미지 대신 &quot;alt&quot; 속성의 이미지 설명을 읽어준다. 

         cf) 웹표준 (크로스 브라우징) : 모든 브라우저에서 작업한 결과가 그대로 나오게 만듬.</code></pre><h3 id="2-input-태그">2) input 태그</h3>
<pre><code>&lt;input type=&quot; 유형&quot; 속성=&quot;속성값&quot; 속성=&quot;속성값&quot;&gt;

사용자로부터 입력을 받을 수 있는 태그이며, 닫는 태그가 없고 기본 사이즈가 정해져 있다.   

- 속성 - 

1) type: (필수) 유형

2) value: (옵션) 실제값

    * 필수처럼 사용하는 type: &lt;button&gt;
    * &lt;checkbox&gt;나 &lt;radio&gt;에서는 실제화면에 출력되지 않고 정보를 전송할 때 사용된다.        
3) name: (옵션) 요소에 이름을 달아주거나 요소들을 그룹핑할때 사용한다. 

    * 필수처럼 사용하는 type: &lt;radio&gt;

4) placeholder: 필드에 힌트표시 password 에서도 글씨로 표시됨

5) autofocus: 자동으로 커서 표시, 여러 요소에 동시에 사용시 제일 윗 요소에 커서 표시됨.

6) readonly: 해당 필드을 읽기전용 으로 표시되어 수정 불가하지만, form태그로 전송할때 값이 전송됨

7) disabled: 해당 필드를 비활성화되며 해당 필드 수정 불가. form태그로 전송할때 값이 전송안됨.

8) required: form 태그 안에서 필수 요소로 지정 빈칸 불가.

9) maxlength: 해당 필드의 최대 글자 수 지정, 단위 - 정수
10) minlength: 해당 필드의 최소 글자 수 지정, 단위 - 정수
                form 태그 안에서만 동작함 (submit 버튼 클릭시 글자수 체크함)

11) checked: radio, checkbox 용도의 속성    


- 많이 사용하는 type -

    &lt;input type=&quot;text&quot; value=&quot;abcd&quot;&gt;
    &lt;input type=&quot;password&quot; value=&quot;1234&quot;&gt;
    &lt;input type=&quot;button&quot; value=&quot;로그인&quot;&gt;

cf) &lt;button&gt;확인&lt;/button&gt; 

    ⇒  &lt;input type=&quot;button&quot;&gt;과 같지만 닫는 태그가 있으며 
        &lt;form&gt; 태그 밖에 있으면 type=&quot;button&quot;으로 인식하고 
        &lt;form&gt; 태그 안에 있으면 type=&quot;sumit&quot;으로 인식된다.  

    &lt;input type=&quot;radio&quot; value=&quot;예&quot; name=&quot;rd&quot;&gt;
    &lt;input type=&quot;checkbox&quot; value=&quot;웹1&quot; name=&quot;chk&quot;&gt;
       &lt;input type=&quot;email&quot;&gt;

 - 중간 빈도 type -

     &lt;input type=&quot;submit&quot; value=&quot;확인&quot;&gt;
    &lt;input type=&quot;reset&quot;&gt;
    &lt;input type=&quot;hidden&quot;&gt;: text와 동일하지만 화면에 출력 안됨 
    &lt;input type=&quot;search&quot;&gt;: text와 동일하지만 글자 입력시 X 표시 나옴 
    &lt;input type=&quot;file&quot;&gt;: 파일 업로드시 사용 -
    &lt;input type=&quot;range&quot;&gt;
    &lt;input type=&quot;number&quot;&gt; 
    &lt;input type=&quot;tel&quot;&gt; : 웹페이지에서는 일반 text 필드, 스마트기기에서는 숫자패드로 출력됨
    &lt;input type=&quot;email&quot;&gt;: form태그 안에서만 동작. 필드에 @ 있어야만 통과
    &lt;input type=&quot;date&quot;&gt; 
    &lt;input type=&quot;datetime-local&quot;&gt;</code></pre><h3 id="3-textarea-태그">3) textarea 태그</h3>
<pre><code>메모/게시글 본문에 쓰이며, 엔터, 빈칸이 다 가능하다. 
(cf. input type=&quot;text&quot;의 경우 엔터키가 안먹음)
사이즈가 정해져 있지만 사이즈를 별도로 줄 수 있음. 

cols: 30칸 rows: 10줄이 기본
ex) &lt;textarea name=&quot;&quot; id&quot;&quot; cols=&quot;10&quot; rows=&quot;10&quot;&gt;&lt;/textarea&gt;</code></pre>