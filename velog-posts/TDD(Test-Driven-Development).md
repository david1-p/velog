<p><img alt="DTT 도표화" src="https://velog.velcdn.com/images/david1-p/post/f7bef876-3c50-47da-8f9f-0d62d3ae5a99/image.png" /></p>
<h3 id="1-좋은-소프트웨어의-요건-소비자-입장이-아닌-개발자-입장에서">1. 좋은 소프트웨어의 요건 (소비자 입장이 아닌, 개발자 입장에서)</h3>
<pre><code>1) 버그가 적고 예상대로 동작해야 한다. (가장 중요!)
2) 변경이 용이하고 확장 가능한 구조</code></pre><h3 id="2-자동화된-테스트의-역할">2. 자동화된 테스트의 역할</h3>
<pre><code>1) 좋은 소프트웨어를 만들려면 테스트 필수
2) 지속적인 테스트와 개발이 함께 이루어질때 충분한 품질 보장
3) 테스트를 많이 진행할수록 자동화된 테스트의 효율과 효과 상승!
4) 사람이 하는 테스트는 준비하는 과정에서의 비용이 자동화 테스트에 비해 비용이 적게 들지만, 
   실행비용은 자동화보다 비용이 많이 든다.
5) 오류가 발생하는 범주가 사람이 하는 테스트는 넓지만, 
   자동화된 테스트는 예측 가능한 범위 내에서 오류가 발생하므로 해결 가능 </code></pre><h3 id="3-tdd를-사용하지-않을-경우-일반적인-코딩-절차">3. TDD를 사용하지 않을 경우 일반적인 코딩 절차</h3>
<pre><code>1) 요구사항을 이해한다. 
2) 코드를 작성한다. 
3) 설계를 개선한다. 
4) 테스트 // 여기까지 해봐야됨 (주로 테스트 과정에서 오류 발생)
5) 오류를 수정한다. </code></pre><h3 id="4-tdd를-사용하는-경우-코딩-절차">4. TDD를 사용하는 경우 코딩 절차</h3>
<pre><code>kent-beck이 과거 프로그래밍 사례를 바탕, 정해진 주기를 따라 점진적으로 안정적인 코드를 작성한다. 
    1), 2), 3)은 필수조건(*)

*1) 다루고 싶은 테스트 시나리오 목록을 작성한다. 
   (요구사항을 진행했을 때 어떤 테스트를 해야할까 생각)
*2) 목록에 있는 정확히 하나의 항목을 실제적이고, 구체적이며 실행가능한 테스트로 바꾼다.
   (자연어 -&gt; 프로그래밍어)
*3) 테스트(및 모든 이전테스트)를 통과하도록 코드를 변경한다. 
   (운영코드를 변경, 통과했다는 의미는 현재와 과거의 테스트를 통과했다는 의미 
     프로그래머가 생각하지 못한 버그가 발생할 수 있는 경우가 있음)
4) 선택적으로 리팩터링하여 구현설계를 개선한다.</code></pre><h3 id="5-tdd의-효과">5. TDD의 효과</h3>
<pre><code>1) 의식적으로!! 구체적인 테스트 시나리오 목록을 기록하게 한다. 
2) 두려워하지 않고 앞으로 나아갈 수 있게 한다. 
   (TDD를 사용하면 자동화테스트를 이미 거치게 되므로 두려움이 적어짐)
3) 필요한 작업이 누락되지 않는다. (누락이 되는 상황이 줄어듬!)
4) 불필요한 코드가 작성될 가능성이 낮아진다.
5) ** 구현 코드의 설계보다 제품의 가치에 집중하게 한다. </code></pre><h3 id="6-요구사항-인터페이스-구현의-구분과-이해">6. 요구사항, 인터페이스, 구현의 구분과 이해</h3>
<pre><code>1) 요구사항은 제품의 가치를 표현한다
2) 인터페이스는 제품의 가치를 전달하는 방식의 약속
3) 구현은 전달할 제품의 가치를 생산하는 수단
4) 테스트는 인터페이스를 통해 요구사항을 검증한다. 
   (구현에 관여하지 않는다. 구현은 정말 자주 바뀌며, 
   실제 테스트가 구현에 의존하는 상황이 많아짐)</code></pre><p>참고
<a href="https://tech.kakaopay.com/post/implementing-tdd-in-practical-applications/">https://tech.kakaopay.com/post/implementing-tdd-in-practical-applications/</a>
<a href="https://tidyfirst.substack.com/p/canon-tdd">https://tidyfirst.substack.com/p/canon-tdd</a>
<a href="https://medium.com/@mingyuchoo/%EC%BC%84%ED%8A%B8-%EB%B2%A1-kent-beck-%EC%9D%B4-2002%EB%85%84-test-driven-development-by-example-%EB%A5%BC-%EC%B6%9C%EA%B0%84%ED%95%98%EB%A9%B4%EC%84%9C-tdd-test-driven-develop-%EA%B0%80-%EC%95%8C%EB%A0%A4%EC%A1%8C%EC%96%B4%EC%9A%94-8df8ae4db004">https://medium.com/@mingyuchoo/%EC%BC%84%ED%8A%B8-%EB%B2%A1-kent-beck-%EC%9D%B4-2002%EB%85%84-test-driven-development-by-example-%EB%A5%BC-%EC%B6%9C%EA%B0%84%ED%95%98%EB%A9%B4%EC%84%9C-tdd-test-driven-develop-%EA%B0%80-%EC%95%8C%EB%A0%A4%EC%A1%8C%EC%96%B4%EC%9A%94-8df8ae4db004</a></p>