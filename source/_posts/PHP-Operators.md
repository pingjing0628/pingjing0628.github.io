---
title: PHP Operators
date: 2019-10-27 15:52:16
tags: PHP
categories: PHP
---
## PHP Operators

<!--more-->
***

* Return true if $a is not TRUE.
```php=
echo "\n".'- Not -'."\n";

var_dump(! 0); // true
var_dump(! false); // true
var_dump(! null); // true
var_dump(! ''); // true
var_dump(! []); // true
var_dump(! new stdClass); // false
```

* Return true if $a is not FALSE.
```php=
echo "\n".'- Is -'."\n";

var_dump(!! 0); // false
var_dump(!! false); // false
var_dump(!! null); // false
var_dump(!! ''); // false
var_dump(!! []); // false
var_dump(!! new stdClass); // true
```


* Return true if either $a or $b is TRUE.
```php=
echo "\n".'- Or -'."\n";

var_dump(0 || 'value'); // true
var_dump(false || 'value'); // true
var_dump(null || 'value'); // true
var_dump('' || 'value'); // true
var_dump([] || 'value'); // true
var_dump(new stdClass || 'value'); // true
```

* Return true if both $a and $b are TRUE.
```php=
echo "\n".'- And -'."\n";

var_dump(0 && 'value'); // false
var_dump(false && 'value'); // false
var_dump(null && 'value'); // false
var_dump('' && 'value'); // false
var_dump([] && 'value'); // false
var_dump(new stdClass && 'value'); // true
```

* Return $a if $a is not FALSE.
```php=
echo "\n".'- Ternary -'."\n";

var_dump(0 ?: 'value'); // "value"
var_dump(false ?: 'value'); // "value"
var_dump(null ?: 'value'); // "value"
var_dump('' ?: 'value'); // "value"
var_dump([] ?: 'value'); // "value"
var_dump(new stdClass ?: 'value'); // {}
```

* Return $a if $a exists and is not NULL.
```php=
echo "\n".'- Null Coalescing -'."\n";

var_dump(0 ?? 'value'); // 0
var_dump(false ?? 'value'); // false
var_dump(null ?? 'value'); // "value"
var_dump('' ?? 'value'); // ''
var_dump([] ?? 'value');  // []
var_dump(new stdClass ?? 'value'); // {}
var_dump($foo ?? $bar ?? 'value'); // "value"
```

`die();`
