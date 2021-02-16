
# Monthly news - Styczeń 2021

## Najważniejsze
- W trakcie głosowania: [Array unpacking with string keys](#-array-unpacking-with-string-keys-rfc)
- Nowe: [Object keys in arrays](#-object-keys-in-arrays-rfc)

## RFC - Zaakceptowane

### ✅ Restrict $GLOBALS usage [[RFC](https://wiki.php.net/rfc/restrict_globals_usage)]
Większością głosów zdecydowano o ograniczeniu dostępu do `$GLOBALS`. W związku z tym, działać będzie m.in. poniższy kod:

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

### ✅ Add is_array_and_list [[RFC](https://wiki.php.net/rfc/is_list)]
W PHP 8.1 pojawi się nowa funkcja `is_array_and_list()`, będącą ekwiwalentem do następującej implementacji:
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

### ✅ Array unpacking with string keys [[RFC](https://wiki.php.net/rfc/array_unpacking_string_keys)]
RFC ma rozszerzyć możliwości operatora "spread":
```php
$array1 = ["a" => 1];
$array2 = ["a" => 2];
$array = ["a" => 0, ...$array1, ...$array2];
var_dump($array); // ["a" => 2]
```

## RFC - W trakcie głosowania

### 🚧 Dump results of expressions in `php -a` [[RFC](https://wiki.php.net/rfc/readline_interactive_shell_result_function)]
Zaproponowano inne prezentowanie danych wyjściowych w przypadku korzystania z `php -a`.

## RFC - Nowe

### 🆕 Object keys in arrays [[RFC](https://wiki.php.net/rfc/object_keys_in_arrays)]
W momencie prowadzenia rozmów nt. enumów w PHP, pojawił się problem używania ich jako klucze tablic. Jest to spowodowane tym, że enumy mają być  swojego rodzaju specjalnymi obiektami, a obiektów w chwili obecnej nie można wykorzystywać jako kluczy tablic. Niniejsze RFC ma to zmienić:

```php
$obj1 = new stdClass;
$obj2 = new stdClass;
 
$array = [];
$array[$obj1] = 1;
$array[$obj2] = 2;
 
var_dump($array[$obj1]); // int(1)
var_dump($array[$obj2]); // int(2)
```

### 🆕 Object scoped RNG Implementations [[RFC](https://wiki.php.net/rfc/object_scope_prng)]
RFC proponujące utworzenie osobnej przestrzeni nazw dla algorytmów zajmujących się generowaniem losowych liczb.

### 🆕 fsync() function [[RFC](https://wiki.php.net/rfc/fsync_function)]
Zaproponowanie funkcji podobnej do `fflush()`, lecz działającej z tą różnicą, że:
> fflush() only instructs the application to flush its internal buffers out to the OS, fsync() further instructs the OS to flush its buffers to physical storage, ensuring a completed and persisted write before returning any success


### 🆕 var_representation() : readable alternative to var_export() [[RFC](https://wiki.php.net/rfc/readable_var_representation)]
Autor proponuje dodanie nowej funkcji `var_representation()`, będącej alternatywą do  `var_export()` w temacie bardziej czytelnego przedstawienia danych w output. Parę przykładów podano w RFC.
