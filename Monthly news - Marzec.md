# Monthly news - Marzec

## Najważniejsze

### Opublikowano harmonogram publikacji PHP 8
W skrócie:
* Pierwsza, stabilna wersja PHP 8 zostanie opublikowana w pierwszym tygodniu grudnia.
* Lista nowych funkcjonalności lub większych zmian wchodzących w skład PHP 8 będzie rozszerzana do lipca 2020 włącznie.
* W świat zostanie wypuszczonych 6 wersji PHP 8.0 RC. Pierwszy ma się pojawić na początku września, a ostatni w drugiej połowie listopada.

Źródło: [php.net](https://wiki.php.net/todo/php80), [thephp.website](https://thephp.website/en/issue/php8-release-schedule/).


### Nowa funkcja: [str_contains()](https://github.com/php/php-src/commit/1668ad7cb17213e67e42994e0c6911e302a3c3c5)
Do PHP zmergowano kod nowej funkcji `str_contains()`. Pojawi się ona w PHP 8.


## 2020-03-22

### Nowa funkcja: get_debug_type() [\[RFC\]](https://wiki.php.net/rfc/get_debug_type)
Zatwierdzono RFC odnośnie funkcji `get_debug_type()`. Funkcja ta zwraca typ zmiennej, którą poda się w argumencie.

```php
$x = "test";
get_debug_type($x); // "string"
```

### Głosowanie na zmienne instancyjne "readonly" [[RFC](https://wiki.php.net/rfc/write_once_properties)]
Rozpoczęło się głosowanie nad możliwością ustawienia własnościom klas trybu “readonly”.

### Obiekty Request/Response [[RFC](https://wiki.php.net/rfc/request_response)]
Update **2020-04-03**: RFC zostało odrzucone.
Rozpoczęło się głosowanie na temat wprowadzenia trzech nowych klas i interfejsu do głównej przestrzeni nazw PHP.

-   SapiRequest
-   SapiResponse, SapiResponseInterface
-   SapiResponseSender

Powyższe klasy mają za zadanie umacnić programowanie obiektowe w PHP poprzez zmianę sposobu pobierania danych z żądania, oraz wysyłania odpowiedzi na to żądanie przy pomocy obiektów. Przykładowo, zamiast odwoływać się do `$_GET` będzie można skorzystać z obiektu w sposób następujący: `$request->query`.




## 2020-03-26

### Przecinek końcowy w liście parametrów [\[RFC\]](https://wiki.php.net/rfc/trailing_comma_in_parameter_list)
Utworzone zostało nowe RFC proponujące umożliwienie dodania przecinka końcowego w liście parametrów do funkcji oraz metod. Funkcja ta istnieje już od jakiegoś czasu dla tablic.

```php
class MyClass {
    private function __construct(
        string $param1,
        string $param2,
        string $param3,
        string $param4,
        int $param5 // <-- tutaj mógłby być przecinek :)
    ) {
        // ...
    }
}
```

### Constructor Property Promotion [\[RFC\]](https://wiki.php.net/rfc/constructor_promotion)
Zaproponowano zmniejszenie boilerplate potrzebnego do określenia początkowych wartości zmiennych instancyjnych klas poprzez konstruktor. Przykład:

```php
class Point {
    public float $x;
    public float $y;
    public float $z;
 
    public function __construct(
        float $x = 0.0,
        float $y = 0.0,
        float $z = 0.0
    ) {
        $this->x = $x;
        $this->y = $y;
        $this->z = $z;
    }
}
```

Jak widać wyżej, każda z własności występuje w kodzie aż 4 razy. RFC proponuje zmniejszenie tego do:

```php
class Point {
    public function __construct(
        public float $x = 0.0,
        public float $y = 0.0,
        public float $z = 0.0
    ) {}
}
```

Tego typu rozwiązanie zastosowane zostało już m.in. w języku [Hack](https://docs.hhvm.com/hack/classes/constructors#constructor-parameter-promotion).

## 2020-03-28

Nadal trwa głosowanie nad drugim RFC dotyczącym [przeciążania operatorów w PHP](https://wiki.php.net/rfc/userspace_operator_overloading). Obecnie 30 osób jest za zaimplementowaniem tej funkcjonalności, a 16 przeciw. Prawdopodobnie w końcu uda się to przepchnąć do przodu :).

## 2020-03-31

Ruszyło głosowanie w sprawie dodania do PHP 8.0 funkcjonalności kompaktowego przypisywania wartości własnościom obiektów (z ang. "Compact Object Property Assignment"). W skrócie o co chodzi:

```php
// zamiast tego:
$myObj->a = 1;
$myObj->b = 2;
$myObj->c = 3;

// będzie można to robić tak:
$myObj->[
    a = 1,
    b = 2,
    c = 3,
];
```

Póki co zdecydowana większość głosujących jest za wdrożeniem tego pomysłu, a więc prawdopodobnie zostanie to wdrożone :). Więcej informacji o tej funkcjonalności w [RFC](https://wiki.php.net/rfc/compact-object-property-assignment).
