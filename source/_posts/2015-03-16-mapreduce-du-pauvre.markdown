---
layout: post
title: "MapReduce du pauvre"
date: 2015-03-16 22:41:41 +0100
comments: true
categories: [Bash, Php, MapReduce] 
---

#MapReduce du pauvre.

##une commande linux
```bash
find -name '*.php' | xargs -p 4 grep "ma chaine de caractère" | wc -l
```
Cela ne se voit pas mais je viens de faire un map/reduce 

* je prend tout les fichiers php
* je lance un grep avec le résultat que je passe à travers le pipe.
* puis je compte le nombre de résultats.

```
[filter] -> [map] -> [reduce]
```
ici l'astuce tient dans une astuces `xargs -p 4` qui ordonne à xargs d'utiliser 4 processeurs en parrallèle ! Donc qui me permet d'aller quatre fois plus vite. On comprend l'interet du map/reduce. Je peux **dispatcher** le travail sur plusieurs instances(ici processeurs). On retrouve ce fonctionnement dans beaucoup de logiciels actuels. L'avantage ici est que j'utilise linux et que je n'ai installé aucun programme et en multiprocesseur je fais pleinenement confiance au kernel de mon linux. Il existe aussi un programme `parallel` mais je ne m'en suis jamais servi.

##En php ?
Il existe bien entendu les fonctions `array_map`, `array_filter`, `array_reduce` mais il ne sont pas spécialement plus rapide qu'une boucle foreach. Il existe un map pour les Collection de doctrine.

je vais montrer que l'on peux écrires toute les opérations possibles avec 'array_reduce'. 

```php
function mysum($array) {
    return array_reduce($array, function($acc, $item) {
        return $acc += $item;
    });
}
function mymult($array) {
    return array_reduce($array, function($acc, $item) {
        return $acc *= $item;
    },1);
}

function mymax($array) {
    return array_reduce($array, function($acc, $item) {
        return max($acc, $item);
    });
}// le min est un peu moins simple

function map($function, $array) {
    return array_reduce(
        $array,
        function ($acc, $input) use ($function) {
            $acc[] = $function($input);
            return $acc;
        });
}

function filter($function, $array) {
    return array_reduce(
        $array,
        function ($acc, $input) use ($function) {
            if ($function($input)) {
                $acc[] = $input;
            }
            return $acc;
        });
}

function mycount($array) {
    return array_reduce(
        $array,
        function ($acc, $input)  {
        return ++$acc;
},0);
}

$a = [1,2,3,4,5];
var_dump(mysum($a)); // 15
var_dump(mymult($a)); // 120
var_dump(mymax($a)); // 5
var_dump(mycount($a)); // 5
var_dump(map(function($input) { return 2 * $input;}, $a)); // [2,4,6,8,10]
var_dump(filter(function($input) { return ($input > 4); }, $a)); // [6]

```
Map et Reduce sont des fonctions un peu particulières, elle prenent comme argument des fonctions. On parle de fonctions d'ordre supérieure, parfois de functor (mais çà c'est une autre histoire ..)



