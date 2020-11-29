
# Monthly news - Listopad

## Najważniejsze
- Nowe: [Support for <func>::function](#--shorter-attribute-syntax-change-rfc)

## PHP 8.0
Zgodnie z zapowiedzianą datą premiery, opublikowano PHP 8.0!
https://www.php.net/releases/8.0/en.php
Wraz z tą nową wersją, w grę wchodzą następujące funkcjonalności:

- Union Types
- Named Arguments
- Match Expressions
- Attributes
- Constructor Property Promotion
- Nullsafe Operator
- Weak Maps
- Just In Time Compilation
- No i wiele więcej :)

## RFC - Zaakceptowane

### ✅ Explicit octal integer literal notation [[RFC](https://wiki.php.net/rfc/explicit_octal_notation)]
Zaakceptowano nową składnię dla ósemkowego systemu liczbowego - `0o`:
```php
0o16 === 14; // true
0o123 === 83; // true
 
016 === 0o16; // true
```

## RFC - Nowe

### 🔥🆕 Support for <func>::function syntax [[RFC](https://externals.io/message/112201)]
Zaproponowano stworzenie możliwości pobierania FQN funkcji, tak jak to ma miejsce dla klas (`::class`).

```php
// Przed
call_user_func('printf', "hello\n");

// Po
call_user_func(printf::function, "hello\n");
```

### 🆕 Closure self reference [[RFC](https://wiki.php.net/rfc/closure_self_reference)]
OP tego RFC chciałby dodać możliwość odwoływania się do closure do samej siebie wewnątrz niej poprzez zmienną `$lambda`. Niniejsze RFC jest zapewne przejdzie przez cały zestaw poprawek, jako że dość mocno psuje wsteczną kompatybilność.

Ma to przeciwdziałać następującym przykładom, kiedy do w międzyczasie zmieni się wartość zmiennej:
```php
$fibonacci = function (int $n) use (&$fibonacci) {
    if ($n === 0) return 0;
    if ($n === 1) return 1;
    return $fibonacci($n-1) + $fibonacci($n-2);
};
 
echo $fibonacci(10). "\n";
$this_is_the_original_fibonacci = $fibonacci;
// ... many lines of code here
$fibonacci = function (int $n) { return rand(0, $n); };
// ... many lines of code here
 
echo $this_is_the_original_fibonacci(10);
```


## Inne
- Prowadzone są rozmowy na temat operatora union `&` dla interfejsów [LINK](https://externals.io/message/112194).
```php
function foo(A & B & E $object) { ... }
```

- Bogdan Ungureanu zaimplementował "literal types" w PHP. Stworzony PR możecie zobaczyć [TUTAJ](https://github.com/BogdanUngureanu/php-src/commit/37413543fafb300468d83ed8fcc72dc859c577cb). Choć funkcjonalność ciekawa, zapewne nie wejdzie do PHP. Autor otwarcie przyznał, że kod jest dla niego eksperymentem, i próbą nauczenia się wnętrza PHP.
- Wypuszczono w świat [Doctrine DBAL 3.0.0](https://github.com/doctrine/dbal/releases/tag/3.0.0)
- Opublikowano [Xdebug 3.0.0](https://xdebug.org/announcements/2020-11-25)
