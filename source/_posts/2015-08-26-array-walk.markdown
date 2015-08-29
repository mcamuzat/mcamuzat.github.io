---
layout: post
title: "array_walk, array_map, array_walk_recursive"
date: 2015-08-26 22:30:55 +0200
comments: true
categories: ["php", "symfony", "Phpunit"] 
---

Une question que l'on m'a posé.

Quel instruction je n'ai jamais jamais réussi à utiliser dans php ? Dans le sens jamais trouvé d'utilisation pratique.

Ma réponse est un `array_recursive_walk()` et dans la même idée les `RecursiveIteratorIterator`

Et puis on m'a demander la différence entre un `array_walk` et un `array_map`.

## `array_map`

Voici quelques exemples

``` php
// via un array_map
$array = ['one', 'two', 'three']
$out = array_map(uc_first, $array);
```

`array_map` ne touche pas le tableau original.

En fait `array_map` fait exactement ceci.

```
$out = [ ucfirst('one'), ucfirst('two'), ucfirst('three') ];
```

De manière générale

```
array_map(function($a),[a ,b, ..,z]) 
[function(a), function(b), function(c), .. function(z)].
```

 * On renvoie un tableau de la **même** taille.
 * la fonction appelé doit contenir un **return**
 * C'est de la programmation fonctionnelle (c'est le **map** de **Map**Reduce).
 * On ne connais pas l'index (que l'on pourrai récupérer dans un `foreach($array as $key => $value)`).

## `Array_walk`

``` php
$array = ['one', 'two', 'three']
function toMaj(&$input, $key) {
    // ucfirst give error with 2 arguments
    $input = ucfirst($input);
}
array_walk($array, 'toMaj');
var_dump($array);
```

Constatons:

 * Je passe par référence `&input`.
 * Donc je n'ai pas besoin de `return`.
 * Donc nous sommes dans du **procédural**. (ce qui n'est pas forcément un défaut)
 * J'ai économisé une variable
 * On est obligé d'écrire des fonctions à deux arguments certaines fonctions comme `ucfirst` refusent d'avoir plusieurs arguments.
 * Par contre, on connait l'index (`$key`) mais impossible de le modifier.
 * `array_walk` renvoie `true` or `false` ce qui n'aide pas beaucoup

```
$array = ['one', 'two', 'three']
function toMaj(&$input, $key) {
    $input = $key.' : 'ucfirst($input);
}
array_walk($array, 'toMaj');
var_dump($array);
```

Pour résumer
les deux fonctions ont un peu près le même résultat.

 * `array_walk` prend un array et remplace chaque valeurs par Fonction(valeur).
 * `array_map` prend un array et recrée un array de la même taille.

## Le `Array_recursive_walk`

Un exemple de code.

Je voulais faire un `CamelCase` -> `snake_case` mais comme le `array_walk` impossible de modifier les clés des index. 

Soit l'exemple suivant

``` php
$array =  ["Tortue-ninja" => ["leonardo" , "donatello", "michelangelo","raphael"], "Mechant" => ["shredder", "krang"]];
array_walk_recursive($array, function(&$item, $key) {
    if ($key === "Tortue-ninja") {
        echo 'Cowabunga!';
    }
    $item = ucfirst($item);
});

var_dump($array);
```
Le programme n'affiche pas de `Cowabunga`, mais j'ai mis en majuscule toutes les valeurs.

```
array(2) {
  'Tortue-ninja' =>
  array(4) {
    [0] =>
    string(8) "Leonardo"
    [1] =>
    string(9) "Donatello"
    [2] =>
    string(12) "Michelangelo"
    [3] =>
    string(7) "Raphael"
  }
  'mechant' =>
  array(2) {
    [0] =>
    string(8) "Shredder"
    [1] =>
    string(5) "Krang"
  }
}

```

On ne passe jamais sur le clé : `Tortue-ninja`, d'ailleurs la documentation est claire

> Toute clé qui est associée à un tableau n'est pas passée à la fonction de rappel.

Un vrai exemple
``` php
        array_walk_recursive($data, function (&$value) {
            if ($value instanceof \Traversable) {
                $value = iterator_to_array($value);
            }
        });
````

Un exemple que l'on retrouve dans [PHPUnit]() et [Symfony2]()

On veut logger des objets en les serialisant en Json. Certain objects (comme les ArrayCollection et autres..) sont *Traversables* (c'est à dire qu'il possède un Iterateur (On implémente `current`, `next`, `rewind`, `valid`). En php il est possible de transformer les Itérateurs en Array avec l'instruction `iterator_to_array()`. ainsi on transforme l'objet en Array pour avoir plus d'information.

## En conclusion 

Donc je n'ai jamais réussi à écrire un `array_walk_recursive` mais au moins j'ai réussi à écrire un post dessus.
