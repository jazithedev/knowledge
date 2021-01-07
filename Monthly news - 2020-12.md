
# Monthly news - Grudzień 2020

## Najważniejsze
- Nowe: [Enumerations](#-enumerations-rfc)
- Nowe: [Fibers](#-fibers-rfc)

## RFC - W trakcie głosowania
### 🚧 Restrict $GLOBALS usage [[RFC](https://wiki.php.net/rfc/restrict_globals_usage)]
Wewnątrz PHP istnieje pewna zmienna `$GLOBALS`, której zadaniem jest przechowywanie referencji do WSZYSTKICH zmiennych dostępnych w obrębie danego zakresu. Jej istniejąca logika narzuca dodatkową złożoność techniczną, oraz wpływa na wydajność. W planach jest jej usunięcie, a zaproponowane RFC ma postawić pierwszy krok w tym kierunku. W związku z tym zaproponowano, aby zmienić wewnętrzną strukturę tej zmiennej i udostępnić tylko dwie możliwości odwołania się do niej:
1. Pobieranie wartości w formie `$GLOBALS[$var]`
2. Pobranie całej zawartości ale tylko w trybie "read-only".

W związku z powyższym, działać będzie m.in. poniższy kod:

```php
$GLOBALS['x'] = 1;
$GLOBALS['x']++;
isset($GLOBALS['x']);
unset($GLOBALS['x']);
// ... wszystko inne korzystające z $GLOBALS['x'].

foreach ($GLOBALS as $var => $value) {
    echo "$var => $value\n";
}
```

Natomiast poniższe skrawki kodu spowodują błąd:

```php
$GLOBALS = [];
$GLOBALS += [];
$GLOBALS =& $x;
$x =& $GLOBALS;
unset($GLOBALS);
by_ref($GLOBALS);
```

## RFC - Nowe

### 🔥🆕 Enumerations [[RFC](https://wiki.php.net/rfc/enumerations)]
Prędzej czy później musiało do tego dojść :). W końcu zaproponowano rozwiązanie przedstawiające enumy w PHP!

```php
enum Suit {
  case Hearts;
  case Diamonds;
  case Clubs;
  case Spades;
}

$val = Suit::Diamonds;

function pick_a_card(Suit $suit) { ... }
 
pick_a_card($val);        // OK
pick_a_card(Suit::Clubs); // OK
pick_a_card('Spades');    // throws TypeError

$a = Suit::Spades;
$b = Suit::Spades;
 
$a === $b; // true
 
 
$a instanceof Suit;         // true
$a instanceof Suit::Spades; // true
```

### 🔥🆕 Fibers [[RFC](https://wiki.php.net/rfc/fibers)]
Kiedy pojawił się temat na Reddit PHP odnoszący się do "czego wg. programistów najbardziej brakuje w PHP", jedną z częstszych odpowiedzi było "asynchroniczność". Było to więc kwestią czasu, zanim pojawi się jakieś RFC odnoszące się do tego tematu. W tym miesiącu zaproponowano dodanie [Fibers](https://en.wikipedia.org/wiki/Fiber_(computer_science)) do PHP 8.1. Koncept ten jest bardzo podobny do tzw. "coroutines", a ich implementacja jest już dostępna w postaci osobnej biblioteki [amphp](https://amphp.org).

### 🆕 Add is_array_and_list [[RFC](https://wiki.php.net/rfc/is_list)]
RFC, które zamierza dodać nową funkcję: `is_array_and_list()` będącą ekwiwalentem do następującej implementacji:
```php
function is_array_and_list(mixed $value): bool {
    if (!is_array($value)) { return false; }
 
    $expectedKey = 0;
    foreach ($value as $i => $_) {
        if ($i !== $expectedKey) { return false; }
        $expectedKey++;
    }
    return true;
}
```

### 🆕 #\[NamedParameterAlias\] Attribute [[RFC](https://wiki.php.net/rfc/named_parameter_alias_attribute)]
Atrybut dający możliwość zmiany nazw nazwanych parametrów.

```php
use NamedParameterAlias;
 
// Old function signature:
function log($arg1) {}
 
// New function signature introduces better name
function log(#[NamedParameterAlias("arg1")] $message) {}

// ...

log(arg1: "Hello World!");
log(message: "Hello World!");
```
Warto zaznaczyć, że taka modyfikacja może zostać dokonana tylko raz.


### 🆕 #\[Deprecated\] Attribute [[RFC](https://wiki.php.net/rfc/deprecated_attribute)]
Powrócił temat atrybutu `#[Deprecated]`, którego zadaniem będzie informowanie poprzez warningi, że nadal korzysta się z części kodu oznaczonego jako przestarzały.

### 🆕 Short match [[RFC](https://wiki.php.net/rfc/short-match)]
Do PHP 8 wprowadzono nową instrukcję warunkową `match`. Mając poniższy kod:

```php
$a = 3;
 
print match (true) {
  $a < 2 => 'small',
  $a == 3 => 'medium',
  default => 'large',
};
```
niniejsze RFC ma spowodować, że będzie on wyglądał następująco:
```php
$a = 3;
 
print match {
  $a < 2 => 'small',
  $a == 3 => 'medium',
  default => 'large',
};
```


### 🆕 Phasing out Serializable [[RFC](https://wiki.php.net/rfc/phase_out_serializable)]
W PHP 7.4 [wprowadzono](https://wiki.php.net/rfc/custom_object_serialization) nowe metody `__serialize()` oraz  `__unserialize()` mające zastąpić interfejs `Serializable`. Niniejsze RFC podejmuje kolejne kroki aby móc usunąć wspomniany interfejs w przyszłości (w PHP 9 lub 10).

### 🆕 Deprecate passing null to non-nullable arguments of internal functions [[RFC](https://wiki.php.net/rfc/deprecate_null_to_scalar_internal_arg)]
Wewnętrzne funkcje zdefiniowane w PHP bądź w rozszerzeniach PHP przyjmują wartość `null` dla argumentów "non-nullable". Zaproponowanę przez Nikitę RFC ma to zmienić.
```php
var_dump(str_contains("foobar", null)); // bool(true)
```

### 🆕 Measuring maximum execution time based on wall-time [[RFC](https://wiki.php.net/rfc/max_execution_wall_time)]
Twórca PR zaproponował nową funkcję - `max_execution_wall_time()`. Różnicą względem `max_execution_time()` jest, że przy tym drugim czas mierzony jest na podstawie CPU. Nie są brane pod wzgląd takie operacje jak żądania do sieci lub systemu, a nawet funkcja `sleep()`.

### 🆕 Concepts to improve mysqli extension [[RFC](https://wiki.php.net/rfc/improve_mysqli)]
RFC wskazujące pewne braki oraz proponujące parę zmian do mysqli API:
1. Exception error reporting mode should be the default one
2. Add bind-in-execute to mysqli
3. `$mysqli->connect_error` is a static property that can only be accessed as an instance property
4. deprecate `mysqli::init()`
5. `new mysqli()` doesn't open a connection with 0 arguments
6. `'new mysqli`' and `'mysqli_connect()'` are not true aliases
7. `mysqli_init()` and `mysqli_real_connect()` are weird aliases that do not match OO style
8. libmysqlclient support - untested and unmaitained

### 🆕 Property initializer for clone [[PR](https://github.com/php/php-src/pull/6538)]
RFC proponuje nieco inną składnię do klonowania obiektów. Poniższy kod:

```php
class Foo
{
	public $bar;
	public $baz;

	public function withProperties($bar, $baz)
	{
	    $self = clone $this;
	    $self->bar = $bar;
	    $self->baz = $baz;

	    return $self;
	}
}
```
mógłby zostać zmieniony na następujący:
```php
class Foo
{
	public $bar;
	public $baz;

	public function withProperties($bar, $baz)
	{
	    return clone $this with {
	        bar: $bar,
	        baz: $baz,
	    };
	}
}
```

## Inne
- PHP 7.3 przechodzi w tryb wsparcia - oczekujmy więc dla tej wersji tylko łatek naprawiających luki bezpieczeństwa
- Dokumentacja PHP została przeniesiona na [GitHub](https://github.com/php/doc-base)!
