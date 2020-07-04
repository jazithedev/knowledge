
# Monthly news - Czerwiec

## Najważniejsze
- Zaakceptowane: [Match expression v2](#---match-expression-v2-rfc)
- Zaakceptowane: [Shorter Attribute Syntax](#--shorter-attribute-syntax)
- W trakcie głosowania: [Rename T_PAAMAYIM_NEKUDOTAYIM](#)
- Nowe: [Nullsafe operator](#--nullsafe-operator)

## RFC - Zaakceptowane

### 🔥 ✅  Match expression v2 [[RFC](https://wiki.php.net/rfc/match_expression_v2)]
Wyrażenie `match` zawita do PHP :). Stało się to dopiero po drugim głosowaniu nad tą funkcjonalnością.

```php
switch ($pressedKey) {
    case MyClass::SAVE:
        save();
        break;
    case MyClass::DELETE:
        delete();
        break;
}
```

mógł zostać przestawiony następująco:

```php
match ($pressedKey) {
    MyClass::SAVE => save(),
    MyClass::DELETE => delete(),
};
```

### 🔥 ✅ Shorter Attribute Syntax [[RFC](https://wiki.php.net/rfc/shorter_attribute_syntax)]
Nie kończą się tematy odnoszące się do zatwierdzonego już wdrożenia atrybutów do PHP 8. W głównej mierze odnoszą się one do czytelności składni, która wydaje się problematyczna do pisania, oraz przy jej odczytywaniu. Autor RFC zaproponował zrezygnować z wzoru `<<Atrybut>>` na rzecz `@@Atrybut`, co ma ułatwić wiele spraw. Niektórzy zaczęli nawet nazywać tą pierwszą wersję operatorem AT-AT.
<center>
<img src="https://vignette.wikia.nocookie.net/starwars/images/f/ff/AT-AT.jpg/revision/latest/scale-to-width-down/340?cb=20160820193312&path-prefix=pl" alt="AT-AT" />
</center>

### ✅ Attribute Amendments [[RFC](https://wiki.php.net/rfc/attribute_amendments)]
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

### ✅ Ensure correct signatures of magic methods [[RFC](https://wiki.php.net/rfc/magic-methods-signature)]
Zaakceptowano RFC wdrażające typy dla większości magicznych metod w PHP.
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

### ✅ Make sorting stable [[RFC](https://wiki.php.net/rfc/stable_sorting)]
Do PHP wdrożono ustabilizowanie sortowania.

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

### ✅ Remove inappropriate inheritance signature checks on private methods [[RFC](https://wiki.php.net/rfc/inheritance_private_methods)]
W PHP istnieje kod sprawdzający, czy metoda prywatna jest nadpisywana przez klasę podrzędną. 
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

Niniejsze RFC powoduje, że powyższy kod wykona się bez wspomnianego błędu.


## RFC - W trakcie głosowania

### 🔥 🚧 Rename T_PAAMAYIM_NEKUDOTAYIM [[RFC](https://wiki.php.net/rfc/rename-double-colon-token)]
Zdaniem autora RFC, powyższy, historyczny już błąd powinien zmienić nazwę. Jest więc plan, aby zmieniono ją na bardziej poprawne `T_DOUBLE_COLON`, a starą ustawić jako alias. Wtedy od PHP 8.0  `T_PAAMAYIM_NEKUDOTAYIM`  będzie oznaczone jako "deprecated".

## RFC - Nowe

### 🔥 🆕 Nullsafe operator [[RFC](https://wiki.php.net/rfc/nullsafe_operator)]
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

### 🆕 Treat namespaced names as single [[RFC](https://wiki.php.net/rfc/namespaced_names_as_token)]
Obecnie PHP traktuje przestrzenie nazw jako sekwencję identyfikatorów. RFC proponuje zmianę tego w taki sposób, aby cała przestrzeń nazw była traktowana jako jeden token. Głównym celem jest zabezpieczenie przypadków, w których używane są w przestrzeniach nazw słowa zarezerwowane w PHP. Na przykład, od PHP 7.4 istnieje zarezerwowane słowo klucze `fn`. Zewnętrzna biblioteka posiadająca przestrzeń nazw zawierającą to słowo klucze będzie wyrzucać błąd "Fatal".

Przykład:
```php
namespace myLib\fn

function someFunction() { ... }
```

W skrócie: RFC zabezpieczy wsteczną kompatybilność na nowsze wersje PHP z nowymi funkcjonalnościami.


Więcej szczegółów znajdziecie w [zawartości tego RFC](https://wiki.php.net/rfc/shorter_attribute_syntax) 🙂.

### 🆕 PHP Namespace Policy [[RFC](https://wiki.php.net/rfc/php_namespace_policy)]
Wznowiono temat stworzenia przestrzeni nazw `\PHP`. Niniejsze RFC ma na celu:
- Zarezerwowanie przestrzeni nazw `\PHP`.
- Ustalenie zasad, w których powyższa przestrzeń będzie używana.
- Określenie wytycznych w sprawie migrowania istniejących już klas do przestrzeni w przyszłości.

### 🆕 Make constructors and destructors return void [[RFC](https://wiki.php.net/rfc/make_ctor_ret_void)]
RFC proponuje określić typ zwracany dla konstruktora i destruktora klas. Ma to na celu konsekwentne określenie sygnatur dla tych metod magicznych, jako że ich reszta [została już określona](https://wiki.php.net/rfc/magic-methods-signature).

### 🆕 Property write/set visibility [[RFC](https://wiki.php.net/rfc/property_write_visibility)]
Zdaniem autora tego RFC, aby obecnie wymusić na własnościach klas read-only, write-only albo semantykę podobną do immutability, należy zastosować dość spory boilerplate.

```php
/**
 * @property-read int $id
 * @property-read string $name
 */
class User {
    private int $id;
    protected string $name;
 
    public function __construct(int $id, string $name) {
        $this->id = $id;
        $this->name = $name;
    }
 
    public function __get($property)
    {
        if (property_exists($this, $property)) {
            // We return value here as non public properties are "readonly" in this class
            return $this->$property;
        }
        throw new PropertyNotFoundException($property, static::class);
    }
 
    public function __set($property, $value)
    {
        if (property_exists($this, $property)) {
            // Here private/protected property is attempted accessed outside allowed scope, so we throw
            throw new PropertyReadOnlyException($property, static::class);
        }
        throw new PropertyNotFoundException($property, static::class);
    }
 
    public function __isset($property)
    {
        return property_exists($this, $property);
    }
 
    public function __unset($property)
    {
        $this->__set($property, null);
    }
}
```

RFC proponuję zamianę ww. kodu na:

#### Opcja 1
```php
class User {
    public:private int $id;
    public:protected string $name;
 
    public function __construct(int $id, string $name) {
        $this->id = $id;
        $this->name = $name;
    }
}
```
#### Opcja 2
```php
class User {
    public private(set) int $id;
    public protected(set) string $name;
 
    public function __construct(int $id, string $name) {
        $this->id = $id;
        $this->name = $name;
    }
}
```

Rozwiązania nie wydają się w pełni czytelne, ale autor wyraźnie zaznacza, że mogą one być podstawą do utworzenia słów kluczych `readonly`, `immutable` oraz `writeonce`.


### 🆕 Allow trailing comma in closure use lists [[RFC](https://wiki.php.net/rfc/trailing_comma_in_closure_use_list)]
Zmieniono już wiele miejsc, gdzie dopuszczane jest dodawanie "trailing commas", lecz występują jeszcze miejsca, gdzie nie jest to możliwe.  Takim miejscem jest chociażby `use` przy funkcjach.

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

### 🆕 Configurable string length in getTraceAsString() [[RFC](https://wiki.php.net/rfc/throwable_string_param_max_len)]
W chwili obecnej poniższe metody zwracają zawartości o maksymalnej długości do 15 bajtów:
- `Throwable->getTraceAsString()`
- `Throwable->__toString()`
RFC proponuje, aby ta długość była możliwa do skonfigurowania przy pomocy nowej opcji konfiguracyjnej:
```
zend.exception_string_param_max_len=15
```

## RFC - Odrzucone

### ❌ PHP Namespace in core [[RFC](https://wiki.php.net/rfc/php-namespace-in-core)]
Głosowanie ws. wprowadzenia przestrzeni nazw `\Php` do core języka zakończyło się. Zmiany wspomniane w RFC nie zostaną wdrożone, jako że większość głosujących była "przeciw".

### ❌ Opcache optimization without any caching [[RFC](https://wiki.php.net/rfc/opcache.no_cache)]
W chwili obecnej opcode optimizer i JIT nie funkcjonują bez włączonej pamięci podręcznej. Propozycją tego RFC jest dodanie nowej opcji konfiguracyjnej `opcache.no_cache`, która umożliwi jej wyłączenie. W chwili obecnej jednak, RFC zostało przegłosowane "przeciw" większością głosów. Większość takich osób może zmieni zdanie, jeśli optymalizacje opcode dla Opcache zostaną przeniesione do core PHP.


## Inne
- Premiera PHP 8 została przełożona na 26 listopada 2020 roku.
- Dnia 8 czerwca 2020 roku PHP obchodziło swoje 25 urodziny.
- Wewnętrzne klasy PHP implementujące  `Traversable` korzystają już z `Iterator` lub `IteratorAggregate`
