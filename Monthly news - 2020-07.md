
# Monthly news - Czerwiec

## Najważniejsze
- Zaakceptowane: [Named Arguments](#-%EF%B8%8F-named-arguments-rfc)
- Zaakceptowane: [Nullsafe operator](#-%EF%B8%8F-nullsafe-operator-rfc)
- Odrzucone: [Strict operators directive](#---strict-operators-directive-rfc)

## RFC - Zaakceptowane

### 🔥 ✅ Named Arguments [[RFC](https://wiki.php.net/rfc/named_params)]
Dnia 24 lipca zdecydowaną większością (57 głosów "za", 18 "przeciw") zostało przegłosowane wprowadzenie nazwanych argumentów do PHP. Jest to kolejna dość spora funkcjonalność, której w tym języku nie było. Osobiście cieszę się z nowej opcji, ale wielu boi się, że będzie ona wykorzystywana w niepoprawny sposób. Czas pokaże :).

```php
// W chwili obecnej:
array_fill(0, 100, 50);
 
// Możliwe od PHP 8:
array_fill(start_index: 0, num: 100, value: 50);
```

### 🔥 ✅ Nullsafe operator [[RFC](https://wiki.php.net/rfc/nullsafe_operator)]
Wszyscy dobrze wiemy jak czasami irytujące jest upewnianie się, czy zmienna jest lub nie jest `null`.
```php
$country =  null;
 
if ($session !== null) {
    $user = $session->user;
 
    if ($user !== null) {
        $country = $user->getAddress()->country;
    }
}
```

Na tego typu problemy w Javascript ES2020 wprowadzono operator `?.`. Ktoś chyba uznał, że coś takiego przydałoby się to w PHP 🙂.

```php
$country = $session?->user?->getAddress()->country;
```

### ✅ Saner numeric strings [[RFC](https://wiki.php.net/rfc/saner-numeric-strings)]
Zasugerowano modyfikację logiki interpretowania łańcuchów znaków zawierających liczby. W chwili obecnej taki łańcuch, który zawiera liczby, może być interpretowany na wiele sposobów, i posiada wiele nieprzewidywalnych zachowań:
```php
var_dump("123" == "123.0");  // bool(true)
var_dump("123" == "   123"); // bool(true)
var_dump("123" == "123   "); // bool(false)
var_dump("123" == "123abc"); // bool(false)

var_dump(123 + "123");    // int(246)
var_dump(123 + "   123"); // int(246)

function foo(int $i) { var_dump($i); }
foo("123");    // int(123)
foo("   123"); // int(123)
foo("123   "); // int(123) with E_NOTICE "A non well formed numeric value encountered"
foo("123abc"); // int(123) with E_NOTICE "A non well formed numeric value encountered"
foo("string"); // TypeError
```

Zadaniem tego RFC jest ograniczenie ilości sposobów interpretowania "numerycznego łańcucha znaków" do takiego stopnia, aby w jego skład wchodziły tylko te łańcuchy, które mają białe znaki przed lub po oraz tylko liczby. Reszta powinna wyrzucać błąd `TypeError`.
### ✅ Saner string to number comparisons [[RFC](https://wiki.php.net/rfc/string_to_number_comparison)]
Nikita Popov przy pomocy tego RFC wprowadzi nieco bardziej oczywiste zachowanie przy porównywaniu łańcuchów znaków z liczbami.
```
Comparison    | Before | After
------------------------------
 0 == "0"     | true   | true
 0 == "0.0"   | true   | true
 0 == "foo"   | true   | false
 0 == ""      | true   | false
42 == "   42" | true   | true
42 == "42foo" | true   | false
```
Zmiany mają dotyczyć:
- operatorów "non-strict": `<=>`, `==`, `!=`, `>`, `>=`, `<`, `<=`;
- funkcji `in_array()`, `array_search()` i `array_keys()` z `$strict` ustawionym na `false` (wartość domyślna);
- funkcji sortujące `sort()`, `rsort()`, `asort()`, `arsort()` oraz `array_multisort()` z `$sort_flags` ustawioną na `SORT_REGULAR` (wartość domyślna).

### ✅ Allow trailing comma in closure use lists [[RFC](https://wiki.php.net/rfc/trailing_comma_in_closure_use_list)]
Zmieniono już wiele miejsc, gdzie dopuszczane jest dodawanie "trailing commas", lecz występują jeszcze miejsca, gdzie nie jest to możliwe.  Takim miejscem jest chociażby `use` przy funkcjach. Jednakże i tutaj zostanie to uzupełnione, gdyż 29 lipca w pełni pozytywnie przegłosowano tą zmianę :).

```php
$longArgs_longVars = function (
    $longArgument,
    $longerArgument,
    $muchLongerArgument,  // "trailing commas" będą obecnie możliwe w PHP 8.0
) use (
    $longVar1,
    $longerVar2,
    $muchLongerVar3 // <-- Tutaj już to nie działa :)
) {
   // body
};
```

### ✅ Configurable string length in getTraceAsString() [[RFC](https://wiki.php.net/rfc/throwable_string_param_max_len)]
W chwili obecnej poniższe metody zwracają zawartości o maksymalnej długości do 15 bajtów:
- `Throwable->getTraceAsString()`
- `Throwable->__toString()`
RFC proponuje, aby ta długość była możliwa do skonfigurowania przy pomocy nowej opcji konfiguracyjnej:
```
zend.exception_string_param_max_len=15
```

### ✅ Treat namespaced names as single token [[RFC](https://wiki.php.net/rfc/namespaced_names_as_token)]
Obecnie PHP traktuje przestrzenie nazw jako sekwencję identyfikatorów. RFC proponuje zmianę tego w taki sposób, aby cała przestrzeń nazw była traktowana jako jeden token. Głównym celem jest zabezpieczenie przypadków, w których używane są w przestrzeniach nazw słowa zarezerwowane w PHP. Na przykład, od PHP 7.4 istnieje zarezerwowane słowo klucze `fn`. Zewnętrzna biblioteka posiadająca przestrzeń nazw zawierającą to słowo klucze będzie wyrzucać błąd "Fatal".

Przykład:
```php
namespace myLib\fn

function someFunction() { ... }
```

W skrócie: RFC zabezpieczy wsteczną kompatybilność na nowsze wersje PHP z nowymi funkcjonalnościami.

Więcej szczegółów znajdziecie w [zawartości tego RFC](https://wiki.php.net/rfc/shorter_attribute_syntax) 🙂.

### ✅ Don't automatically unserialize Phar [[RFC](https://wiki.php.net/rfc/phar_stop_autoloading_metadata)]
Jak się okazuje, przez wykonanie tego kodu:
```php
file_exists("phar://...somepath.extension/file/within/phar.ext")
```
plik `phar.ext` zostanie automatycznie odserializowany, co doprowadzi do pobrania kodu oraz jego wywołania. Wydaje się to niebezpieczne, więc zostało zaproponowane rozwiązanie, aby odserializowanie zostało dokonane dopiero po wywołaniu czegoś w na wzór `Phar->getMetadata()`. Głosowanie w tej sprawie trwało do 4 sierpnia, i 25 osób było "za" a 0 "przeciw".


## RFC - W trakcie głosowania

### 🚧 PHP Namespace Policy [[RFC](https://wiki.php.net/rfc/php_namespace_policy)]
RFC odnoszące się do stworzenia pewnych dodatkowych zasad co do przestrzeni nazw `\PHP` przeszło w tryb "Vote". Głosowanie potrwa do 8 sierpnia, ale już teraz widać, że większość głosujących nie jest tą zmianą pozytywnie zainteresowana. RFC ma na celu:
- Zarezerwowanie przestrzeni nazw `\PHP`.
- Ustalenie zasad, w których powyższa przestrzeń będzie używana.
- Określenie wytycznych w sprawie migrowania istniejących już klas do przestrzeni w przyszłości.

## RFC - Nowe

### 🆕 Language Constructs Syntax Changes [[RFC](https://wiki.php.net/rfc/language-constructs-syntax-changes)]
RFC proponuje dwie drobne zmiany składni:
1. `declare(strict_types: 1);` na `declare strict_types = 1;`
2. `__halt_compiler();`  na `__halt_compiler`


### 🆕 Deprecations for PHP 8.1 [[RFC](https://wiki.php.net/rfc/deprecations_php_8_0)]
W skład tego RFC wchodzi parę rzeczy, które zostaną oznaczone jako "deprecated" w PHP 8.1, a zostaną całkowicie usunięte w PHP 9:
- Ustawienie jako przestarzałe funkcji `date_sunrise()` i `date_sunset()` na rzecz `date_sun_info()`.
- Ustawienie, aby funkcje `key()`, `current()`, `next()`, `prev()`, `reset()` nie operowały na obiektach, co dotychczas jest możliwe. Rozwiązaniem jest wprowadzenie rzutowania obiektu na tablicę zanim operacja zostanie dokonana przez daną funkcję.
- Uniemożliwienie wywołania funkcji `mb_check_encoding()`,  `get_class()`, `get_parent_class()` i `get_called_class()` bez argumentów.
- Ustawienie stałych `FILE_BINARY`, `FILE_TEXT` jako "deprecated".
- Ustawienie flagi `t` używanej przy `fopen()` jako "deprecated".
- Uniemożliwienie podania wartości typu `boolean` jako argument do `IntlCalendar::roll()`.
- Uniemożliwienie dostania się do metod lub własności statycznych traitów.
```php
trait T {
    public static $foo;
}
class C {
    use T;
}
var_dump(T::$foo); // Dozwolone
```


### 🆕 Shorter Attribute Syntax Change [[RFC](https://wiki.php.net/rfc/shorter_attribute_syntax_change)]
Wiele osób nie może się pogodzić z nową składnią dla atrybutów - rozpoczynanie od prefiksu `@@`. Niniejsze RFC chce tą składnię zamienić na `#[]`. Argumenty:

- Mogą wystąpić problemy z parsowaniem.
- Nowa składnia będzie miała wyraźne rozpoczęcie atrybutu `#[` oraz jego zakończenie `]`, co pozwala to łatwiej parsować, a i daje więcej możliwości rozwinięcia funkcjonalności.
- Ze składni `@@` nie korzysta żaden inny język. Nie jest więc w żaden sposób sprawdzona.
- Operator `@` prawdopodobnie nigdy nie zniknie z PHP, w związku z czym nie będzie możliwości przejścia `@@` na pojedynczy znak `@`.

## RFC - Odrzucone

### 🔥 ❌  Strict operators directive [[RFC](https://wiki.php.net/rfc/strict_operators)]
Celem RFC jest wprowadzenie nowej dyrektywy `strict_operators` przyjmującą wartości `0` lub `1`. Jej zadaniem ma być ograniczenie żonglowania typami w operatorach powodujących czasami nieoczekiwanie wyniki. Przykłady:

```php
$a = '42';
$b = 10;
$c = '9 eur';
 
if (($a > $b) && ($b > $c) && ($c > $a)) {
   // Unexpected 
}
```
```php
$red = '990000';
$purple = '9900e2';
 
$red == $purple; // true
```
```php
$a = ['x' => 1, 'y' => 22];
$b = ['y' => 10, 'x' => 15];
 
$a > $b; // true
$a < $b; // true
```
Z ważniejszych zmian wchodzących w skład RFC, ma być:
- Operatory `==`, `!=`, `<`, `>`, `<=`, `>=`, `<=>` będą mogły być zastosowane tylko dla typów `int` lub `float` (w innym przypadku zostanie wyrzucony `TypeError`.
- Operator konkatenacji zadziała tylko na `null`, `int`, `float`, `string` i `stringable`.
- Brak zmian dla operatorów `===`, `!==`, `?:`, `??`, `&&`, `||`, `!`, `and`, `or`, and `xor`.

**Update:** RFC wycofano na rzecz [Saner string to number comparisons](https://wiki.php.net/rfc/string_to_number_comparison).

### ❌ Make constructors and destructors return void [[RFC](https://wiki.php.net/rfc/make_ctor_ret_void)]
RFC proponowało określić typ zwracany dla konstruktora i destruktora klas, aby konsekwentne móc określać sygnaturu dla tych metod magicznych, jako że ich reszta [została już określona](https://wiki.php.net/rfc/magic-methods-signature). Niestety jednak, propozycja nie zdobyła w głosowaniu 2/3 głosów, w związku z czym została odrzucona (34 było "za", 22 "przeciw").

### ❌ StackFrame class [[RFC](https://wiki.php.net/rfc/stack-frame-class)]
W chwili obecnej funkcja `debug_backtrace()` zwraca tablicę tablic z informacjami o linii, pliku, klasie, typach, obiektach, argumentach i nazw funkcji jako klucze. RFC proponuję alternatywę tej funkcji, która zwracałaby tablicę obiektów klasy `StackFrame`. Takowa tablica zwracana byłaby poprzez statyczną metodę `getTrace()`. Głównym celem tego rozwiązania jest zmniejszenie zużycia pamięci, oraz poprawa czytelności. Tak jak w poprzednim RFC, głosowanie w tym temacie zakończy się 4 sierpnia.

```php
final class StackFrame implements ArrayAccess
{
    public readonly string $file;
 
    public readonly int $line;
 
    public readonly ?string $function;
 
    public readonly ?string $class;
 
    public readonly ?object $object;
 
    public readonly ?string $object_class;
 
    public readonly ?\Closure $closure;
 
    public readonly array $args;
 
    public static function getTrace(
	    int $options = DEBUG_BACKTRACE_PROVIDE_OBJECT,
	    int $limit = 0
    ): array {}
 
    public function getFile(): string {}
 
    public function getLine(): int {}
 
    public function getFunction(): ?string {}
 
    public function getClass(): ?string {}
 
    public function getObject(): ?object {}
 
    public function getObjectClass(): ?string {}
 
    public function getClosure(): ?\Closure {}
 
    public function getType(): ?string {}
 
    public function getArgs(): array {}
}
```
Przykład:
```php
function frames() {
    return StackFrame::getTrace();
}
$frame = frames()[0];
 
var_dump([
    'file' => $frame['file'],
    'line' => $frame['line'],
    'function' => $frame['function'],
    'class' => $frame['class'],
    'type' => $frame['type'],
    'object' => $frame['object'],
    'args' => $frame['args'],
]);
```

## Inne
- Microsoft zapowiedział, że nie będzie prowadził wsparcia dla PHP 8. Firma więc nie będzie dostarczać oficjalnych buildów tego języka na platformę Windows. Zapewne zajmie się tym ktoś inny :).
- Od 4 sierpnia nastąpi etap "feature freeze" dla PHP 8. Pozostaje więc już niewiele czasu na wdrożenie nowych funkcjonalności do PHP8, choć już trochę ich się narobiło :).
- Opublikowano PHP8 w wersji "alpha2". Link do ściągnięcia: [LINK](https://downloads.php.net/~carusogabriel/)
