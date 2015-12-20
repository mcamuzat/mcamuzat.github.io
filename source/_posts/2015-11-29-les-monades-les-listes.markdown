---
layout: post
title: "Les Monades : Les listes"
date: 2015-11-29 21:44:52 +0100
comments: true
categories:  ["php", "haskell", "programmation fonctionnelle"] 
---

Nous continuons notre exploration des Monades/Functors, nous allons parler de Collection, de lapins, de marteaux et de non-déterminisme.

Voici notre nouveau *container* Le container **List**. Il prend en entrée un tableau (Array) ou en Php un `Traversable`. 
{% img center /images/collection.png 600 450 'Le container Collection' 'Le container collection' %}


Nous allons voir ensemble les listes, Collections. Nous allons voir le `map`, le `bind` nous allons voir que le comportement n'est pas exactement le même..

<!--more-->

Ne nous embêtons pas allons directement dans l'implémentation.

``` php
class Collection extends Container{
    /**
     * @param array $value
     */
    public function __construct($value)
    {

        $this->value = $this->isNativeTraversable($value)
            ? $value
            : [$value];
    }

    private function isNativeTraversable($value)
    {
        return is_array($value) || $value instanceof \Traversable;
    }

    public function map(callable $transformation)
    {
        $result = [];
        foreach ($this->value as $key => $value) {
            $result[$key] = call_user_func($transformation, $value);
        }
        return self::of($result);
    }

    public function extract() {
        $result = array();
        foreach ($this->value as $value) {
            if ($value instanceof Container) {
                $result[] = $value->extract();
            } else {
                $result[] = $value;
            }
        }
        return $result;
    }

}
```

On garde toujours la même définition. `map` prend toujours une fonction et renvoie un Objet du même type. `extract` renvoie la valeur, `Collection::of` renvoie une collection.

Quelques exemples
``` php
var_dump(
   Collection::of(array(1,2,3,4))
     ->map(function($value) {return 2 * value;})
     ->map(function($value) {return $value-1;})
     ->extract()
); // [ 1, 3, 5, 7]

var_dump(Collection::of(array("one","two","three"))
     ->map("strtoupper")
     ->map(function($value) { return $value."!!!!";})
     ->extract()
);// ["ONE!!!!", "TWO!!!!","THREE!!!!"]

```

Nous allons reprendre notre liste du post [précédent](blog/2015/11/22/les-monades-3-le-maybe-suite/)

``` php
$data = [
    ['id_article' => 1, 'titre' => 'titre1', 'meta' => ['images' => ['//first.jpg', '//second.jpg']]],
    ['id_article' => 2, 'titre' => 'titre2', 'meta' => ['images' => ['//third.jpg']]],
    ['id_article' => 3, 'titre' => 'titre3'],
];
```
{% img center /images/arraydanscontainer.png 600 450 'Un array dans le container' 'Un array dans le container' %}

* Nous allons transformer chaque ligne en `maybe` grâce à l'instruction `maybeFromValue` ([post2](blog/2015/11/15/les-monades-suite-le-functor-maybe-dot/))

{% img center /images/collectionmaybe.png 600 450 'Un array dans le container' 'Un array dans le container' %}

Nous aimerions utiliser notre instruction `get`.

l'algo :

 * je récupère le maybe.
 * J'appelle la fonction bind du maybe avec le get

Cela donne ..

``` php
function get($key)
{
    return function ($value) use ($key) {
        return $value->bind(function ($array) use ($key) {
            return isset($array[$key]) ? Some::of($array[$key]) : Nothing::of(null);
        });
    };
}
```
Oui vous ne rêvez pas c'est une fonction qui renvoie une fonction qui renvoie une fonction. 

L'implémentation est sympathique..

``` php
$result = Collection:of($data)
   ->map(fromValue)
   ->bind(get("meta"))
   ->bind(get("images"))
   ->bind(get(0))
   ->extract();
```

Nous obtenons en une ligne *sans if sans condition*.

``` php
["//first.jpg", "//third.jpg", null]

```

