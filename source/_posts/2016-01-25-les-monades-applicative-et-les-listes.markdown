---
layout: post
title: "Les Monades: Applicative et les listes"
date: 2016-01-25 20:45:47 +0100
comments: true
categories:  ["PHP", "Programmation Fonctionnelle", "Haskell", "Categorie"] 

---

Dans le précédent [post](/blog/2015/12/20/les-monades-5-les-applicatives/), j'avais parlé des applicatives sur les Maybes.

Nous allons voir ensemble comment les applicatives se comportent sur les listes.

Rappelons que l'idée des applicatives c'est 

 * ma valeur est dans un container
 * et ma fonction que je vais appliquer est aussi dans un container

Pour la liste c'est un peu près la même idée. 

 * mes valeurs sont dans une collection
 * mes fonctions sont aussi dans une collection

<!--more-->

Initialisons une Collection de valeurs

``` php
$collectionValue = Monad\Collection::of([1, 2]);
```

Créons un collection de fonctions
``` php
$collection = Monad\Collection::of([
    function($a) {
        return 3 + $a;
    },
    function($a) {
        return 4 + $a;
    },
]);
```

Regardons de suite le résultat, nous obtenons une collection qui contient `[4, 5, 5, 6]`. En fait on a calculé toutes les possibilités.. Puisque `[1+3, 1+4, 2+3, 2+4]`

Essayons de programmer un générateur de nom de scout (?!!)

``` php
// des animaux
$collectionAnimaux = Monad\Collection::of([
  "renard",
  "blaireau",
  "aigle",
  "panda"
]);

// des adjectifs
$collectionAdjectif =  Monad\Collection::of([
  "affectueux",
  "perçant",
  "agile",
  "bavard"
]);

// des générateurs
$collectionGenerateur = Monad\Collection::of([
    Maybe\just(
        f\curryN(
            2, function($nom,$adj) {return $nom . " " . $adj;}
        )
        ),
    Maybe\just(
        f\curryN(
            2, function($nom,$adj) {return "petit ". $nom . " " . $adj;}
        )
    )

   ]
);

// On mélange
var_dump($collectionGerateur->ap($collectionAnimaux)->ap($collectionAdjectif));

```

Grâce à l'évaluation partielle je peux créer des fonctions à plusieurs arguments. Les applicatives sur les listes me permette de faire toutes les combinaisons.

J'obtiens
 
``` php
 .. 32 résultats
 string(14) "panda perçant"
    [14] =>
    string(11) "panda agile"
    [15] =>
    string(12) "panda bavard"
    [16] =>
    string(23) "petit renard affectueux"
    [17] =>
    string(21) "petit renard perçant"
    [18] =>
    string(18) "petit renard agile"
```

Bon c'est sur que *petit renard affectueux* n'est pas génial comme nom..

Le Panda bavard.

Liste des articles
 
 * Partie 1 : [Monade/Functor](/blog/2015/11/11/les-monades-en-php-cest-possible-dot/)
 * Partie 2 : [Le functor Maybe](/blog/2015/11/15/les-monades-suite-le-functor-maybe-dot/)
 * Partie 3 : [Le functor Maybe avec le Bind](/blog/2015/11/22/les-monades-3-le-maybe-suite/)
 * Partie 4 : [Les listes](/blog/2015/11/29/les-monades-les-listes/)
 * Interlude : [Les évaluations partielles](/blog/2015/12/06/les-monades-evaluation-partielle/)
 * Partie 5 : [Les applicatives](/blog/2015/12/20/les-monades-5-les-applicatives/)
 * Partie 6 : [Les applicatives et les listes](/blog/2016/01/25/les-monades-applicative-et-les-listes/) 

