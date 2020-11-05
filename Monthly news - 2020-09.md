
# Monthly news - Wrzesień/Październik

## Najważniejsze
- Zaakceptowane: [Shorter Attribute Syntax Change](#-%EF%B8%8F-named-arguments-rfc)
- Nowe: [Short Functions](#-%EF%B8%8F-named-arguments-rfc)

## RFC - Zaakceptowane

### 🔥 ✅ Shorter Attribute Syntax Change [[RFC](https://wiki.php.net/rfc/shorter_attribute_syntax_change)]
No i stało się - w końcu ten temat został rozstrzygnięty. Ostatecznym wyborem dla atrybutów w PHP 8 okazała się składnia `#[Attr]`. Argumenty przedstawiane na rzecz tego rozwiązania:
- Mniejsze problemy z parsowaniem - występuje wyraźne rozpoczęcie atrybutu `#[` oraz jego zakończenie `]`.
- Składnia `#[]` jest już używana w innych językach programowania.
- Ma możliwość grupowania atrybutów.
- Pozwala rozszerzyć funkcjonalność i zagnieżdżone atrybuty w przyszłości.
- Ma wsteczną kompatybilność względem PHP 7

Ważną informacją jest także to, że mimo obecnie panującego etapu "feature freeze", zmiana składni zostanie wdrożona do PHP 8.0.0RC1.

## RFC - Nowe

### 🔥 🆕 Short Functions [[RFC](https://wiki.php.net/rfc/short-functions)]
Po wdrożeniu "arrow functions" w PHP 7.4, oczywiste było, że temat zostanie pociągnięty dalej. Została więc zaproponowana możliwość tworzenia skróconych funkcji nazwanych oraz metod:

```php
function add(int $a, int $b): int => $a + $b;
 
function add(int $a, int $b): int 
{
    return $a + b;
}

class Adder
{
    public function __construct(private int $val) {}
 
    public function add(int $in): int => $in + $this->val;
 
    public function add(int $in): int {
        return $in + $this->val;
    }
}
```
Kolejne przykłady:
```php
function pick_one(int $a) 
{
    return match($a) {
        1 => 'One',
        2 => 'Two',
        3 => 'Three',
        default => 'More',
    };
}
```
vs.
```php
function pick_one(int $a) => match($a) {
    1 => 'One',
    2 => 'Two',
    3 => 'Three',
    default => 'More',
};
```
```php
class Person
{
    public function __construct(private string $firstName) {}

    public function getFirstName(): string
    {
        return $this->firstName;
    }
}
```
vs.
```php
class Person
{
    public function __construct(private string $firstName) {}
    public function getFirstName(): string => $this->firstName;
}
```

### 🆕 any() and all() on iterables [[RFC](https://wiki.php.net/rfc/any_all_on_iterable)]

Zdaniem autora, w PHP przydałyby się funkcje `any()` oraz `all()` dla danych o typie `iterable`. Dzięki temu będzie można implementować prostszy kod. Przykładem może być zamiana tego kodu:
```php
$satisifes_predicate = false;
foreach ($item_list as $item) {
    if (API::satisfiesCondition($item)) {
        $satisfies_predicate = true;
        break;
    }
}
if (!$satisfies_predicate) {
    throw new APIException("No matches found");
}
```
na następujący:
```php
if (!any($item_list, fn($item) => API::satisfiesCondition($item))) {
    throw new APIException("No matches found");
}
```
W przyszłości:
- będzie można zastosować nowy parametr `$flag = 0` w identyczny sposób, jaki istnieje przy funkcji `array_filter()`;
- będzie można dodać również i funkcję `first()`.

### 🆕 Support for multi-line arrow functions [[PR](https://github.com/php/php-src/pull/6246)]
W PHP 7.4 istnieje możliwość tworzenia tzw. "arrow functions". Warunkiem ich funkcjonowania jest używanie ich w tylko w jednej linii. Został zaproponowany PR, który umożliwia również używanie ich w wielu liniach.

```php
$guests = array_filter($users, fn ($user) => {
    $guest = $repository->findByUserId($user->id);

    return $guest !== null && in_array($guest->id, $guestsIds);
});
```

### 🆕 OOP API for HashContext [[RFC](https://wiki.php.net/rfc/hash.context.oop)]
Twórcy PHP już jakiś czas temu określili się, że z czasem będą chcieli pozbyć się typu `resource` z PHP. Z tego też powodu w PHP 7.2 powstała klasa `HashContext`, na której operują funkcje `hash_*`. Wcześniej działały one właśnie w oparciu o typ `resource`. W chwili obecnej jednak, wspomniana klasa wygląda dość biednie:
```php
class HashContext {
    private __construct(void)
}
```
W ramach tego RFC, autor chciałby rozszerzyć `HashContext` o odpowiednie API:
- `HashContext::__construct()` jako klasa publiczna
- Dodać metody:
   - `update(string $data): this`
     działająca jak `hash_update()`
   - `updateFile(string $uri, ?StreamContext $context = null): this`
     działająca jak `hash_update_file()`
   - `updateStream(Stream $stream, int $length = -1): this`
     działająca jak `hash_update_stream()`
   - `final(bool $raw_output = false): string`
     działająca jak `hash_final()`

Należy zaznaczyć, że pseudo klasy `Stream` oraz `StreamContext` są wrapperami na odpowiednie zasoby  `php_stream` oraz `php_stream_context`.

### 🆕 Explicit octal integer literal notation [[RFC](https://wiki.php.net/rfc/explicit_octal_notation)]
RFC przedstawia propozycję nowej składni dla ósemkowego systemu liczbowego - prefiksu `0o`:
```php
0o16 === 14; // true
0o123 === 83; // true
 
016 === 0o16; // true
```
Da więc to możliwość usunięcia w przyszłości składni `016`.


## Inne
- Opublikowany został Composer 2.0 - [LINK](https://github.com/composer/composer/releases/tag/2.0.0)
- Zapowiedziano Psalm 4 - [LINK](https://psalm.dev/articles/psalm-4)
