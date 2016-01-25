---
layout: post
title: "Les monades 5: les applicatives"
date: 2015-12-20 15:49:14 +0100
comments: true
categories: ["php", "haskell", "programmation fonctionnelle"] 
---

Nous allons voir les foncteurs applicatifs.

Reprenons le container Maybe

{% img center /images/applicative.png 428 212 'Avec évaluation partielle' 'avec évaluation partielle' %}

Ce que j'aimerai c'est pouvoir faire ce genre d'opération

``` php
Maybe(3) + Maybe(3) = Maybe(6)
Container(4) * Container(5) = Container(40)
str_repeat(Maybe(".oOo"), Maybe("3")) = Maybe(".oOo.oOo.oOo");
```


La bonne nouvelle c'est que c'est possible. 

<!--more-->

J'ai besoin de 2 étapes:

 - Nous allons mettre en place la librairie [php-functionnal](https://github.com/widmogrod/php-functional). Il devient difficile d'utiliser sa propre librairie.
 - Nous avons besoin d'une nouvelle opération `ap` pour applicative.

## installation de php-functionnal

Grâce à composer c'est très simple.

``` 
composer require widmogrod/php-functional
```

Voici le fichier php dans la racine du projet 
``` php
<?php
require_once __DIR__ . '/vendor/autoload.php'; // Autoload files using Composer autoload

use Monad\Maybe;
use Functional as f;

$a = Maybe\Just::of(1);
$b = $a->map(function($a) {return $a + 1;});
var_dump($b);
```

Si vous obtenez ceci 
``` php
class Monad\Maybe\Just#4 (1) {
  protected $value =>
  int(2)
}
```

Tout va bien!!

Je n'ai pas utilisé les mêmes noms que la librairie voici les traductions 

 * Container -> Identity
 * Collection -> Collection
 * Some -> Just
 * Nothing -> Nothing
 * `Maybe\just(10)` est un helper `Maybe\Just::of(10)`
 * `Maybe\nothing()` -> `Maybe\Nothing::of(10)`

Nous allons faire quelque chose de curieux puisque nous ne mettons pas une valeur dans notre Maybe mais une fonction ! 

Regardons un exemple simple

``` php
$addOne = new Maybe\just(function($a) { return $a + 1;});
$value = new Maybe\just(5);
$result = $addOne->ap($value);

var_dump($result);
```

Dans le premier Maybe on a mis une fonction. `ap` prend en entrée un Maybe.

Nous obtenons 
``` php
class Monad\Maybe\Just#5 (1) {
  protected $value =>
  int(6)
}

```

Essayons avec `Nothing`

``` php
$addOne = new Maybe\just(function($a) { return $a + 1;});
$value = new Maybe\nothing();
$result = $addOne->ap($value);
```

Le résultat
``` php
class Monad\Maybe\Nothing#4 (0) {
}
```

Cela ne semble pas très utiles

Nous allons utiliser notre fonction `curryN` du [post précédent](). 

``` php
function add($a, $b)
{
    return $a + $b;
}

$add = Maybe\just(f\curryN(2, "add"));

$result = $add->ap(MayBe\just(5))->ap(Maybe\just(5));

// just(10)

```

 * la fonction add est une addition et prend deux arguments. `(? + ?)`
 * Je la transforme en évaluation partielle avec CurryN() et je la place dans un `just(? + ?)`
 * Au premier `ap` je soumet le premier argument, il manque encore un argument , la fonction devient `just( 5 + ?)`.
 * Au second `ap` l'argument manquant est fournis. La fonction est complète `just( 5 + 5)` -> `just(10)`.

La recette est simple, Je mets la fonction à plusieurs arguments dans mon Maybe avec le curryN. et j'applique chacun des arguments.

En fait si on fait une image

{% img center /images/applicative.png 428 212 'Avec évaluation partielle' 'avec évaluation partielle' %}


Mais nous pouvons faire cela avec toutes les fonctions

```
$superStrRepeat = Maybe\just(f\curryN(2, "str_repeat"));

//alors 
var_dump(
   $superStrRepeat->ap(Maybe\just(".o0o"))->ap(Maybe\just(3))
);
//Maybe\just(".oOo.oOo.oOo");
var_dump(
   $superStrRepeat->ap(Maybe\nothing())->ap(Maybe\just(3))
);
//Maybe\Nothing();
var_dump(
   $superStrRepeat->ap(Maybe\nothing())->ap(Maybe\nothing()))
);
// Maybe\Nothing
```

C'est pratique car nous pouvons maintenant appliquer des fonctions à plusieurs arguments. et des fonctions qui ne travaillent pas avec des object Maybe, Nothing.

Quand on "augmente" les fonctions pour travailler avec d'autre types,  on appelle cela le `Lift`

D'ailleurs cela s'exprime en 1 ligne avec la librairie

``` php
var_dump(f\liftA2("add", MayBe\just(5), Maybe\just(5)));
// Maybe\Just(10)

var_dump(f\liftA2("str_repeat",MayBe\just(".o0o"), Maybe\just(5)));
// Maybe\just(".o0o.o0o.o0o.o0o.o0o")
```
{% img center /images/str_repeataveccontainer.png 517 425 'On utilise la fonction LiftA2' '' %}


## Pour résumer.

 * Les *functors* implémentent la fonction `map` qui prend en entrée une fonction.
 * Les *applicatives* implémentent la fonction `ap` prend en entrée un applicative. Cela permet d'appliquer des fonctions à plusieurs arguments.
 * Les *monades* implémentent la fonction `bind` qui prend entrée une fonction *monadique* c'est à dire qui renvoie une Nomade.

Tous les monades que j'ai présenté implémentent les 3 fonctions (Maybe, Collection, Identity(Container)). 

## En conclusion.

Dans le prochain Post nous allons voir le cas particulier de `ap` pour les collections.

 * Partie 1 : [Monade/Functor](/blog/2015/11/11/les-monades-en-php-cest-possible-dot/)
 * Partie 2 : [Le functor Maybe](/blog/2015/11/15/les-monades-suite-le-functor-maybe-dot/)
 * Partie 3 : [Le functor Maybe avec le Bind](/blog/2015/11/22/les-monades-3-le-maybe-suite/)
 * Partie 4 : [Les listes](/blog/2015/11/29/les-monades-les-listes/)
 * Interlude : [Les évaluations partielles](/blog/2015/12/06/les-monades-evaluation-partielle/)
 * Partie 5 : [Les applicatives](/blog/2015/12/20/les-monades-5-les-applicatives/)
 * Partie 6 : [Les applicatives et les listes](/blog/2016/01/25/les-monades-applicative-et-les-listes/) 

