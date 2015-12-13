---
layout: post
title: "Les Monades : Evaluation partielle"
date: 2015-12-06 18:49:49 +0100
comments: true
categories: ["programmation fonctionnelle", "PHP", "Haskell"] 
---

## Interlude.

J'ai besoin pour le prochain chapitre d'introduire la notion d'application partielle. Cela consiste à évaluer une partie de la fonction même si je n'ai pas tous les arguments.

Soit la fonction suivante.

``` php
function plus($a, $b) {
   return $a + $b;
}
```

Que donne l'exécution ?
``` php
$result = plus(10); 
```

<!--more-->

Une erreur bien entendu, puisque il manque un argument..

Voici une fonction tirée de la librairie [php-functionnal](https://github.com/widmogrod/php-functional)

``` php
function push(array $array, array $values)
{
    foreach ($values as $value) {
        $array[] = $value;
    }
    return $array;
}

function curryN($numberOfArguments, callable $function, array $args = [])
{
    return function () use ($numberOfArguments, $function, $args) {
        $argsLeft = $numberOfArguments - func_num_args();
        return $argsLeft <= 0
            ? call_user_func_array($function, push($args, func_get_args()))
            : curryN($argsLeft, $function, push($args, func_get_args()));
    };
}
```

Et maintenant reprenons ma première fonction
``` php
$add = curryN(2, function($a, $b) {return $a + $b;});
```

Maintenant réessayons notre commande

``` php
$add10 = $add(10);
```

Je n'ai pas d'erreur mais il y a mieux !

``` php
$result = $add10(10) // 20.
```

Varions encore un peu avec aucun argument

``` php
$addition = $add();
result = $addition(10,10);//20
```

S'il manque un argument, la fonction ne renvoie pas de résultat mais une nouvelle fonction. 

On appelle cela l'évaluation partielle.

C'est plutôt pratique..

## Quelques applications pratiques

### On réutilise mieux les calculs.

Par exemple
```
$result1 = $valeurTresComplique + $valeursTresSimple1;
$result2 = $valeurTresComplique + $valeursTresSimple2;
```

Devient 
```
$partiel = $add($valeurTresComplique);
$result1 = $partiel($valeursTresSimple1);
$result2 = $partiel($valeursTresSimple2);
```

### C'est plus simple à tester.

Si toute les fonctions ne prennent qu'un argument, Il y a moins de cas à tester. 
De plus cela permet une meilleure isolation du code. Une fonction à 5-6 arguments est rarement un bon signe dans le code.


### Cela permet de chainer les fonctions.

Nous allons nous servir de cette astuce pour nos monades/functors. Depuis le début on ne peux mettre qu'une seule valeur dans mon *container* donc comment faire pour faire des fonctions à plusieurs arguments ?

## Les évaluations partielles 

Il faut quand même noter que le langage PHP n'est pas génial pour le coup.

L'expression suivante en javascript est parfaitement légale.
``` js
result = add(10)(5);
```
je suis obligé d'utiliser une variable intermédiaire en PHP.
``` php 
$add10= add(4);
$result = $add10(5);
```
Bref la syntaxe n'est pas très pratique.

## Conclusion

En Haskell et [OCaml](https://fr.wikipedia.org/wiki/OCaml) l'évaluation partielle est la norme.
``` haskell
max 10 10
```
En fait le langage fait. 
``` haskell
(max 10) 10
```

Transformer une fonction à plusieurs arguments en une série de fonction à un argument s'appelle la *Curryfication*. Cela vient du prénom de la première personne a avoir écris sur le sujet [Haskell Curry](https://fr.wikipedia.org/wiki/Haskell_Curry). Le nom de famille doit voir dire quelque chose aussi.. 

Cela semble un peu compliqué et pas forcement intéressant sur les exemples que j'ai choisi. Mais dans le prochain post nous allons utiliser cette notion.

Merci de m'avoir lu.

 * partie 1 : [Monade/Functor](/blog/2015/11/11/les-monades-en-php-cest-possible-dot/)
 * partie 2 : [Le functor Maybe](/blog/2015/11/15/les-monades-suite-le-functor-maybe-dot/)
 * partie 3 : [Le functor Maybe avec le Bind](/blog/2015/11/22/les-monades-3-le-maybe-suite/)
 * partie 4 : [Les listes](/blog/2015/11/29/les-monades-les-listes/)