## Le bind

Je n'ai pas donnée le code du bind qui se résume à 

``` php
    public function bind(callable $transformation)
    {
        return self::of($this->concat($this->map($transformation)));
    }
```

Je vais essayer de justifier tout cela.

Partons d'abord du principe que `$this->concat` n'existe pas..

Donc mon `bind` devient

``` php
    public function bind(callable $transformation)
    {
        return self::of($this->map($transformation));
    }
```

Un exemple 
``` php
function addOne($input) {
	return Collection::of($input+1);
}

$result = Collection::of(array(1,2,3))->bind(addOne);

var_dump($result);
```

Le résultat
``` php 
class Collection#6 (1) {
  protected $value =>
  array(1) {
    [0] =>
    class Collection#5 (1) {
      protected $value =>
      array(3) {
        [0] =>
        class Collection#2 (1) {
          ...
        }
        [1] =>
        class Collection#3 (1) {
          ...
        }
        [2] =>
        class Collection#4 (1) {
          ...
        }
      }
    }
  }
}

```

Nous avons une collection qui contient une collection (double container!!) et pire dans chaque valeur est encore une collection !. On perd aussi le chainage.

Bref nous avons tout perdu.

### Solution le marteau.

{% img center /images/marteau.png 515 150 'Le marteau comme solution.' 'Le marteau comme solution' %}

Nous allons aplatir le résultat.

C'est a dire que nous allons transformer notre collection `[[a],[b],[c]]` en `[a, b, c]`

Voici l'implémentation en code.. C'est un peu long n'hésitez pas à sauter cette partie..

Partons du principe que c'est un array..

On aplati notre liste ainsi

``` php
$flatten = array(array("a","d"), array("b"), array("c"));
$result;
foreach($flatten as $value) {
    foreach($value as $subvalue) {
        $result[] = $subvalue;
    }
}
var_dump($result); //array("a", "d", "b", "c");
```

Le problème est que notre collection n'est pas un `Array`.. Mais essayons avec une fonction un peu plus tordue

``` php
$result = array_reduce(
    $flatten,
    function ($acc, $value){
    array_reduce($value, function($idontcare, $value) use(&$acc) {
        $acc[] = $value;
    });
    return $acc;
}, []);
```

C'est un façon un peu plus complexe d'exprimer la même chose que le code plus haut. Sans utiliser les boucles `foreach`. 
 
Le reduce pour notre collection est facilement exprimable.

``` php
    // dans la classe Collection
     public function reduce(callable $function, $accumulator)
    {
        foreach ($this->value as $item) {
            $accumulator = call_user_func($function, $accumulator, $item);
        }
        return $accumulator;
    }
   
```

Reprenons le code du `array_reduce` et utilisons notre `reduce`

``` php
 // dans la classe Collection
    private function concat(Collection $collection)
    {
        return $collection->reduce(function ($agg, $value) {
            $value = ($value instanceof Collection) ? $value : Collection::of($value);
            return $value->reduce(function($agg, $v) {
                $agg[] = $v;
                return $agg;
            }, $agg);
        }, []);
    }
```

Voici comment on aplatit notre fonction et on sauvegarde le chainage. Mais il y a mieux..

## Si j'avais un marteau..

Montrons quelque exemples de bind.

### Exemple 1 : Les lapins.

Soit le fonction suivante

``` php 
function reproduction($input) {
       return Collection::of(array($input, $input, $input);
}
```

Un exemple 

```
$lapin = Collection::of(array("lapin"))
    ->bind("reproduction")
    ->bind("reproduction")
    ->extract();
``` 

Le résultat 
 
{% img center /images/reproductionlapin.png 594 482 'Un array dans le container' 'Un array dans le container' %}

* premier bind

``` php
["lapin"] -> map ->[["lapin", "lapin","lapin"]] -> concat -> ["lapin", "lapin","lapin"]
```

* second bind 

``` php
["lapin", "lapin","lapin"] -> map [["lapin","lapin","lapin"][..][..]] -> ["lapin" .. *9]
```

