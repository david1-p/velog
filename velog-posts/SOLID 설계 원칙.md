<h1 id="설계원칙-solid">설계원칙 (S.O.L.I.D)</h1>
<h3 id="1-단일-책임-원칙srp-single-responsibilty-principle">1. 단일 책임 원칙(SRP, Single Responsibilty Principle)</h3>
<p> 모든 클래스는 각각 하나의 책임만 가져야 하는 원칙이다. 예를 들면 A라는 로직이 존재한다면 어떠한 클래스는 A에 관한 클래스여야 하고, 이슬 수정했을 때도 A와 관련된 수정이어야 한다. </p>
<h3 id="2-개방-폐쇄-원칙ocp-open-closed-principle">2. 개방 폐쇄 원칙(OCP, Open Closed Principle)</h3>
<p>유지 보수 사항이 생긴다면 코드를 쉽게 확장할 수 있도록 하고 수정할 때는 닫혀 있어야 하는 원칙이다. 
기존의 코드는 잘 변경하지 않으면서도 확장은 쉽게 할 수 있어야 한다. </p>
<h3 id="3-리스코프-치환-원칙lsp-liskov-substitution-principle">3. 리스코프 치환 원칙(LSP, Liskov Substitution Principle)</h3>
<p>프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 하는 것을 의미한다. 클래스는 상속되기 마련이고 부모, 자식이라는 계층관계가 만들어진다. 이때 부모 객체에 자식 객체를 넣어도 시스템이 문제없이 돌아가게 만드는 것을 말한다. </p>
<h3 id="4-인터페이스-분리-윈칙isp-interface-segregation-principle">4. 인터페이스 분리 윈칙(ISP, Interface Segregation Principle)</h3>
<p>하나의 일반적인 인터페이스보다 구체적인 여러 개의 인터페이스를 만들어야 한다는 원칙이다. </p>
<h3 id="5-의존-역전-원칙dip-dependency-inversion-principle">5. 의존 역전 원칙(DIP, Dependency Inversion Principle)</h3>
<p>자신보다 변하기 쉬운 것에 의존하던 것을 추상와된 인터페이스나 상위 클래스를 두어 변하기 쉬운 것의 변화에 영향을 받지 않게 하는 원칙. 
상위 계층은 하위 계층의 변화에 대한 구현으로부터 독립되어야 한다. </p>