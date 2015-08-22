---
layout: post
title: "QuickCheck une autre façon de tester"
date: 2015-08-22 19:01:42 +0200
comments: true
categories: ["php", "PHPunit", "Haskell", "QA"]  
---

## Introduction

Nous allons voir ensemble, une nouvelle façon de faire des tests. Nous allons installer utiliser un projet [php-quickcheck](https://github.com/steos/php-quickcheck). L'idée ici n'est pas d'écrire des tests, mais demander au logiciel de les générer. 

## Installation.

Nous allons créer le `composer.json` suivant.

``` json
{
  "require": {
    "steos/php-quickcheck": "dev-master"
  }
}
```

Puis créer un fichier `test.php`.

``` php

require_once __DIR__ . '/vendor/autoload.php'; // Autoload files using Composer autoload

use QCheck\Generator as Gen;
use QCheck\Quick;

```

Un petit `composer install`. Et tout est en place.

## Exemple N°1

### Affirmation

Je vais essayer de prouver que `array_merge($list1, $list2) == $list1 + $list2` (**ce qui est faux**)

Je l'écris dans la fonction suivante

``` php
function isEqual(array $list1, array $list2) {
    return (array_merge($list1, $list2) == $list1 + $list2);
}
```

### Mise en place et contre-exemple.

Voici le code

``` php
$test = Gen::forAll(
    [Gen::ints()->intoArrays(), Gen::ints()->intoArrays()], isEqual
 );

```

`Gen::ints()->intoArrays()` génère des array avec une taille aléatoire `[0, 1], [-15,0,5], ..` que  je vais passer à la fonction `isEqual`


```
print_r(Quick::check(102, $test, ['echo' => true]));

```

Je vais lancer 102 fois mon test.

Voici ce que me dit le programme dès que je lance.

```
..F
Array
(
    [result] => 
    [seed] => 1440263990644
    [failing_size] => 2
    [num_tests] => 3
    [fail] => Array
        (
            [0] => Array
                (
                    [0] => 1
                )

            [1] => Array
                (
                    [0] => -2
                    [1] => 1
                )

        )

    [shrunk] => Array
        (
            [nodes_visited] => 9
            [depth] => 3
            [result] => 
            [smallest] => Array
                (
                    [0] => Array
                        (
                            [0] => 0
                        )

                    [1] => Array
                        (
                            [0] => 0
                        )

                )

        )

)
```

Le résultat est intéressant, Le logiciel a essayé 3 fois, au troisième essai l'exemple `([1], [-2,1])` donne un cas qui ne marche pas.

Vérifions avec `php -a`

``` 
php > var_dump(array_merge([1], [-2,1]));
array(3) {
  [0] =>
  int(1)
  [1] =>
  int(-2)
  [2] =>
  int(1)
}
php > var_dump([1] + [-2,1]);
array(2) {
  [0] =>
  int(1)
  [1] =>
  int(1)
}

```

Effectivement.. Mais il y a mieux. Le logiciel a fais un *shrunk*, c'est à dire qu'il a calculé le plus petit exemple possible qui est `([0], [0])`. 

Donc la librairie me donne tort et en plus me donne le contre-exemple.

## Exemple N°2 

### Affirmation

J'affirme que `(sort (array) == sort(sort(array))` que en gros cela ne sert à rien de trier deux fois un array.


### Mise en place

```
$test2 = Gen::forAll(
    [Gen::ints()->intoArrays()],
    function ($list) {
        $lista = $list;
        $listb = $list;
        sort($lista);
        sort($listb);
        sort($listb);
        return ($lista == $listb);
    }
  );

print_r(Quick::check(101, $test2, ['echo' => true]));

```

Je lance le logiciel 

```
.....................................................................................................Array
(
    [result] => 1
    [num_tests] => 101
    [seed] => 1440265001108
)
```

Le logiciel semble d'accord. Il a fait 101 tests, mais il n'a pas trouvé de contre-exemple. 

## Exemple N°3

Nous allons encoder en `run legth encoding` qui est l'actuel encodage des fichiers bitmaps.

Quelque exemples:
``` 
Input: WWWWWWWWWWWWBWWWWWWWWWWWWBBBWWWWWWWWWWWWWWWWWWWWWWWWBWWWWWWWWWWWWWW 
Output: 12W1B12W3B24W1B14W 
```

Il y a `12W` puis `1B` etc .. je compresse ma chaîne de caractères.

Dans l'autre sens
```
Input: 12W1B12W3B24W1B14W 
Output: WWWWWWWWWWWWBWWWWWWWWWWWWBBBWWWWWWWWWWWWWWWWWWWWWWWWBWWWWWWWWWWWWWW 
```

Voici une implémentation en php

``` php
function encode($str) {
    return preg_replace_callback(
        '/(.)\1+/',
        function($m) {
            return sprintf('%s%s', strlen($m[0]), $m[1]);
        },
        $str
    );
};
function decode($str) {
    return preg_replace_callback(
        '/(\d+)(\D)/',
        function($m) {
            return str_repeat($m[2], $m[1]);
        },
         $str
     );
}
```

Mon implémentation est correcte, mais il y a un petit souci. Pouvez vous deviner le souci de mon programme. 

A priori `$input == decode(encode($input))`

### Mise en place. 

``` php
$test3 = Gen::forAll(
    [Gen::alphaNumStrings()],
    function ($string) {
      return ($string == decode(encode($string)));
    }
);
print_r(Quick::check(101, $test2, ['echo' => true]));
```

Le logiciel ne tarde pas à trouver le souci

```
.....F
Array
(
    [result] => 
    [seed] => 1440265916923
    [failing_size] => 5
    [num_tests] => 6
    [fail] => Array
        (
            [0] => G67k}
        )

    [shrunk] => Array
        (
            [nodes_visited] => 34
            [depth] => 7
            [result] => 
            [smallest] => Array
                (
                    [0] => 0 
                )

        )

)
```

La chaîne de caractère `"G67k"` ne marche pas, et en fait la chaîne `"0"` tout cours ne marche pas.

## Conclusion des 3 exemples.

 * Je n'ai pas écris de test. C'est le logiciel qui génère les tests.
 * Les tests sont aléatoires. Par exemple si j'avais limité à 5 tests l'exemple 3 pourrait passer.
 * Si le code ne passe pas le logiciel est capable de *réduire* jusqu'à trouver un contre-exemple ici la chaine `"0"` ou l'entrée `([0],[0])`
 * Un autre cas, dans le dernier exemple, j'ai pris un générateur de texte qui prend des chiffres et des lettres, si j'avais pris un générateur de lettre seulement comme `gen::alphaString`. Le test passerait sans problème. 

Ce type de logiciel s'appelle le [QuickCheck](https://en.wikipedia.org/wiki/QuickCheck) du nom du premier logiciel en [Haskell](https://en.wikipedia.org/wiki/Haskell_%28programming_language%29). Ce sont des tests aléatoires. 

Il existe deux portage en php.

 * [eris](https://github.com/giorgiosironi/eris)
 * [php-quickcheck](https://github.com/steos/php-quickcheck)

Il y a le même problème que les tests unitaires: Quand les tests unitaires ne passent pas,  il y a un problème. Mais des tests unitaires qui passent ne prouve pas forcement que le logiciel est correct. Néanmoins cette méthode qui génère des milliers de tests donne des résultats assez intéressants. La capacité a trouvé automatiquement un contre-exemple (s'il y a un contre-exemple) est vraiment un plus. 

Cela n'a pas été évident d'écrire ce post. J'ai eu un peu de mal à trouver un exemple pertinent. Je me suis inspiré des exemples de [hypothesis](https://github.com/DRMacIver/hypothesis). L'implémentation du RLE viens de [rosetta](http://rosettacode.org/wiki/Run-length_encoding) mais l'exemple en php est obsolète (la regex `/../e` php5.5 n'en veux pas). J'ai retraduis le code. 


