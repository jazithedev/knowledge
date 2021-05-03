
# Monthly news - Luty/Kwiecień 2021

## Najważniejsze
- Zaakceptowane: [Fibers](#--fibers-rfc)
- Zaakceptowane: [Enumerations](#--enumerations-rfc)
- Zaakceptowane: [Pure Intersection types](#--pure-intersection-types--rfc)
- Zaakceptowane: [Partial Function Application](#--partial-function-application-rfc)
- Zaakceptowane: [Auto-capturing multi-statement closures](#--auto-capturing-multi-statement-closures-rfc)

## RFC - Zaakceptowane

### ✅ 🔥 Fibers [[RFC](https://wiki.php.net/rfc/fibers)]
Fibers zostaną dodane do PHP 8.1. Architektura ta ma wspomóc wykonywanie zadań równoległych w PHP.

### ✅ 🔥 Enumerations [[RFC](https://wiki.php.net/rfc/enumerations)]
Enumy pojawią się w PHP 8.1 :).

```php
enum Suit {
  case Hearts;
  case Diamonds;
  case Clubs;
  case Spades;
}
```

### ✅ Array unpacking with string keys [[RFC](https://wiki.php.net/rfc/array_unpacking_string_keys)]
RFC ma rozszerzyć możliwości operatora "spread":
```php
$array1 = ["a" => 1];
$array2 = ["a" => 2];
$array = ["a" => 0, ...$array1, ...$array2];
var_dump($array); // ["a" => 2]
```
### ✅ Deprecate passing null to non-nullable arguments of internal functions [[RFC](https://wiki.php.net/rfc/deprecate_null_to_scalar_internal_arg)]
Wewnętrzne funkcje zdefiniowane w PHP bądź w rozszerzeniach PHP przyjmują wartość `null` dla argumentów "non-nullable". Zaproponowanę przez Nikitę RFC ma to zmienić w PHP 8.1.
```php
var_dump(str_contains("foobar", null)); // bool(true)
```
### ✅ Static variables in inherited methods [[RFC](https://wiki.php.net/rfc/static_variable_inheritance)]
Kiedy metoda zawierająca statyczną zmienną jest dziedziczona, ta odziedziczona metoda korzysta obecnie z niezależnego zestawu zmiennych statycznych. RFC proponuje aby ustawiać tylko jeden zestaw zmiennych statycznych na jedną metodę.

```php
class A {
    public static function counter() {
        static $i = 0;
        return ++$i;
    }
}
class B extends A {}
 
var_dump(A::counter()); // int(1)
var_dump(A::counter()); // int(2)
var_dump(B::counter()); // int(1)
var_dump(B::counter()); // int(2)
```

W przypadku odziedziczenia `A::counter()` do klasy B, wykorzystywany jest osobny zestaw zmiennych statycznych. Wynikiem czego jest `A::counter()` oraz `B::counter()` działające niezależnie.

RFC ma poprawić to w taki sposób, aby powyższy kod działał w sposób następujący:

```php
var_dump(A::counter()); // int(1)
var_dump(A::counter()); // int(2)
var_dump(B::counter()); // int(3)
var_dump(B::counter()); // int(4)
```

### ✅ Change Default mysqli Error Mode [[RFC](https://wiki.php.net/rfc/mysqli_default_errmode)]
W chwili obecnej domyślnie wszystkie błędy od mysqli są "silent". Oznacza to, że w przypadku błędów SQL, nie zostanie wyrzucony żaden wyjątek, błąd lub warning. Niniejsze RFC ma zamiar zmienić domyślną wartość na następującą:

```php
mysqli_report(MYSQLI_REPORT_ERROR | MYSQLI_REPORT_STRICT);
```

### ✅ Object scoped RNG Implementations [[RFC](https://wiki.php.net/rfc/object_scope_prng)]
Celem RFC jest wdrożenie losowego generowania liczb w obrębie obiektów. Implementacja ta ma zostać oparta o nową architekturę "Fibers".

### ✅ Namespaces in bundled PHP extensions [[RFC](https://wiki.php.net/rfc/namespaces_in_bundled_extensions)]
RFC proponujące zestaw zasad implementowania rozszerzeń do PHP w obrębie swoich przestrzeni nazw. Wstępnie zaproponowano następujące modyfikacje:

- IMAPConnection -> IMAP\Connection
- FTPConnection -> FTP\Connection
- LDAP -> LDAP\Connection
- LDAPResult -> LDAP\Result
- LDAPResultEntry -> LDAP\ResultEntry
- PgSql -> PgSql\Connection
- PgSqlResult -> PgSql\Result
- PgSqlLob -> PgSql\Lob

### ✅ mysqli bind in execute [[RFC](https://wiki.php.net/rfc/mysqli_bind_in_execute)]
Ciąg dalszy modyfikacji biblioteki `mysqli`. Tym razem chodzi o umożliwienie bindowania wartości parametrów z zapytaniem z poziomu metody `execute`:

```php
// it is now possible to bind by value
$stmt = $mysqli->prepare('INSERT INTO users(id, name) VALUES(?,?)');
$stmt->execute([1, trim(' Dharman ')]);
 
// binding an array becomes less of a chore
$arr = [2,3,5,8,13];
$stmt = $mysqli->prepare('SELECT name FROM users WHERE id IN ('.str_repeat('?,', count($arr) - 1) . '?)');
$stmt->execute($arr);
```

### ✅ noreturn type [[RFC](https://wiki.php.net/rfc/noreturn_type)]
RFC chce wprowadzić nowy typ dla funkcji, które zawsze będą wywoływać funkcję `exit()`.
```php
function redirect(string $uri): never {
    header('Location: ' . $uri);
    exit();
}
 
function redirectToLoginPage(): never {
    redirect('/login');
}
```

### ✅ Phasing out Serializable [[RFC](https://wiki.php.net/rfc/phase_out_serializable)]
RFC mówiące o usunięciu interfejsu `Serializable`.

### ✅ Deprecate implicit non-integer-compatible float to int conversions [[RFC](https://wiki.php.net/rfc/implicit-float-int-deprecate)]
W obecnym stanie PHP w przypadku, kiedy `float` będzie miało wartość wykraczającą poza zakres `int`, nastąpi częściowa utrata danych. Podobnie może się stać dla danej float zapisanej jako string. Niniejsze RFC chce ustawić flagę "deprecated" na rzutowanie `float`/`float string` => `int`.

### ✅ Add fetch_column method to mysqli [[RFC](https://wiki.php.net/rfc/mysqli_fetch_column)]
Propozycja dodania metody `fetch_column` do mysqli. W taki sposób, zamiast takiego kodu:
```php
$result = $mysqli->query('SELECT username FROM users WHERE id = 123');
echo $result->fetch_row()[0] ?? false;
```
będzie można wytworzyć następujący:
```php
$result = $mysqli->query('SELECT username FROM users WHERE id = 123');
echo $result->fetch_column();
```

## RFC - W trakcie głosowania

### 🚧 Adding return types to internal methods [[RFC](https://externals.io/message/113413)]
Pomysł z rozpoczęciem uzupełniania typów zwracanych przez metody wewnętrznych PHP oraz ustalenia konkretnego planu na przeprowadzenie tej operacji.

### 🚧 var_representation() : readable alternative to var_export() [[RFC](https://wiki.php.net/rfc/readable_var_representation)]
Autor proponuje dodanie nowej funkcji `var_representation()`, będącej alternatywą do  `var_export()` w temacie bardziej czytelnego przedstawienia danych w output. Parę przykładów podano w RFC.

### 🚧 any() and all() on iterables [[RFC](https://wiki.php.net/rfc/any_all_on_iterable)]
RFC proponuje dodanie nowych funkcji pod nową przestrzeń nazw `PHP\iterable\*` do PHP 8.1:

```php
/**
 * Determines whether any element of the iterable satisfies the predicate.
 *
 *
 * If the value returned by the callback is truthy
 * (e.g. true, non-zero number, non-empty array, truthy object, etc.),
 * this is treated as satisfying the predicate.
 *
 * @param iterable $input
 * @param null|callable(mixed):mixed $callback
 */
function any(iterable $input, ?callable $callback = null): bool {
    foreach ($input as $v) {
        if ($callback !== null ? $callback($v) : $v) {
            return true;
        }
    }
    return false;
}

/**
 * Determines whether all elements of the iterable satisfy the predicate.
 *
 * If the value returned by the callback is truthy
 * (e.g. true, non-zero number, non-empty array, truthy object, etc.),
 * this is treated as satisfying the predicate.
 *
 * @param iterable $input
 * @param null|callable(mixed):mixed $callback
 */
function all(iterable $input, ?callable $callback = null): bool {
    foreach ($input as $v) {
        if (!($callback !== null ? $callback($v) : $v)) {
            return false;
        }
    }
    return true;
}
```


## RFC - Nowe

### 🆕 🔥 Pure Intersection types  [[RFC](https://github.com/php/php-src/pull/6799)]
Autor PR chciałby wprowadzić typy "intersection", czyli mając `A&B` oznacza, że obiekt musi typu `A` oraz typu `B` w tym samym czasie.

### 🆕 🔥 Partial Function Application [[RFC](https://wiki.php.net/rfc/partial_function_application)]
Propozycja możliwości tworzenia tzw. "partiali", czyli funkcji z częściowo uzupełnionymi argumentami, które generują funkcje z parametrami, które wcześniej nie zostały uzupełnione:
```php
function whole($one, $two) { /* ... */ }
 
$partial = whole(?, 2);
$result = $partial(1);
```

### 🆕 🔥 Auto-capturing multi-statement closures [[RFC](https://wiki.php.net/rfc/auto-capture-closure)]
W chwili obecnej PHP daje możliwość zdefiniowania funkcji na dwa możliwe sposoby:
```php
$y = 1;
$fn1 = fn($x) => $x + $y;
```
```php
$y = 1;
$fn2 = function ($x) use ($y): int { // manual-capture + statement list
   // ...
 
   return $x + $y;
};
```
Niniejsze RFC chce połączyć obie z nich, i dać możliwość zdefiniowania funkcji następująco:
```php
$y = 1;
$fn3 = fn ($x): int { // auto-capture + statement list
    // ...
 
    return $x + $y;
};
```

### 🆕 Sealed Classes [[RFC](https://wiki.php.net/rfc/sealed_classes)]
RFC proponuje utworzenie nowych słów kluczych `sealed` oraz `permits` dla klas dla PHP 8.1. Póki co RFC jest jeszcze w wczesnej wersji, i dużo może się zmienić, nie mniej jednak funkcjonalność polega mniej więcej na tym:
```php
sealed class Shape permits Circle, Square, Rectangle {}
 
final class Circle extends Shape {} // ok
final class Square extends Shape {} // ok
final class Rectangle extends Shape {} // ok
 
final class Triangle extends Shape {} // Fatal error: Class Triangle cannot extend sealed class Shape.
```

### 🆕 New in initializers [[RFC](https://wiki.php.net/rfc/new_in_initializers)]
RFC chce zaproponować, aby umożliwić tworzenie instancji klasy dla domyślnych wartości parametrów funkcji lub metod:
```php
function test(
    $foo = new A,
    $bar = new B(1),
    $baz = new C(x: 2),
) {
}
```

### 🆕 Deprecations for PHP 8.1 [[RFC](https://wiki.php.net/rfc/deprecations_php_8_1)]
Lista zawierająca części PHP, które w wersji 8.1 powinny zostać określone jako "deprecated":

-   `date_sunrise()` and `date_sunset()`
-   `key()`, `current()`, `next()`, `prev()`, and `reset()` on objects
-   `mb_check_encoding()` without argument
-   `get_class()`, `get_parent_class()` and `get_called_class()` without argument
-   `FILE_BINARY` and `FILE_TEXT` constants
-   `t` fopen mode
-   Passing bool for `$amountOrUpOrDown` argument of `IntlCalendar::roll()`
-   Accessing static members on traits
-   `strptime()`
-   `strftime()` and `gmtstrftime()`
-   `mhash*()` function family
-   `ctype_*()` function family accepts `int` parameters
-   Return by reference with void type
-   NIL constant defined by the IMAP extension
-   Calling overloaded pgsql functions without the connection argument
-   `$num_points` parameter of `image(open|filled)polygon`
-   `mysqli::init()`


### 🆕 CachedIterable [[RFC](https://wiki.php.net/rfc/cachediterable)]
Propozycja dodania nowej klasy `CachedIterable` mającej na celu przechowywanie niemodyfikowalnej (immutable) kopii kluczy i wartości struktury `iterable`, z której dana instancja została utworzona.

```php
final class CachedIterable implements IteratorAggregate, Countable, JsonSerializable
{
    public function __construct(iterable $iterator) {}
    public function getIterator(): InternalIterator {}
    public function [count](http://www.php.net/count)(): int {}
    // [[$key1, $value1], [$key2, $value2]]
    public static function fromPairs([array](http://www.php.net/array) $pairs): CachedIterable {}
    // [[$key1, $value1], [$key2, $value2]]
    public function toPairs(): [array](http://www.php.net/array){} 
    public function __serialize(): [array](http://www.php.net/array) {}  // [$k1, $v1, $k2, $v2,...]
    public function __unserialize([array](http://www.php.net/array) $data): void {}
 
    // useful for converting iterables back to arrays for further processing
    public function keys(): [array](http://www.php.net/array) {}  // [$k1, $k2, ...]
    public function values(): [array](http://www.php.net/array) {}  // [$v1, $v2, ...]
    // useful to efficiently get offsets at the middle/end of a long iterable
    public function keyAt(int $offset): mixed {}
    public function valueAt(int $offset): mixed {}
 
    // '[["key1","value1"],["key2","value2"]]' instead of '{...}'
    public function jsonSerialize(): [array](http://www.php.net/array) {}
    // dynamic properties are forbidden
}
```

### 🆕 Warning for implicit float to int conversions [[RFC](https://github.com/Girgias/float-int-warning)]
RFC proponuje wyrzucanie `E_WARNING` w momencie rzutowania wartości `float` lub `float string` na `int`. Planowane jest, aby w PHP 9 warning został zmieniony na `TypeError`.

### 🆕 println [[RFC](https://wiki.php.net/rfc/println)]
RFC chce dodać nową globalną funkcję `println()`, która będzie miała identyczne zachowanie co `print()`, lecz na koniec linii będzie dodawać `\n`.

### 🆕 Short Functions [[RFC](https://wiki.php.net/rfc/short-functions)]
RFC, którego celem jest umożliwienie tworzenie nazwanych funkcji/metod w skróconej formie:
```php
function add(int $a, int $b): int => $a + $b;

class Adder
{
    public function add(int $in): int => $in + $this->val;
}
```

### 🆕 debug_backtrace_depth(int $limit=0): int [[RFC](https://wiki.php.net/rfc/debug_backtrace_depth)]
Propozycja nowej funkcji zwracającej informację o głębokości stack strace.


### 🆕 Add IntlDatePatternGenerator [[RFC](https://wiki.php.net/rfc/intldatetimepatterngenerator)]
Propozycja utworzenia klasy wspomagającej w formatowaniu dat w zależności od podanego "locale":
```php
class IntlDatePatternGenerator
{
    public function __construct(?string $locale = null) {}
    public static function create(?string $locale = null): ?IntlDatePatternGenerator {}
    public function getBestPattern(string $skeleton): string|false {}
}

// Procedural style:
function datepatterngenerator_create(?string $locale = null): ?IntlDatePatternGenerator {}
 
function datepatterngenerator_get_best_pattern(IntlDatePatternGenerator $patternGenerator, string $skeleton): string|false {}
```
Przykład:
```php
$skeleton = "YYYYMMdd";
 
$today = \DateTimeImmutable::createFromFormat('Y-m-d', '2021-04-24');
 
$dtpg = new \IntlDatePatternGenerator("de_DE");
$pattern = $dtpg->getBestPattern($skeleton);
echo "de: ", \IntlDateFormatter::formatObject($today, $pattern, "de_DE"), "\n";
 
$dtpg = new \IntlDatePatternGenerator("en_US");
$pattern = $dtpg->getBestPattern($skeleton), "\n";
echo "en: ", \IntlDateFormatter::formatObject($today, $pattern, "en_US"), "\n";
 
/*
de: 24.04.2021
en: 04/24/2021
*/
```

## RFC - Odrzucone

### ❌ Direct execution opcode file without php source code file [[RFC](https://wiki.php.net/rfc/direct-execution-opcode)]
W chwili obecnej OPCache nie potrafi wywołać pliku opcode bez pliku źródłowego PHP. Zdaniem RFC powinno się to zmienić. Nie było jednak osób będących takiego samego zdania na ten temat co autor.

## Inne
- Wprowadzono [Inheritance Cache](https://github.com/php/php-src/pull/6627) (przyspieszenie PHP)
- Internalsi powoli podchodzą do usunięcia funkcji `utf8_encode` and `utf8_decode` ([LINK](https://externals.io/message/113645))
- Na repozytoria PHP zostały wgrane commity z niepożądanym kodem. Na szczęście jednak sprawa szybko wyszła na jaw. Rezultatem tego incydentu jest przeniesienie repozytoriów z git.php.net na Github.
- Opublikowano następujące wersje PHP: 8.0.5, 7.4.18, 7.3.28
