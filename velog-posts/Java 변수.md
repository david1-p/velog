<h1 id="1-변수">1. 변수</h1>
<p>   화면에 글자가 출력할 때는 system.out.print()를 사용한다. 
   system.out.print() 줄바꿈 X
   system.out.println() 줄바꿈 O</p>
<blockquote>
<p>   ex) system.out.print(&quot;Hello, world&quot;) =&gt; Hello, world 출력
        system.out.print(3+5) =&gt; 8 출력
           system.out.print(&quot;3+5&quot;) =&gt; 3+5 출력</p>
</blockquote>
<p>int x: 정수(integer)를 저장하기 위한 변수 x 선언</p>
<blockquote>
</blockquote>
<pre><code>    ex) int x;
        x = 5; =&gt; int x = 5;</code></pre><table>
<thead>
<tr>
<th align="center">종류</th>
<th align="center">변수명</th>
<th align="left">설명</th>
</tr>
</thead>
<tbody><tr>
<td align="center">숫자</td>
<td align="center">int</td>
<td align="left">정수(integer) 저장하기 위한 타입</td>
</tr>
<tr>
<td align="center"></td>
<td align="center">long</td>
<td align="left">대략 20억 넘을때 long 사용</td>
</tr>
<tr>
<td align="center"></td>
<td align="center">float</td>
<td align="left">실수(floating-point number) 저장하는 타입</td>
</tr>
<tr>
<td align="center"></td>
<td align="center">double</td>
<td align="left">float은 오차없이 7자리, double은 15자리</td>
</tr>
<tr>
<td align="center">문자</td>
<td align="center">char</td>
<td align="left">문자(charater) 저장하기 위한 타입</td>
</tr>
<tr>
<td align="center"></td>
<td align="center">string</td>
<td align="left">여러문자(문자열, string)를 저장하기 위한 타입</td>
</tr>
</tbody></table>
<pre><code>ex) inx x = 100; // 정수 저장할 Type: int
    double pi = 3.14; // 실수 저장할 Type: double
    char ch = 'a'; // 문자(1개) 저장 Type: char
    string str = &quot;abc&quot; // 여러문자(0~n개)를 저장할 Type: string</code></pre><p>상수는 한번 값을 정하면 다른 값으로 변경할 수 없다. 
상수 이름은 모두 대문자로 하는 것이 관례인데, 여러 단어로 이루어져 있는 경우 '_'로 구분한다. </p>
<p>리터럴(literal)</p>
<p>12, 123, 3.14, 'A' 같은 값들이 '상수'인데, 
프로그래밍에서는 상수 '값을 한 번 저장하면 변경할 수 없는 저장공간'으로 정의하였으므로 상수 대신 리터럴로 정의한다. </p>
<p>변수(variable) : 하나의 값을 저장하기 위한 공간
상수(constant) : 값을 한번만 저장할 수 있는 공간
리터럴(literal) : 그 자체로 값을 의미하는 것</p>
<pre><code>ex) int year = 2014;
    final int MAX_VALUE= 100;

    변수: year
    리터럴: 2014, 100
    상수: MAX_VALUE             </code></pre><table>
