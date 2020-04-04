---


---

<h1 id="monthly-news---marzec">Monthly news - Marzec</h1>
<h2 id="najważniejsze">Najważniejsze</h2>
<h3 id="opublikowano-harmonogram-publikacji-php-8">Opublikowano harmonogram publikacji PHP 8</h3>
<p>W skrócie:</p>
<ul>
<li>Pierwsza, stabilna wersja PHP 8 zostanie opublikowana w pierwszym tygodniu grudnia.</li>
<li>Lista nowych funkcjonalności lub większych zmian wchodzących w skład PHP 8 będzie rozszerzana do lipca 2020 włącznie.</li>
<li>W świat zostanie wypuszczonych 6 wersji PHP 8.0 RC. Pierwszy ma się pojawić na początku września, a ostatni w drugiej połowie listopada.</li>
</ul>
<p>Źródło: <a href="https://wiki.php.net/todo/php80">php.net</a>, <a href="https://thephp.website/en/issue/php8-release-schedule/">thephp.website</a>.</p>
<h3 id="nowa-funkcja-str_contains">Nowa funkcja: <a href="https://github.com/php/php-src/commit/1668ad7cb17213e67e42994e0c6911e302a3c3c5">str_contains()</a></h3>
<p>Do PHP zmergowano kod nowej funkcji <code>str_contains()</code>. Pojawi się ona w PHP 8.</p>
<h3 id="nowa-funkcja-get_debug_type-rfc">Nowa funkcja: get_debug_type() <a href="https://wiki.php.net/rfc/get_debug_type">[RFC]</a></h3>
<p>Zatwierdzono RFC odnośnie funkcji <code>get_debug_type()</code>. Funkcja ta zwraca typ zmiennej, którą poda się w argumencie.</p>
<pre class=" language-php"><code class="prism  language-php"><span class="token variable">$x</span> <span class="token operator">=</span> <span class="token string">"test"</span><span class="token punctuation">;</span>
<span class="token function">get_debug_type</span><span class="token punctuation">(</span><span class="token variable">$x</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// "string"</span>
</code></pre>
<h2 id="section">2020-03-22</h2>
<h3 id="głosowanie-na-zmienne-instancyjne-readonly-rfc">Głosowanie na zmienne instancyjne “readonly” [<a href="https://wiki.php.net/rfc/write_once_properties">RFC</a>]</h3>
<p>Rozpoczęło się głosowanie nad możliwością ustawienia własnościom klas trybu “readonly”.</p>
<h3 id="obiekty-requestresponse-rfc">Obiekty Request/Response [<a href="https://wiki.php.net/rfc/request_response">RFC</a>]</h3>
<p>Update <strong>2020-04-03</strong>: RFC zostało odrzucone.<br>
Rozpoczęło się głosowanie na temat wprowadzenia trzech nowych klas i interfejsu do głównej przestrzeni nazw PHP.</p>
<ul>
<li>SapiRequest</li>
<li>SapiResponse, SapiResponseInterface</li>
<li>SapiResponseSender</li>
</ul>
<p>Powyższe klasy mają za zadanie umacnić programowanie obiektowe w PHP poprzez zmianę sposobu pobierania danych z żądania, oraz wysyłania odpowiedzi na to żądanie przy pomocy obiektów. Przykładowo, zamiast odwoływać się do <code>$_GET</code> będzie można skorzystać z obiektu w sposób następujący: <code>$request-&gt;query</code>.</p>
<h2 id="section-1">2020-03-26</h2>
<h3 id="przecinek-końcowy-w-liście-parametrów-rfc">Przecinek końcowy w liście parametrów <a href="https://wiki.php.net/rfc/trailing_comma_in_parameter_list">[RFC]</a></h3>
<p>Utworzone zostało nowe RFC proponujące umożliwienie dodania przecinka końcowego w liście parametrów do funkcji oraz metod. Funkcja ta istnieje już od jakiegoś czasu dla tablic.</p>
<pre class=" language-php"><code class="prism  language-php"><span class="token keyword">class</span> <span class="token class-name">MyClass</span> <span class="token punctuation">{</span>
    <span class="token keyword">private</span> <span class="token keyword">function</span> <span class="token function">__construct</span><span class="token punctuation">(</span>
        string <span class="token variable">$param1</span><span class="token punctuation">,</span>
        string <span class="token variable">$param2</span><span class="token punctuation">,</span>
        string <span class="token variable">$param3</span><span class="token punctuation">,</span>
        string <span class="token variable">$param4</span><span class="token punctuation">,</span>
        int <span class="token variable">$param5</span> <span class="token comment">// &lt;-- tutaj mógłby być przecinek :)</span>
    <span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token comment">// ...</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<h3 id="constructor-property-promotion-rfc">Constructor Property Promotion <a href="https://wiki.php.net/rfc/constructor_promotion">[RFC]</a></h3>
<p>Zaproponowano zmniejszenie boilerplate potrzebnego do określenia początkowych wartości zmiennych instancyjnych klas poprzez konstruktor. Przykład:</p>
<pre class=" language-php"><code class="prism  language-php"><span class="token keyword">class</span> <span class="token class-name">Point</span> <span class="token punctuation">{</span>
    <span class="token keyword">public</span> float <span class="token variable">$x</span><span class="token punctuation">;</span>
    <span class="token keyword">public</span> float <span class="token variable">$y</span><span class="token punctuation">;</span>
    <span class="token keyword">public</span> float <span class="token variable">$z</span><span class="token punctuation">;</span>
 
    <span class="token keyword">public</span> <span class="token keyword">function</span> <span class="token function">__construct</span><span class="token punctuation">(</span>
        float <span class="token variable">$x</span> <span class="token operator">=</span> <span class="token number">0.0</span><span class="token punctuation">,</span>
        float <span class="token variable">$y</span> <span class="token operator">=</span> <span class="token number">0.0</span><span class="token punctuation">,</span>
        float <span class="token variable">$z</span> <span class="token operator">=</span> <span class="token number">0.0</span>
    <span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token variable">$this</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token property">x</span> <span class="token operator">=</span> <span class="token variable">$x</span><span class="token punctuation">;</span>
        <span class="token variable">$this</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token property">y</span> <span class="token operator">=</span> <span class="token variable">$y</span><span class="token punctuation">;</span>
        <span class="token variable">$this</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token property">z</span> <span class="token operator">=</span> <span class="token variable">$z</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Jak widać wyżej, każda z własności występuje w kodzie aż 4 razy. RFC proponuje zmniejszenie tego do:</p>
<pre><code>class Point {
    public function __construct(
        public float $x = 0.0,
        public float $y = 0.0,
        public float $z = 0.0
    ) {}
}
</code></pre>
<p>Tego typu rozwiązanie zastosowane zostało już <a href="http://m.in">m.in</a>. w języku <a href="https://docs.hhvm.com/hack/classes/constructors#constructor-parameter-promotion">Hack</a>.</p>
<h2 id="section-2">2020-03-28</h2>
<p>Nadal trwa głosowanie nad drugim RFC dotyczącym <a href="https://wiki.php.net/rfc/userspace_operator_overloading">przeciążania operatorów w PHP</a>. Obecnie 30 osób jest za zaimplementowaniem tej funkcjonalności, a 16 przeciw. Prawdopodobnie w końcu uda się to przepchnąć do przodu :).</p>
<h2 id="section-3">2020-03-31</h2>
<p>Ruszyło głosowanie w sprawie dodania do PHP 8.0 funkcjonalności kompaktowego przypisywania wartości własnościom obiektów (z ang. “Compact Object Property Assignment”). W skrócie o co chodzi:</p>
<pre class=" language-php"><code class="prism  language-php"><span class="token comment">// zamiast tego:</span>
<span class="token variable">$myObj</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token property">a</span> <span class="token operator">=</span> <span class="token number">1</span><span class="token punctuation">;</span>
<span class="token variable">$myObj</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token property">b</span> <span class="token operator">=</span> <span class="token number">2</span><span class="token punctuation">;</span>
<span class="token variable">$myObj</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token property">c</span> <span class="token operator">=</span> <span class="token number">3</span><span class="token punctuation">;</span>

<span class="token comment">// będzie można to robić tak:</span>
<span class="token variable">$myObj</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token punctuation">[</span>
    a <span class="token operator">=</span> <span class="token number">1</span><span class="token punctuation">,</span>
    b <span class="token operator">=</span> <span class="token number">2</span><span class="token punctuation">,</span>
    c <span class="token operator">=</span> <span class="token number">3</span><span class="token punctuation">,</span>
<span class="token punctuation">]</span><span class="token punctuation">;</span>
</code></pre>
<p>Póki co zdecydowana większość głosujących jest za wdrożeniem tego pomysłu, a więc prawdopodobnie zostanie to wdrożone :). Więcej informacji o tej funkcjonalności w <a href="https://wiki.php.net/rfc/compact-object-property-assignment">RFC</a>.</p>

