---
layout: post
title: "Problème Np-Complet"
date: 2015-09-09 21:55:10 +0200
comments: true
categories: ["PHP", "Composer"] 
---


Aujourd'hui je vais parler des problèmes Np-complets avec le [xkcd](http://xkcd.com/) suivant. L'original est [ici](http://xkcd.com/287/)

{% img center /images/np_complete.png 600 388 'xkcd' 'xkcd' %}

On appelle ce genre de problème les NP-complets

Pour faire simple (Ce que j'en ai compris)

Les NP-problèmes sont des problèmes donc il est très facile de vérifier une solutions. Dans notre cas ici il suffit de commander 7 * 2.15 pour obtenir 15.05. Le problème a une solution. Mais il est quasiment impossible de trouver une méthode efficace pour déterminer la solution, voir même de savoir s'il y a une solutions.

Il suffit de regarder les images suivantes (pris d'un post de [stack exchange](http://cstheory.stackexchange.com/questions/5188/explain-p-np-problem-to-10-year-old))

* si on met les paquets les plus gros d'abord, les plus larges, avec un [planner](http://www.optaplanner.org/).

{% img center /images/backpack.png 600 450 'bin packing' 'slide1' %}

* A quel moment mettre le paquet `2 * 4 = 8`. suivant la forme la solution varie
{% img center /images/backpack2.png 600 450 'bin packing' 'slide2' %}

* Enfin impossible de savoir s'il y a une solution ici. Il y a priori la place, pourtant il n'y a pas de solution

{% img center /images/backpack3.png 600 450 'bin packing' 'slide3' %}

la source des images [ici](http://cstheory.stackexchange.com/posts/5206/revisions).

Il reste la force brute.

Voici ma version du xkcd en php

``` php
function findSolution($total, $list, $menu) {
    foreach($menu as $plat => $prix) {
        $result = array_merge($list, array($plat));
        if ($total-$prix == 0) {
            var_dump($result);
            return;
        }
        if ($total-$prix > 0) {
            $result = findSolution($total-$prix, $result, $menu);
        }
    }
}
$menu = array(
    "Mixed Fruit"=>215,
    "French Fries"=>275,
    "Side Salad"=>335,
    "Hot Wing"=>355,
    "Mozzarela Sticks"=>420,
    "Sampler Plate"=>580,
);
findSolution(1505, array(), $menu);
```

Cela affiche toute les solutions possibles.

Bien sur il y a des doublons. Il n'y a pas de différence entre `["Hot wing", "Hot wing", "Mixed Fruit", "Sampler Plate"]` et  `["Hot wing", "Hot wing", "Sampler Plate", "Mixed Fruit"]`

Pour supprimer les doublons à l'affichage. Je vais utiliser des globales.

```
$count = 0;
$allResult = array();
function findSolution($total, $list, $menu) {
    global $count;
    global $allResult;
    foreach($menu as $plat => $prix) {
        $count++;
        $result = array_merge($list, array($plat));

        if ($total-$prix == 0) {
            $sort = $result;
            sort($sort);
            $allResult[] = $sort;
            return $result;
        }
        if ($total-$prix > 0) {
            $result = findSolution($total-$prix,$result, $menu);

        }
    }
}
$menu = array(
    "Mixed Fruit"=>215,
    "French Fries"=>275,
    "Side Salad"=>335,
    "Hot Wing"=>355,
    "Mozzarela Sticks"=>420,
    "Sampler Plate"=>580
);
$solution = findSolution(1505, array(), $menu);

$result = array_map(
    "unserialize",
    array_unique(
        array_map("serialize", $allResult
    )
)
);
var_dump($result);
var_dump($count);
```

 * je trie le tableau de résultats. pour que tout mes doublons s'affiche pareil.
 * `array_unique` ne marche pas si les valeurs sont des *tableaux* (ce qui est la cas ici)
 * Je transforme mes tableaux en chaine de caractères grâce à la serialization avec le `array_map`
 * Alors `array_unique` vire les doublons 
 * Je deserialize avec à nouveau `array_map` et l'opération inverse.

``` php
array([resultat1], [resultat1],[resultat2])
// array_map("serialize", $result);
array("resultat1_serialisé","resultat1_serialisé","resultat2_serialisé");
// array_unique(..)
array("resultat1_serialise", "resultat2_serialise");
// array_map("unserialize", ..)
array([resultat1], [resultat2])
``` 

``` php
int(12040)
array(2) {
  [0] =>
  array(7) {
    [0] =>
    string(11) "Mixed Fruit"
    [1] =>
    string(11) "Mixed Fruit"
    [2] =>
    string(11) "Mixed Fruit"
    [3] =>
    string(11) "Mixed Fruit"
    [4] =>
    string(11) "Mixed Fruit"
    [5] =>
    string(11) "Mixed Fruit"
    [6] =>
    string(11) "Mixed Fruit"
  }
  [1] =>
  array(4) {
    [0] =>
    string(8) "Hot Wing"
    [1] =>
    string(8) "Hot Wing"
    [2] =>
    string(11) "Mixed Fruit"
    [3] =>
    string(13) "Sampler Plate"
  }
}
```
J'ai fait 12040 boucles pour obtenir toutes les solutions. On peut bien sur optimiser un peu l'algorithme, si la valeur 5.60 ne marche pas à la première boucle, il y a pas de chance pour quelle marche à la seconde boucle. Donc il faut supprimer des valeurs dans le tableau au fur et à mesure.


## Conclusion
La force brute n'est pas vraiment une solution, avec plus de valeurs on explose les possibilités.  Il existe plusieurs [approches](https://fr.wikipedia.org/wiki/Probl%C3%A8me_du_sac_%C3%A0_dos) qui tende vers des solutions.
 
En informatique, il y a des nombreux cas où ce problème intervient 

 * L'ordre optimal d'installation des logiciels (Probablement la partie la plus marrante de [composer](https://getcomposer.org/), mais elle est assez peu documentée)
 * Beaucoup de cryptages utilisent des problèmes NP-complets (factorisation de deux nombres premiers).
 * La dernière phrase du comics parle du [voyageur de commerce](https://fr.wikipedia.org/wiki/Probl%C3%A8me_du_voyageur_de_commerce), un problème célèbre.
 * Le sudoku, Les jeux [nintendos](http://arxiv.org/pdf/1203.1895v1.pdf) ?!
 * Enfin une [liste complète](https://fr.wikipedia.org/wiki/Liste_de_probl%C3%A8mes_NP-complets) de wikipedia