<thead>
<tr>
<th align="center">종류</th>
<th align="center">리터럴</th>
<th align="center">설명</th>
</tr>
</thead>
<tbody><tr>
<td align="center">논리형</td>
<td align="center">false, true</td>
<td align="center">없음</td>
</tr>
<tr>
<td align="center">정수형</td>
<td align="center">123,0b01,0xFF, 100L</td>
<td align="center">L(long) 접미사 없으면 int 타입!</td>
</tr>
<tr>
<td align="center">실수형</td>
<td align="center">3.14, 3.0e8, 1.4f, 0X1.0p^-1</td>
<td align="center">f, d</td>
</tr>
<tr>
<td align="center">문자형</td>
<td align="center">&quot;A&quot;, &quot;B&quot;, &quot;C&quot;</td>
<td align="center">없음</td>
</tr>
<tr>
<td align="center">문자열</td>
<td align="center">&quot;ABC&quot;, &quot;123&quot;, &quot;A&quot;, &quot;TRUE&quot;</td>
<td align="center">문자(charater) 없음</td>
</tr>
</tbody></table>
<p>&lt;문자 리터럴&gt;
'A'와 같이 작은 따옴표로 문자 하나를 감싼 것을 '문자 리터럴'
두문자 이상은 큰 따옴표로 감싸야 한다. &quot;문자열 리터럴&quot;</p>
<p>char ch = 'J';
string name = &quot;Java&quot;; 변수 name에 문자열 리터럴 &quot;Java&quot; 저장
string 문자열 리터럴은 &quot;&quot; 아무것도 안넣은 것도 허용
특별히 아래와 같은 표현 허용함</p>
<p>string name = new string(&quot;java&quot;);
string name = &quot;java&quot;;</p>
<p>&lt;문자열 결합&gt;</p>
<p>string name = &quot;ja&quot;+&quot;va&quot;; =&gt; &quot;java&quot;
string str = name + 8.0; =&gt; &quot;name8.0&quot; </p>
<p>ex) 7 + &quot; &quot; -&gt; &quot;7&quot;+&quot; &quot; =&gt; &quot;7 &quot;
    &quot; &quot; + 7 -&gt; &quot; &quot; +  =&gt; &quot;  7&quot;
    7 + &quot;7&quot; -&gt; &quot;77&quot;
    7 + 7 + &quot; &quot; -&gt; 14 +&quot;&quot; -&gt; &quot;14  &quot;
    &quot; &quot; + 7 + 7 -&gt; &quot;7&quot;+ 7 -&gt; &quot;77&quot;</p>
<p>&lt;두 변수 값 바꾸기(Tmp 사용)&gt;</p>
<p>tmp = x; ① x의 값을 tmp에 저장
x= y;      ② y의 값을 x에 저장
y= tmp;     ③ tmp에 저장된 값을 y에 저장</p>
<p>기본형 </p>
<table>
<thead>
<tr>
<th align="center">종류</th>
<th align="center">1byte</th>
<th align="center">2byte</th>
<th align="center">4byte</th>
<th align="center">8byte</th>
</tr>
</thead>
<tbody><tr>
<td align="center">논리형</td>
<td align="center">boolean</td>
<td align="center"></td>
<td align="center"></td>
<td align="center"></td>
</tr>
<tr>
<td align="center">문자형</td>
<td align="center"></td>
<td align="center">char</td>
<td align="center"></td>
<td align="center"></td>
</tr>
<tr>
<td align="center">정수형</td>
<td align="center">byte</td>
<td align="center">short</td>
<td align="center">int</td>
<td align="center">long</td>
</tr>
<tr>
<td align="center">실수형</td>
<td align="center"></td>
<td align="center"></td>
<td align="center">float</td>
<td align="center">double</td>
</tr>
</tbody></table>
<p>boolean은 true/false 값 두개를 표현하면 되므로 1byte
char은 Java에서 유니코드(2byte 문자체계) 사용하므로 2byte
int를 기준으로 짧으면 short(2byte), long(8byte)
float 실수값을 부동소수점(floating-point) 방식으로 저장하기 때문에 float
double은 float보다 두 배의 크기를(8byte)를 갖기 때문에 double</p>
<p>정수형 
byte (-2^7 ~ 2^7-1)
short (-2^15 ~ 2^15-1)
int (-2^31 ~ 2^31 -1) : 대략 10자리 수 
long (-2^63 ~ 2^63 -1) </p>
<p>※ 7~9자리 수 계산할 때는 넉넉하게 long타입(약19자리) 변수로 선언하는 것이 좋다. 
※ println()은 변수 값 그대로 출력하므로 다른 형태로 값을 변환하고 싶다면 printf()사용 
printf는 지시자(specifer)
지시자는 값을 어떻게 출력할 것인지 지시해주는 역할 
지시자는 '%d'사용</p>
<p> ex) int타입 변수 age의 값이 14일때, printf()는 지시자 '%d' 대신 14를 넣어서 출력한다. 
 system.out.printf(&quot;age: %d&quot;, age);
 system.out.printf(&quot;age: %d&quot;, 14);
 system.out.printf(&quot;age: 14&quot;);</p>
