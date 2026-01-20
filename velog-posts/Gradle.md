<h2 id="1-gradle이란">1. Gradle이란?</h2>
<p>Gradle은 Java, Kotlin, Scala 등 JVM 기반 언어에서 주로 사용되는 오픈소스 빌드 자동화 도구입니다. 기존 Ant의 유연성과 Maven의 의존성 관리 편의성을 결합하고 단점을 보완하여 설계되었습니다.</p>
<p><strong>특징</strong></p>
<ul>
<li><strong>고성능</strong>: 증분 빌드(Incremental Build), 빌드 캐시(Build Cache), 데몬 프로세스를 활용하여 빌드 속도를 비약적으로 최적화했습니다.</li>
<li><strong>유연성</strong>: Groovy 또는 Kotlin DSL(Domain Specific Language)을 사용하여 로직이 포함된 복잡한 빌드 스크립트를 작성할 수 있습니다.</li>
<li><strong>확장성</strong>: 다양한 플러그인 생태계를 갖추고 있으며, 커스텀 태스크를 쉽게 정의할 수 있습니다.</li>
<li><strong>멀티 프로젝트 지원</strong>: 대규모 프로젝트를 모듈 단위로 나누어 관리하기 용이하도록 설계되었습니다.</li>
</ul>
<h2 id="2-빌드-자동화-도구의-사용-목적">2. 빌드 자동화 도구의 사용 목적</h2>
<p>빌드 도구는 단순한 컴파일러 실행을 넘어 소프트웨어 생명주기 전반을 관리합니다.</p>
<ul>
<li><strong>생산성 향상</strong>: 컴파일, 테스트, 패키징, 배포 등 반복적인 수동 작업을 자동화합니다.</li>
<li><strong>일관성 보장</strong>: 개발자 로컬, 테스트 서버, 운영 서버 등 어떤 환경에서도 동일한 빌드 결과를 보장합니다.</li>
<li><strong>의존성 관리</strong>: 외부 라이브러리를 자동으로 다운로드하고, 라이브러리 간의 버전 충돌을 효율적으로 관리합니다.</li>
<li><strong>품질 관리</strong>: 테스트 자동화 및 정적 분석을 통해 휴먼 에러를 방지하고 코드 품질을 유지합니다.</li>
<li><strong>CI/CD 연동</strong>: Jenkins, GitHub Actions 등과 연동하여 빌드부터 배포까지의 파이프라인을 구축할 수 있습니다.</li>
</ul>
<h2 id="3-maven-vs-gradle">3. Maven vs Gradle</h2>
<p>가장 큰 차이점은 <strong>빌드 스크립트 작성 방식</strong>과 <strong>성능</strong>입니다. Gradle은 Maven보다 늦게 나온 만큼 성능과 유연성 면에서 우위에 있습니다.</p>
<table>
<thead>
<tr>
<th>구분</th>
<th>Maven</th>
<th>Gradle</th>
</tr>
</thead>
<tbody><tr>
<td><strong>스크립트 언어</strong></td>
<td>XML (pom.xml)</td>
<td>Groovy DSL / Kotlin DSL (build.gradle)</td>
</tr>
<tr>
<td><strong>빌드 속도</strong></td>
<td>상대적으로 느림 (전체 빌드 위주)</td>
<td>빠름 (증분 빌드, 빌드 캐시, 데몬 활용)</td>
</tr>
<tr>
<td><strong>의존성 관리</strong></td>
<td>선언적 관리, 상속 구조</td>
<td>유연한 관리, 동적 버전 지원</td>
</tr>
<tr>
<td><strong>가독성</strong></td>
<td>태그 구조로 인해 장황함</td>
<td>코드 기반으로 간결하고 직관적임</td>
</tr>
<tr>
<td><strong>확장성</strong></td>
<td>정해진 라이프사이클에 종속적</td>
<td>스크립트로 로직 제어 및 확장 용이</td>
</tr>
</tbody></table>
<blockquote>
<p><strong>핵심 차이</strong>: Gradle은 작업의 입력과 출력을 확인하여 변경된 부분만 빌드하는 <strong>증분 빌드(Incremental Build)</strong>를 지원하기 때문에, 프로젝트 규모가 커질수록 Maven 대비 압도적인 성능 차이를 보입니다.</p>
</blockquote>
<h2 id="4-dependency-configuration-의존성-설정">4. Dependency Configuration (의존성 설정)</h2>
<p>애플리케이션에 필요한 라이브러리의 사용 범위와 시점을 정의합니다. 올바른 설정은 빌드 속도를 높이고 결과물의 크기를 줄이는 데 필수적입니다.</p>
<p><strong>implementation</strong></p>
<ul>
<li>컴파일 및 런타임에 모두 필요한 의존성입니다.</li>
<li><strong>특징</strong>: 의존하고 있는 라이브러리가 변경되더라도, 해당 모듈을 직접 의존하는 모듈만 재컴파일하면 됩니다. (재컴파일 범위 최소화로 빌드 속도 향상)</li>
<li>대부분의 라이브러리 적용 시 기본적으로 사용합니다.</li>
</ul>
<p><strong>api</strong></p>
<ul>
<li>implementation과 유사하지만, 의존성을 다른 모듈로 <strong>전이</strong>시킵니다.</li>
<li><strong>특징</strong>: A -&gt; B(api) -&gt; C 구조일 때, C가 변경되면 A도 재컴파일됩니다. 라이브러리 개발 등 내부 의존성을 외부로 노출해야 할 때만 신중히 사용해야 합니다.</li>
</ul>
<p><strong>compileOnly</strong></p>
<ul>
<li>컴파일 시점에만 필요하고 런타임에는 포함되지 않는 의존성입니다.</li>
<li>예: Lombok (컴파일 시 코드를 생성하고, 실제 실행 시에는 필요 없음)</li>
</ul>
<p><strong>runtimeOnly</strong></p>
<ul>
<li>컴파일 시점에는 필요 없고, 실행 시점에만 필요한 의존성입니다.</li>
<li>예: DB Driver (코드 상에서는 JDBC 인터페이스만 사용하고, 구현체는 런타임에 로딩)</li>
</ul>
<p><strong>annotationProcessor</strong></p>
<ul>
<li>컴파일 시점에 어노테이션을 분석하고 코드를 생성하는 전처리기입니다.</li>
<li>예: QueryDSL, MapStruct, Lombok</li>
</ul>
<p><strong>testImplementation</strong></p>
<ul>
<li>테스트 코드를 작성하고 실행할 때만 필요한 의존성입니다.</li>
<li>예: JUnit, Mockito, H2 Database</li>
</ul>
<hr />
<h2 id="5-gradle-wrapper-gradlew">5. Gradle Wrapper (gradlew)</h2>
<p>Gradle을 설치하지 않은 환경에서도 프로젝트를 빌드할 수 있도록 도와주는 내장 스크립트입니다.</p>
<ul>
<li>프로젝트 생성 시 포함된 <code>gradlew</code> 스크립트를 사용하면, 개발자 간 혹은 CI 서버 간에 <strong>완벽히 동일한 Gradle 버전</strong>을 사용하도록 강제할 수 있습니다.</li>
<li>협업 시 버전 호환성 문제를 방지하기 위해 로컬에 설치된 gradle 명령어보다 <code>./gradlew</code> 사용을 권장합니다.</li>
</ul>
<h2 id="6-빌드-라이프사이클-build-lifecycle">6. 빌드 라이프사이클 (Build Lifecycle)</h2>
<p>Gradle 빌드는 크게 3단계로 진행됩니다.</p>
<ol>
<li><strong>초기화 (Initialization)</strong>: <code>settings.gradle</code>을 읽어 빌드 대상 프로젝트(모듈)를 결정합니다.</li>
<li><strong>설정 (Configuration)</strong>: 모든 프로젝트의 빌드 스크립트를 실행하여 태스크들의 의존성 그래프를 구성합니다.</li>
<li><strong>실행 (Execution)</strong>: 결정된 태스크 그래프에 따라 실제 작업을 수행합니다.</li>
</ol>