
# Kwiecień

## Najważniejsze
- [Allow trailing comma in parameter list](#-%EF%B8%8F-allow-trailing-comma-in-parameter-list-rfc)
- [Reclassifying engine warnings](#-%EF%B8%8F-reclassifying-engine-warnings-rfc)
- [Attributes v2](#--attributes-v2-rfc)
- [Pipe Operator v2](#--pipe-operator-v2-rfc)
- ["match" expression](#--match-expression-rfc)

## RFC - Zaakceptowane

### 🔥 ✔️ Allow trailing comma in parameter list [[RFC](https://wiki.php.net/rfc/trailing_comma_in_parameter_list)]
W tym miesiącu przeprowadzono głosowanie w sprawie RFC odnoszącego się do wprowadzenia przecinka końcowego w liście parametrów. Tylko jedna osoba była przeciwna wprowadzeniu tej zmiany. Na szczęście pojawi się w nadchodzącym PHP 8 :).

### 🔥 ✔️ Reclassifying engine warnings [[RFC](https://wiki.php.net/rfc/engine_warnings)]
Od jakiegoś już czasu w PHP do wyrzucania błędów w większości korzysta się z wyjątków "Error". Korzysta z tego zarówno core języka, jak i w częściach pisanych przez programistów. Istnieje jednak jeszcze parę miejsc, w których core wyrzuca błędy typu _"Notice"_ lub _"Warning"_ . Takimi przykładami może być chociażby odwoływanie się do nieistniejącej zmiennej, albo poniższy kod:
```php
$x = null;
$y = $x['foo']; // produkuje "Notice"
```
W drugiej połowie 2019 roku Nikita Popov postanowiła podejść do tego problemu, i zaproponowała nową [modyfikację](https://wiki.php.net/rfc/engine_warnings) do PHP. Chodzi o przemianę niektórych błędów typu _"Notice"_ na _"Warning"_, a _"Warning"_ na _"Error exception"_. Choć temat powstał już dość dawno, to dopiero w tym miesiącu został zatwierdzony w trakcie głosowania. Trzeba więc liczyć się z tym, że od PHP 8, między innymi, odwoływanie się do nieistniejącej zmiennej wywoła _"Error exception"_ :). Listę wszystkich zmian znajdziecie na stronie tego [[RFC](https://wiki.php.net/rfc/engine_warnings)].

### ✔️ throw expression [[RFC](https://wiki.php.net/rfc/throw_expression)]
W poprzednim miesiącu utworzono RFC rozszerzające możliwości wyrzucania wyjątków (nazwałbym to "syntax sugar"). Natomiast 19 kwietnia to RFC zostało zaakceptowane, i ogłoszono jego wdrożenie do PHP 8. Dla przypomnienia, oto nowe możliwości, jakie czekają nas w nowej wersji:

```php
$callable = fn() => throw new Exception();
$value = $nullableValue ?? throw new InvalidArgumentException();
$value = $falsableValue ?: throw new InvalidArgumentException();
$value = !empty($array)
    ? reset($array)
    : throw new InvalidArgumentException();
$condition && throw new Exception();
$condition || throw new Exception();
$condition and throw new Exception();
$condition or throw new Exception();
```

### ✔️ Deprecations for PHP 8.0 [[RFC](https://wiki.php.net/rfc/deprecations_php_8_0)]
Od wersji PHP 8.0 nadawanie parametrów opcjonalnych przed wymaganymi w definicjach funkcji lub metod będzie uznawane jako "deprecated". Po utworzeniu takiego kodu:

```php
function foo($param_optional = null, $param_required) {
//           ^^ parametr opcjonalny , ^^ parametr wymagany
}
```

system wyrzuci błąd "Notice" odpowiednio o tym informujący:

> Deprecated: Required parameter $param_required follows optional parameter $param_optional in ... on line ...

Od grudnia więc trzeba będzie zacząć kod odpowiednio refaktoryzować :).

Źródło: [php.watch](https://php.watch/versions/8.0/deprecate-required-param-after-optional)

### Change Default PDO Error Mode [[RFC](https://wiki.php.net/rfc/pdo_default_errmode)]
Błędy PDO mają domyślnie ustawiony tryb "silent". Oznacza to, że jeśli coś się popsuje, nie pojawią się warningi lub fatale. Wyjątkiem może być przypadek, kiedy zostanie to dodatkowo zaimplementowane przez programistę. Pod koniec marca zaproponowano RFC, które ma to zmienić, aby domyślnym trybem błędów byłoby `PDO::ERRMODE_EXCEPTION`, a w tym miesiącu zatwierdzono decyzję o dodaniu tej zmiany do PHP 8.

## RFC - W trakcie głosowania
### 🔥 🚧 Attributes v2 [[RFC](https://wiki.php.net/rfc/attributes_v2)]
Już w [marcu](https://github.com/ktrzos/knowledge/blob/master/Monthly%20news%20-%20Marzec.md) wspominałem o powstaniu RFC proponującego wprowadzeniu natywnych adnotacji do PHP. Obecnie w tym języku dokonujemy tego poprzez PhpDoc. 20 kwietnia ruszyło głosowanie w tej sprawie, i wychodzi na to, że dość sporo ludzi jest zachwyconych tą funkcjonalnością. Póki co, większość głosujących jest "za" i głosują za wersją `<<ClassName>>` :). Samo głosowanie trwać będzie do 4 maja włącznie.

```php
<<ExampleAttribute>>
class Foo
{
    <<ExampleAttribute>>
    public const FOO = 'foo';
 
    <<ExampleAttribute>>
    public $x;
 
    <<ExampleAttribute>>
    public function foo(<<ExampleAttribute>> $bar) { }
}
 
$object = new <<ExampleAttribute>> class () { };
 
<<ExampleAttribute>>
function f1() { }
 
$f2 = <<ExampleAttribute>> function () { };
 
$f3 = <<ExampleAttribute>> fn () => 1;
```

### 🚧 throw expression [[RFC](https://wiki.php.net/rfc/throw_expression)]
Rozpoczęło się głosowanie nad RFC proponującym zwiększenie ilości miejsc, w jakich można wyrzucić wyjątek. W głównej mierze chodzi o zmodyfikowanie instrukcji `throw` w wyrażenie, aby było możliwe dodanie go m.in. do:
- operatorów `?:`, `??`, ternary operator;
- arrow functions.

Przykłady:
```php
$callable = fn() => throw new Exception();
$value = $nullableValue ?? throw new InvalidArgumentException();
$value = $falsableValue ?: throw new InvalidArgumentException();
 
$value = !empty($array)
    ? reset($array)
    : throw new InvalidArgumentException();

$condition && throw new Exception();
$condition || throw new Exception();
$condition and throw new Exception();
$condition or throw new Exception();
```


## RFC - Nowe

### 🔥 🆕 Pipe Operator v2 [[RFC](https://wiki.php.net/rfc/pipe-operator-v2)]
Przywrócony został temat wdrożenia operatora "pipe" w PHP. Wcześniej poruszono go aż 4 lata temu pod [TYM RFC](https://wiki.php.net/rfc/pipe-operator). Kto nie jest zaznajomiony, chodzi o możliwość przedstawienia kompozycji wielu funkcji:

```php
getPromotions(
  mostExpensiveItem(
    getShoppingList(
      getCurrentUser(),
      'wishlist'
    ),
    ['exclude' => 'onSale']
  ),
  $holiday
);
```

W sposób następujący:
```php
$holiday = "Lincoln's Birthday";
$result = getCurrentUser()
   |> fn($x) => getShoppingList($x, 'wishlist')
   |> fn($x) => mostExpensiveItem($x, ['exclude' => 'onSale'])
   |> fn($x) => getPromotions($x, $holiday);
```
Dla osób uwielbiających programowanie funkcyjne to wspaniała wiadomość. Niestety jednak społeczności PHP nie do końca podoba się taka notacja - wielu uważa ją za brzydką. Mimo to, wszystko zależy od tego jak to w przyszłości zostanie przegłosowane :). Ja jestem fanem, i mam nadzieję, że to wejdzie.

Należy jeszcze dodać, że powyższe RFC nawiązuje również do [innego, proponującego wdrożenie "partiali" do PHP](https://wiki.php.net/rfc/partial_function_application). Z ich pomocą można by wytworzyć następujący konstrukt kodu:
```php
$result = "Hello World"
    |> htmlentities(?, ENT_HTML5)
    |> explode(' ', ?)
    |> strtoupper(?)
    |> fn($x) => array_filter(fn($v) => $v != 'O');
```
### 🆕 Locale-independent float to string cast [[RFC](https://wiki.php.net/rfc/locale_independent_float_to_string)]
Odkryłem bardzo ciekawe RFC, które powstało w marcu, a mnie ominęło. Otóż dotyczy ono formatowania liczb zmiennoprzecinkowych podczas rzutowania na `string` w zależności od ustawionego lokalnego locale. Póki co, trwają rozmowy nad implementacją, ale już obecnie wygląda to dość ciekawie.

```php
setlocale(LC_ALL, "de_DE");
$f = 3.14;
 
(string) $f;		// 3,14 => 3.14
strval($f);		// 3,14 => 3.14
print_r($f);		// 3,14 => 3.14
var_dump($f);		// float(3,14) => float(3.14)
debug_zval_dump($f);	// float(3,14) => float(3.14)
settype($f, "string");	// 3,14 => 3.14
implode([$f])		// 3,14 => 3.14
xmlrpc_encode($f);	// 3,14 => 3.14

echo var_export($f, true);	// 3.14
echo serialize($f);		// d:3.14
echo json_encode($f);		// 3.14
```

### 🆕 PHP Namespace in core [[RFC}(https://wiki.php.net/rfc/php-namespace-in-core)]
Tak naprawdę to RFC zostało utworzone pod koniec ubiegłego miesiąca, ale dopiero teraz je zauważyłem. Jego celem jest wdrożenie globalnej przestrzeni nazw dla PHP pod przestrzenią `\PHP`, która jest zarezerwowana przez twórców od dłuższego już czasu. Póki co [trwa dość spora dyskusja](https://externals.io/message/109651) w tym temacie. Kto wie co jeszcze w tym temacie zostanie postanowione, lub zmienione :).

### 🆕 Stricter type checks for arithmetic/bitwise operators [[RFC](https://wiki.php.net/rfc/arithmetic_operator_type_checks)]
Nikita Popov stworzyła [RFC](https://wiki.php.net/rfc/arithmetic_operator_type_checks) z kolejną poprawką do PHP. RFC proponuje wyrzucanie błędów gdy dokonuje się operacji arytmetycznych lub bitowych na tablicach, referencjach lub obiektach. Przykładową, błędną implementacją jest chociażby poniższy kod:

```
var_dump([] % [42]); // int(0)
```

Wszystkie operacje +, -, *, /, **, %, <<, >>, &, |, ^, ~, ++, -- (poza jednym wyjątkiem dodawaniem tablic) miałyby powodować `TypeError`. Jest nawet pomysł o rozszerzenie tego RFC o podobne błędy dla stringów nie mających w swojej zawartości liczb (np. `"a" + "b"` wyrzuciłoby błąd).
Więcej informacji [TUTAJ](https://wiki.php.net/rfc/arithmetic_operator_type_checks).

### 🆕 Always available JSON extension [[RFC](https://wiki.php.net/rfc/always_enable_json)]
Powstało RFC proponujące, aby rozszerzenie PHP JSON było niemożliwe do wyłączenia od PHP 8. W chwili obecnej jest to możliwe przy pomocy:
```
./configure –disable-json
```

## RFC - Odrzucone
###  🔥 ❌ "match" expression [[RFC](https://wiki.php.net/rfc/match_expression)]
Kto wie czy to nie początek pattern matchingu w PHP? Nie można mieć jednak dużych oczekiwań, i na początek taka funkcjonalność by w pełni wystarczyła. To nowo utworzone RFC proponuje wprowadzenie nowego wyrażenia `match`, które umożliwiałoby szereg usprawnień z poziomu kodu:

```php
match ($condition) {
    1 => {
        foo();
        bar();
    },
    2 => baz(),
}
 
$expressionResult = match ($condition) {
    1, 2 => foo(),
    3, 4 => bar(),
    default => baz(),
};
```
Dnia 25 kwietnia rozpoczęło się głosowanie w tej sprawie. Póki co, większość głosujących jest niestety przeciw, więc prawdopodobnie nie zobaczymy tej funkcjonalności w PHP w najbliższym czasie. Jest jednak światełko w tunelu, gdyż wielu mówi o chęci zobaczenia tej funkcjonalności w nieco innej formie - parę rzeczy jest jeszcze niejasnych, a osoba tworząca RFC ponoć ruszyła z głosowaniem zbyt wcześnie.
Póki co, trzeba zadowolić się bibliotekami zewnętrznymi imitującymi pattern matching, jak chociażby [Deji69/encase-matching](https://github.com/Deji69/encase-matching).

### ❌ Obiekty Request/Response [[RFC](https://wiki.php.net/rfc/request_response)]

RFC odnoszące się do obiektów Request/Response zostało w głosowaniu odrzucone przez społeczność PHP. Według wielu osób funkcjonalność ta wprowadzałaby tylko niepotrzebną alternatywę dla już istniejących rozwiązań. Do tego już w chwili obecnej istnieją odpowiednie implementacje stworzone chociażby przez Symfony, oraz opisane w PSR. 


### ❌ Userspace operator overloading [[RFC](https://wiki.php.net/rfc/userspace_operator_overloading)]

Zakończyło się głosowanie nad RFC odnoszącym się do tematu "Userspace operator overloading", w którym to 38 osób było "za", a 28 "przeciw". W związku z czym zaproponowana implementacja funkcjonalności została odrzucona. Dla przypomnienia, RFC proponowało dodanie możliwości przeciążania różnego rodzaju operatorów (m.in. +, i, *, /, **, <<, >> itp.), co zwiększałoby możliwości obiektów.
Wielu głosujących jest zdania, że niewiele brakowało autorowi do osiągnięcia konsensusu z osobami głosującymi przeciw. Prawdopodobne jest więc, że w niedalekiej przyszłości ujrzymy kolejne, nieco zmodyfikowane lub ograniczone RFC w tym temacie.

Rozmowę głosujących na temat RFC można przeczytać tutaj: [LINK](https://externals.io/message/109241).


### ❌ Compact Object Property Assignment [[RFC](https://wiki.php.net/rfc/compact-object-property-assignment)]
Nie zobaczymy kompaktowej obsługi przypisywania wartości własnościom obiektów w PHP.  Przynajmniej nie w takiej formie, jaką proponuje RFC. Zdecydowana większość głosujących była przeciwnych wprowadzeniu tej funkcjonalności.

## Inne

### PhpStorm 2020.1
Opublikowano pierwszą większą aktualizację PhpStorma tego roku. Największe nowości:
- zintegrowanie wtyczki do Composera;
- dodano nowe możliwości w integracji z biblioteką PhpUnit;
- dodatkowe możliwości metadanych w PhpStorm;
- nowe inspekcje, polepszono type inference i dodano machine learning do podpowiedzi używanych metod.

Więcej informacji: [JetBrains.com](https://blog.jetbrains.com/phpstorm/2020/04/phpstorm-2020-1-release/)