<p> cf) 출력하려는 값이 두개라면 %d도 2개 사용해야 하며 그 이상 사용시 해당 개수만큼 %d 쓰면 된다. </p>
<p> system.out.printf(&quot;age: %d, year: %d&quot;, age, year);
 system.out.printf(&quot;age: %d, year: %d&quot;, 14, 2019);
 화면에는 &quot;age: 14 year: 2019&quot; 출력됨</p>
<p>printf()는 출력후 줄바꿈이 안됨!
따라서 지시자 %n을 넣어야함(\n도 사용가능하지만 os에 따라 안먹힐수 있음)</p>
<p>ex) system.out.printf(&quot;age: %d&quot;, age); 출력 후 줄바꿈 X
    system.out.printf(&quot;age: %d%n&quot;, age); 출력 후 줄바꿈 </p>
<p>&lt;print f의 지시자&gt; 자주 사용하는 것 </p>
<table>
<thead>
<tr>
<th align="center">종류</th>
<th align="left">출력</th>
</tr>
</thead>
<tbody><tr>
<td align="center">%d</td>
<td align="left">10진(decimal) 정수의 형식으로 출력</td>
</tr>
<tr>
<td align="center">%x</td>
<td align="left">16진수(hexa-decimal) 정수의 형식으로 출력</td>
</tr>
<tr>
<td align="center">%f</td>
<td align="left">부동소수점(floating-point) 주로 쓰임! (소수점 아래 6자리만 출력)</td>
</tr>
<tr>
<td align="center">%e</td>
<td align="left">지수형태로 출력</td>
</tr>
<tr>
<td align="center">%g</td>
<td align="left">값을 간략하게 표현</td>
</tr>
<tr>
<td align="center">%c</td>
<td align="left">문자(character)</td>
</tr>
<tr>
<td align="center">%s</td>
<td align="left">문자열(string)로 출력</td>
</tr>
</tbody></table>
<ul>
<li>system.out.printf(&quot;[%s]%n&quot;, url) =&gt; 문자열 길이만큼 출력공간 확보
system.out.printf(&quot;[%20s]%n&quot;, url) =&gt; 최소 20글자 출력공간 확보(우측정렬)
system.out.printf(&quot;[%-20s]%n&quot;, url) =&gt; 최소 20글자 출력공간 확보(좌측정렬)
system.out.printf(&quot;[%.8s]%n&quot;, url) =&gt; 왼쪽에서 8글자만 출력</li>
</ul>
<p>Overflow
정수 타입이 표현할 수 있는 최대값에 1을 더하면 최소값
정수 타입이 표현할 수 있는 최소값에서 1을 빼면 최대값</p>
<p>타입간 변환방법</p>
<ol>
<li><p>숫자로 문자로 변환 - 숫자에 '0'을 더한다. 
 (char)(3+'0') =&gt; '3'</p>
</li>
<li><p>문자를 숫자로 변환 - 문자에서 '0'을 뺀다.
 '3' - '0' = 3</p>
</li>
<li><p>숫자를 문자열로 변환 - 숫자에 빈 문자열(&quot;&quot;)을 더한다.
 3 + &quot; &quot; =&gt; &quot;3&quot;</p>
</li>
<li><p>문자열을 숫자로 변환 - integer.parselnt() 또는 Double.parseDouble() 사용한다
 Integer.parselnt(&quot;3&quot;) =&gt; 3
 Double.parseDouble(&quot;3.14&quot;) =&gt; 3.14</p>
</li>
<li><p>문자열을 문자로 변환 -&gt; charAt(0) 사용한다. 
 &quot;3&quot;.charAt(0) =&gt; '3'</p>
</li>
<li><p>문자를 문자열로 변환 - 빈문자열(&quot;&quot;)을 더한다. 
 '3' + &quot; &quot; =&gt; &quot;3&quot;</p>
</li>
</ol>