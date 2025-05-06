<blockquote>
<p>아래 코드는 try절 안에서 예외가 발생했고 catch절에서 해당 예외를 전달했지만 finally절에서 return &quot;OK&quot;를 통해 정상적으로 소스코드가 실행된 척 하기 때문에 예외 정보가 무시되고 정상적으로 작동하는 것처럼 보인다.<br /> 어떻게 하면 아래 코드를 해결 할 수 있을까? </p>
</blockquote>
<pre><code class="language-java">// 문제
public class DoNotUseReturnSample {
    public String test() {
        try{
            throw new Exception(&quot;예외 발생&quot;);
        } catch(Exception e) {
            throw e;
        } finally {
            return &quot;OK&quot;;
        }
    }
}

public class DoNotUseReturnTest {
    public static void main(String[] args) {
        DoNotUseReturnSample sample = new DoNotUseReturnSample();

        System.out.println(sample.test());
    }
}</code></pre>
<hr />
<p>해결방안 
예외를 무시하는 문제를 미리 방지하려면 불필요한 finally절을 사용하지 말고 반드시 파일과 같은 리소스를 사용한 후 반환하는 등의 코드만 사용해야 한다. </p>
<pre><code class="language-java">public class DoNotUseReturnSample {
    public String test() throws Exception{
        try{
            throw new IOException(&quot;예외 발생&quot;);
        } catch(IOException e) {
            throw e;
        } 
    }
}</code></pre>
<hr />