Nous commençons avec un lapin, nous multiplions par 3 à chaque interaction. Comme la liste est aplatie à chaque fois.

### Exemple 2 : les fractales

Soit la fonction suivante

``` php

function fractale($value) {
    if ($value == "#")
        return (Collection::of(array('#', '_', '#')));
    return Collection::of(array("_", "_","_"));
}
$result = Collection::of(array("#"))->bind("fractale")->bind("fractale")->bind("fractale")->extract();
echo implode($result);
//#_#___#_#_________#_#___#_#

```

### Exemple 3 : avec les chiffres

Soit la fonction suivante

La fonction inférieure à 20 renvoie un array vide.

``` php
function moiEtMonSuccesseur($input) {
    return Collection::of($input, $input+1); 
}

function inferieurA20($value) {
   if($value > 20) {
        return Collection::of([]);
   }
   return (Collection::of(array($value)));
}

$result = Collection::of([10,20,30])->bind("moiEtMonSuccesseur")->bind("inferieurA20")->bind("moiEtMonSuccesseur")->extract();

```

 * premier `bind`

``` php
[10,20, 30] -> map -> [[10,11],[20,21],[30,31]]->concat -> [10, 11, 20, 21, 30, 31]
```

 * second `bind` 

``` php
[10, 11, 20, 21, 30, 31] -> map -> [[10],[11],[20],[],[],[]]-> concat -> [10,11,20]
```
 * troisième `bind`

``` php
[10, 11, 20] -> map [[10,11], [11,12], [20, 21]]-> concat -> [10,11,11,12,20,21]
```

 

### Exemple 4: Trouver les positions possibles d'un jeux de société

``` php 
function donneTousLesCoupsPossibles($position) {
    //renvoie toutes les positions légales
    return Collection::of(array(position_possible..));
}

$postion1->bind(donneTousLesCoupsPossibles)
   ->bind(donneTousLesCoupsPossibles);
```
cette fonction donne toute les parties possibles dans deux coup.


## Conclusion
On comprend assez bien l'intérêt de cette monade pour gérer des listes, mais il y a une autre vision possible. La collection avec le bind est considérée comme une façon de gérer des entrées *non déterministes*. J'ai eu un peu de mal à comprendre, mais voici l'idée.
La valeur 3 n'a qu'une valeur qui est `3` facile, la valeur `[1, 2, 3]` est une représentation de la même valeur sauf qu'elle à trois états possible `1, 2, 3`. Grâce au `bind` je prend en compte tous les états possibles.

Pour résumer :

 * Le Maybe prend le cas ou la valeur est présente.
 * La liste permet de gérer le Non-determinisme. 

Il reste encore beaucoup de chose à parler. Nous avons parlé des functors(`map` ou `fmap`), des monades (`of` et `bind`) nous allons voir les applicatives..

# des liens.

 * Ma référence pour l'implémentation est [php-functional](https://github.com/widmogrod/php-functional).
 * La bible pour le haskell est [Learn You a Haskell for Great Good!](learnyouahaskell.com) Le livre est gratuit avec des jolis dessins. Enfin le fond et la forme sont vraiment bon.
 * Il existe en français !! [Apprendre Haskell vous fera le plus grand bien !](http://lyah.haskell.fr/)


 * Partie 1 : [Monade/Functor](/blog/2015/11/11/les-monades-en-php-cest-possible-dot/)
 * Partie 2 : [Le functor Maybe](/blog/2015/11/15/les-monades-suite-le-functor-maybe-dot/)
 * Partie 3 : [Le functor Maybe avec le Bind](/blog/2015/11/22/les-monades-3-le-maybe-suite/)
 * Partie 4 : [Les listes](/blog/2015/11/29/les-monades-les-listes/)
 * Interlude : [Les évaluations partielles](/blog/2015/12/06/les-monades-evaluation-partielle/)
 * Partie 5 : [Les applicatives](2015/12/20/les-monades-5-les-applicatives/)

