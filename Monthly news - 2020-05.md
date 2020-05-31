
# Monthly news - Maj

## Najważniejsze
- Zaakceptowane: [Attributes v2](#-%EF%B8%8F-attributes-v2-rfc)
- Zaakceptowane [Mixed Type v2](#-%EF%B8%8F-mixed-type-v2-rfc)
- Zaakceptowane: [non-capturing catches](#-%EF%B8%8F-non-capturing-catches-rfc)
- W trakcie głosowania: [Ensure correct signatures of magic methods](#--ensure-correct-signatures-of-magic-methods-rfc)
- Nowe: [Named Arguments](#---named-arguments-rfc)
- Nowe: ["Deprecated" Attribute](#---deprecated-attribute-rfc)
- Nowe: [Match expression v2](#---match-expression-v2-rfc)
- Odrzucone: [Match expression](#--match-expression-rfc)

## RFC - Zaakceptowane

### 🔥 ✔️ Attributes v2 [[RFC](https://wiki.php.net/rfc/attributes_v2)]
Tak jak podejrzewałem już w poprzednim miesiącu, atrybuty w wersji drugiej zostały zaakceptowane i zostaną wdrożone do PHP 8. Przyjęta zostałą wersja "<< ... >>". Dla przypomnienia, ich celem jest funkcjonowanie jak adnotacje w języku Java. Wiele bloków PhpDocs zostanie tą funkcjonalnością zastąpionych :).


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

### 🔥 ✔️ Mixed Type v2 [[RFC](https://wiki.php.net/rfc/mixed_type_v2)]
Zatwierdzono wprowadzenie typu `mixed` do PHP 8.
```php
class Example {
    public mixed $exampleProperty;
    public function foo(mixed $foo): mixed {}
}
```
Należy pamiętać oparu rzeczach:
1. Nowy typ `mixed` jest pseudotypem. Oznacza to, że nie można rzutować zmiennej na ten typ. Ponadto, nie ma funkcji `is_mixed()`, oraz `get_debug_type()` nie zwróci `mixed` dla zmiennej o tym typie.
2. Nie można tworzyć typu union razem z typem `mixed`. Przykład **błędnego** typu union: `int|mixed`.
3. W PHP 8 typ `mixed` będzie domyślnym typem jeśli nie określono jakiegokolwiek innego typu dla zmiennych, lub parametrów. W przypadku wartości zwracanej w funkcjach i metodach, będzie to `mixed|void`.

### 🔥 ✔️ non-capturing catches [[RFC](https://wiki.php.net/rfc/non-capturing_catches)]
RFC proponujące umożliwienie skrócenia składni try-catch w momencie, gdy nie korzystamy z zmiennej przechowującej dane o wyjątku:
```php
try {
    doSomething();
} catch (AppException $ex) {
    echo "Message";
}
```
będzie mógł zostać przerobiony na taki:
```php
try {
    doSomething();
} catch (AppException) {
    echo "Message";
}
```

### ✔️ Constructor Property Promotion [[RFC](https://wiki.php.net/rfc/constructor_promotion)]
Do PHP 8 dojdzie możliwość definiowania zmiennych instancyjnych już na poziomie konstruktora. Celem tego RFC jest zminimalizowany boilerplate. Zamiast takiego kodu:
```php
class Point {
    public float $x;
    public float $y;
    public float $z;
 
    public function __construct(
        float $x = 0.0,
        float $y = 0.0,
        float $z = 0.0,
    ) {
        $this->x = $x;
        $this->y = $y;
        $this->z = $z;
    }
}
```
Wystarczy stworzyć następujący:
```php
class Point {
    public function __construct(
        public float $x = 0.0,
        public float $y = 0.0,
        public float $z = 0.0,
    ) {}
}
```

### ✔️ Add str_starts_with() and str_ends_with() functions [[RFC](https://wiki.php.net/rfc/add_str_starts_with_and_ends_with_functions)]
Do PHP 8 dojdą dwie nowe funkcje operujące na łańcuchach znaków:
```php
str_starts_with(string $haystack, string $needle): bool
str_ends_with(string $haystack, string $needle): bool
```

### ✔️ Locale-independent float to string cast [[RFC](https://wiki.php.net/rfc/locale_independent_float_to_string)]
Zatwierdzone zostało RFC dotyczące formatu wyświetlania danych typu `float` jako `string` w oparciu o ustawione w PHP locale:
```php
setlocale(LC_ALL, "de_DE");
$f = 3.14;
 
(string) $f;		// 3,14 => 3.14
strval($f);		// 3,14 => 3.14
print_r($f);		// 3,14 => 3.14
var_dump($f);		// float(3,14) => float(3.14)
debug_zval_dump($f);	// float(3,14) => float(3.14)
settype($f, "string");	// 3,14 => 3.14
implode([$f]);		// 3,14 => 3.14
xmlrpc_encode($f);	// 3,14 => 3.14
```
### ✔️ get_resource_id() [[PR](https://github.com/php/php-src/pull/5427)]
Do PHP został dodany kod odnoszący się do nowej funkcji `get_resource_id()`. Jej zadaniem jest funkcjonowanie podobnie co `(int) $resource`, ale w bardziej oczywisty sposób.

### ✔️ Unbundle ext/xmlrpc [[RFC](https://wiki.php.net/rfc/unbundle_xmlprc)]
Rozszerzenie ext/xmlrpc wbudowane do PHP opiera się na [libxmlrpc-epi](https://sourceforge.net/projects/xmlrpc-epi "https://sourceforge.net/projects/xmlrpc-epi/"), które okazuje się już porzucone. Dzięki temu RFC wspomniane rozszerzenie ma nie być już dołączane do core PHP, a do PECL ([PHP Extension Community Library](https://pecl.php.net)).

### ✔️ Always available JSON extension [[RFC](https://wiki.php.net/rfc/always_enable_json)]
Od PHP 8 rozszerzenie JSON będzie niemożliwe do wyłączenia, i nie będzie już dołączane do PHP jako biblioteka, a jako statycznie zbudowana część.

## RFC - W trakcie głosowania

### 🔥 🚧 Ensure correct signatures of magic methods [[RFC](https://wiki.php.net/rfc/magic-methods-signature)]
Jako że zatwierdzono nowy typ w PHP - `mixed` - postanowiono od razu podejść do tematu braku typów dla metod magicznych klas. Autor RFC zaproponował następujące otypowanie:
```php
Foo::__call(string $name, array $arguments): mixed;
Foo::__callStatic(string $name, array $arguments): mixed;
Foo::__clone(): void;
Foo::__debugInfo(): ?array;
Foo::__get(string $name): mixed;
Foo::__isset(string $name): bool;
Foo::__serialize(): array;
Foo::__set(string $name, mixed $value): void;
Foo::__set_state(array $properties): object;
Foo::__sleep(): array;
Foo::__unserialize(array $data): void;
Foo::__unset(string $name): void;
Foo::__wakeup(): void;
```

Zdaniem wielu osób powinno się również wprowadzić `: void` dla `__construct()` oraz `__destruct()`.

### 🚧 PHP Namespace in core [[RFC](https://wiki.php.net/rfc/php-namespace-in-core)]
Rozpoczęło się głosowanie ws. wprowadzenia przestrzeni nazw `\Php` do core języka. Póki co, ponad połowa głosów jest przeciw. Nie ma co jednak wróżyć jak to głosowanie się zakończy, gdyż potrwa ono aż do 6 czerwca.

### 🚧 Opcache optimization without any caching [[RFC](https://wiki.php.net/rfc/opcache.no_cache)]
W chwili obecnej opcode optimizer i JIT nie funkcjonują bez włączonej pamięci podręcznej. Propozycją tego RFC jest dodanie nowej opcji konfiguracyjnej `opcache.no_cache`, która umożliwi jej wyłączenie.

## RFC - Nowe
### 🔥 🆕  Named Arguments [[RFC](https://wiki.php.net/rfc/named_params)]
Wznowiony został temat wdrożenia do PHP nazwanych argumentów! Wspaniała wiadomość! Jeśli ktoś nie wie o co chodzi, oto prosty przykład:

```php
function xyz(string $a, string $b, ?string $c = null, ?string $d = null) {
  // ...
}

// Obecnie:
xyz("a", "b", null, "d");

// Z nazwanymi argumentami:
xyz("a", "b", d: "d");
```

W planach jest również wprowadzenie wersji skróconej. Chociażby dla takiego przykładu:

```php
new ParamNode(
    name: $name,
    type: $type,
    default: $default,
    variadic: $variadic,
    byRef: $byRef
);
```

są trzy propozycje:

```php
new ParamNode(:$name, :$type, :$default, :$variadic, :$byRef);
new ParamNode(=$name, =$type, =$default, =$variadic, =$byRef);
new ParamNode(=> $name, => $type, => $default, => $variadic, => $byRef);
```

Podobna sprawa tyczy się tablic:

```php
['x' => $x, 'y' => $y, 'z' => $z] = $point;
```

```php
[:$x, :$y, :$z] = $point;
[=$x, =$y, =$z] = $point;
[=> $x, => $y, => $z] = $point;
```
### 🔥 🆕  "Deprecated" Attribute [[RFC](https://wiki.php.net/rfc/deprecated_attribute)]
Temat powstał na wskutek zatwierdzenia RFC odnoszącego się do wdrożenia atrybutów do PHP 8, i jest kontynuacją tematu rozszerzenia ich funkcjonalności. Atrybut `<<Deprecated>>` ma być pierwszym atrybutem zarezerwowanym przez PHP, który miałby powodować wywołanie "trigger_error" w momencie odwoływania się do funkcji, metod, parametrów, klas lub stałych mających przypisany ten atrybut.

```php
<<Deprecated>>
function test() {}
// Deprecated: Function test() is deprecated
 
<<Deprecated("use test3() instead")>>
function test2() {}
// Deprecated: Function test2() is deprecated, use test3() instead
 
class Foo {
    <<Deprecated>>
    public function test() {}
    // Deprecated: Method Foo::test() is deprecated in %s
 
    public function test2(<<Deprecated>> $value) {}
    // When $foo->test2("bar")
    // Deprecated: Argument #1 ($value) to method Foo::test() is deprecated
 
    <<Deprecated>>
    public $value;
    // When reading or writing $foo->value
    // Deprecated: Property Foo::$value is deprecated
 
    <<Deprecated>>
    const FOO = 1;
    // when reading Foo::FOO
    // Deprecated: Constant Foo::FOO is deprecated
}
```

### 🔥 🆕  Match expression v2 [[RFC](https://wiki.php.net/rfc/match_expression_v2)]
No i doczekaliśmy się drugiej wersji RFC chcącego wdrożyć wyrażenie `match`. Tym razem niniejsze RFC jest nieco uproszczone względem swojego poprzednika. Między innymi ominięto bloki, pattern matching oraz omijanie warunku `(true)`.

RFC oferuje wiele ważnych i bardzo ciekawych zmian, z którymi gorąco polecam się zaznajomić. Nie mniej jednak, moim zdaniem najbardziej przykuwają uwagę następujące:

#### Wyrzucenie błędu `UnhandledMatchError` w przypadku, gdy `match` nie obsługuje jakiejś wartości.

```php
$result = match ($operator) {
    BinaryOperator::ADD => $lhs + $rhs,
};
 
// Wyrzuca wyjątek gdy `$operator === BinaryOperator::SUBTRACT`
```

#### Natychmiastowe zwracanie wartości

W skrócie, zamiast takiego konstruktu:
```php
switch (1) {
    case 0:
        $result = 'Foo';
        break;
    case 1:
        $result = 'Bar';
        break;
    case 2:
        $result = 'Baz';
        break;
}
 
echo $result;
//> Bar
```
będzie można użyć następujący:
```php
echo match (1) {
    0 => 'Foo',
    1 => 'Bar',
    2 => 'Baz',
};
```

### 🆕 Attribute Amendments [[RFC](https://wiki.php.net/rfc/attribute_amendments)]
Po zaakceptowaniu RFC wprowadzającego atrybuty, pozostało jeszcze parę kwestii do zoptymalizowania:
#### Grupowanie atrybutów
Przed:
```php
<<Attr1>><<Attr2>>
class Example
{
```
Po:
```php
<<Attr1, Attr2>>
class Example
{
```
#### Walidacja użyć atrybutów
Chodzi o możliwości określenia warunków na temat, czy atrybut może być użyty na konkretnym konstrukcie:
```php
<<PhpAttribute(PhpAttribute::TARGET_CLASS)>>
class Table
{
}

class User
{
    <<Table("users")>>
    public $id;
}

use ORM\Attributes\Table;
 
<<Table("users")>>
class User
{
}
```
RFC da nam następujące stałe:
```php
class PhpAttribute
{
    public const int TARGET_CLASS = 1;
    public const int TARGET_FUNCTION = (1 << 1);
    public const int TARGET_METHOD = (1 << 2);
    public const int TARGET_PROPERTY = (1 << 3);
    public const int TARGET_CLASS_CONSTANT = (1 << 4);
    public const int TARGET_PARAMETER = (1 << 5);
    public const int TARGET_ALL = ((1 << 6) - 1);
 
    public function __construct(int $target = self::TARGET_ALL)
    {
    }
}
```
#### Powtarzalność atrybutów
W chwili obecnej atrybut może zostać użyty wiele razy na jednym konstrukcie (np. klasie, metodzie, funkcji itp.).

```php
class HomepageController
{
    <<Route("/")>>
    <<Route("/homepage")>>
    public function indexAction()
    {
    }
}
```

RFC proponuje dodatkowy parametr, który będzie ograniczał tą ilość użyć do 1:
```php
<<PhpAttribute(PhpAttribute::TARGET_ALL, PhpAttribute::IS_REPEATABLE)>>
class Route
{
}
 
// z parameterami nazwanymi
<<PhpAttribute(flags: PhpAttribute::IS_REPEATABLE)>>
class Route
{
}
```

### 🆕 Conditional Return, Break, and Continue Statements [[RFC](https://wiki.php.net/rfc/conditional_break_continue_return)]

Zaproponowano możliwość tworzenia nowych warunków w funkcjach i metodach. Stworzono dwie wersje:

```php
return if ($condition): $returnValue;
```
oraz
```php
return $returnValue if ($condition);
```

Przykłady:
```php
function divide($dividend, $divisor = null) {
    return if ($divisor === null || $divisor === 0): 0;
 
    return $dividend / $divisor;
}
```
```php
function divide($dividend, $divisor = null) {
    return 0 if ($divisor === null || $divisor === 0);
 
    return $dividend / $divisor;
}
```

### 🆕 Guard statement [[RFC](https://wiki.php.net/rfc/guard_statement)]
Niniejsze RFC jest propozycją wdrożenia wzorca [early return](https://www.itamarweiss.com/personal/2018/02/28/return-early-pattern.html), a samą implementację oparto na tej, istniejącej już w [Swift](https://docs.swift.org/swift-book/ReferenceManual/Statements.html#grammar_if-statement).
```php
guard condition else {
  statements
}
```

Zamysł jest taki, że ciało tego warunku wykona się tylko jeśli warunek zwróci `false`. Ponadto, ciało musi zawierać `return`, `throw` lub `break`/`continue` w kontekście pętli.

Tak więc, zamiast takiego kodu:

```php
function getValue (?string $value) {
  if (null === $value) {
    return;
  }

  return $value . " something";
}
```
autor RFC proponuje następujące rozwiązanie:
```php
function getValue (?string $value) {
  guard (null !== $value) else {
    return $value . " something";
  }
  
  return null;
}
```
W powyższym przykładzie niewiele się zmienia, ale być może istnieją jakieś konkretne przypadki, w których ta czytelność się poprawia.

### 🆕 Make sorting stable [[RFC](https://wiki.php.net/rfc/stable_sorting)]
W chwili obecnej sortowanie w PHP nie działa zbyt stabilnie, tzn. jego wynik nie gwarantuje tej samej listy posortowanych elementów. Niniejsze RFC ma to poprawić.

```php
$array = [
    'c' => 1,
    'd' => 1,
    'a' => 0,
    'b' => 0,
];
asort($array);

// obecnie można uzyskać następujące wyniki
['b' => 0, 'a' => 0, 'c' => 1, 'd' => 1]
['a' => 0, 'b' => 0, 'd' => 1, 'c' => 1]
['b' => 0, 'a' => 0, 'd' => 1, 'c' => 1]
 
// przy stabilnym sortowaniu
['a' => 0, 'b' => 0, 'c' => 1, 'd' => 1]
```

### 🆕 Strict operators directive
W chwili obecnej operatory porównywania wartości dokonują konwersji typów, jeśli porównywane są wartości o różnych typach. Te konwersje są dość złożone, i zależą od operatorów oraz typów wartości. Może to spowodować wiele nieoczekiwanych wyników.
```php
$a = ['x' => 1, 'y' => 22];
$b = ['y' => 10, 'x' => 15];
 
$a > $b; // true
$a < $b; // true

[1] < ['bar' => 50]; // false
[1] > ['bar' => 50]; // false
['a' => 'foo', 'b' => 'bar'] == ['b' => 'bar', 'a' => 0]; // true
```
Autor tego RFC chciałby tą kwestię nieco ustabilizować. Proponowane jest więc wdrożenie nowej dyrektywy `strict_operators`, która ograniczałaby konwersję pomiędzy typami wyrzucając `TypeError` dla niewspieranych możliwości.

```php
declare(strict_operators=1);
 
1.2 + 2; // float(3.2)

10 > 42;        // false
3.14 < 42;      // true
 
"foo" > "bar";  // TypeError("Unsupported type string for comparison operation")
"foo" > 10;     // TypeError("Operator type mismatch string and int for comparison operation")
 
"foo" == "bar"; // false
"foo" == 10;    // TypeError("Operator type mismatch string and int for comparison operation")
"foo" == null;  // TypeError("Operator type mismatch string and null for comparison operation")
 
true > false;   // true
true != 0;      // TypeError("Operator type mismatch bool and int for comparison operation")
 
[10] > [];      // TypeError("Unsupported type array for comparison operation")
[10] == [];     // false

"120" > "99.9";               // TypeError("Unsupported type string for comparison operation")
(float)"120" > (float)"99.9"; // true
 
"100" == "1e1";               // false
(int)"100" == (int)"1e2";     // true
 
"120" <=> "99.9";             // TypeError("Unsupported type string for comparison operation")

$a = 0;
$b = "foo";
$a == $b;     // TypeError("Unsupported type string for comparison operation")
[$a] == [$b]; // TypeError("Unsupported type array for comparison operation")
```

### 🆕 Error backtraces [[RFC](https://wiki.php.net/rfc/error_backtraces)]
W skrócie: chodzi o umożliwienie backtrackingu również i dla błędów "Fatal" (podobnie jak to jest w wyjątkach).
```
Fatal error: Allowed memory size of 134217728 bytes exhausted in /var/www/test.php on line 123
#0 /var/www/test.php(123): spl_eat_all_memory()
#1 /var/www/foo.php(456): foo(123, 'foo')
#2 /var/www/bar.php(789): bar()
#3 {main}
```

Błędy również będą dostępne dla `error_get_last()`:
```
#1 {main}
array(5) {
  ["type"]=>
  int(1)
  ["message"]=>
  string(22) "Error, terrible error!"
  ["file"]=>
  string(17) "/var/www/test.php"
  ["line"]=>
  int(3)
  ["backtrace"]=>
  string(123) "#0 /var/www/test.php(345): $this->causeTerribleError()
#1 {main}"
}
```


### 🆕 Add CMS Support [[RFC](https://wiki.php.net/rfc/add-cms-support)]
RFC, którego propozycją jest dodanie do PHP wsparcia dla _Cryptographic Message Syntax_, czyli nowszej wersji wspieranego już _PKCS#7_. Oba określenia są zbiorami standardów używanych w kryptografii.

### 🆕 Remove inappropriate inheritance signature checks on private methods [[RFC](https://wiki.php.net/rfc/inheritance_private_methods)]
Nie powiem, jestem zaskoczony, że to RFC powstało. Okazuje się, że w bebechach PHP istnieje kod sprawdzający, czy metoda prywatna jest nadpisywana przez klasę podrzędną.
```php
class A 
{ 
    final private function finalPrivate() { 
        echo __METHOD__ . PHP_EOL; 
    } 
} 
 
class B extends A 
{ 
    private function finalPrivate() { 
        echo __METHOD__ . PHP_EOL; 
    } 
}
```
Powyższy kod zwróci błąd:
> Fatal error: Cannot override final method A::finalPrivate()

Autor tego RFC chce usunąć to sprawdzanie, aby powyższy kod wykonywał się bez błędu.

## RFC - Odrzucone

### 🔥 ❌ Match expression [[RFC](https://wiki.php.net/rfc/match_expression)]
Niestety zaproponowana nowa instrukcja `match` nie przeszła przez głosowanie. Na wskutek dyskusji powstały trzy główne przeciwwskazania:

1. W RFC powstały w między czasie wiele modyfikacji, a autor ruszył z głosowaniem wdrożenia tej funkcjonalności zbyt szybko.
2. Zasugerowano, aby `(true)` było omijane dla `match (true)`, ale wiąże się to z dalszymi problemami czytelności.
3. `match` powinien być zwykłym wyrażeniem nie zawierającym możliwości bloków.
```php
match ($x) {
    0 => foo(), // <-- niby dobre
}

match ($x) {
    0 => { // <-- niby złe
        foo();
        bar();
        baz();
    },
}
```

## Inne
### Lista "deprecated"
Na [TEJ](https://stitcher.io/blog/new-in-php-8#breaking-changes) stronie pojawiła się lista głównych zmian, jakie wdroży PHP 8. W tym wszystkim zawarte jest również do będzie oznaczone jako "deprecated". W skrócie, będą to:
- zmiany w systemie warningów: Reclassifying engine warnings [[RFC](https://wiki.php.net/rfc/engine_warnings)]
- operator `@` już nie będzie uciszać błędów "Fatal"
- domyślny poziom raportowania błędów będzie `E_ALL`.
- zastosowane zostanie parę porządków w stosunku do poprzednich wersji PHP:
  - 7.2: [https://wiki.php.net/rfc/deprecations_php_7_2](https://wiki.php.net/rfc/deprecations_php_7_2)
  - 7.3: [https://wiki.php.net/rfc/deprecations_php_7_3](https://wiki.php.net/rfc/deprecations_php_7_3)
  - 7.4: [https://wiki.php.net/rfc/deprecations_php_7_4](https://wiki.php.net/rfc/deprecations_php_7_4)

### Intersection types
Na koniec mała wisienka na torcie! Jeden z członków community PHP ma w planach utworzyć RFC odnoszące się do możliwości tworzenia typów "intersection". W Typescript wygląda to następująco:
```ts
type A = { a: number };
type B = { b: boolean };
type C = { c: string };

function method(value: A & B & C) {
  // ...
}
```
