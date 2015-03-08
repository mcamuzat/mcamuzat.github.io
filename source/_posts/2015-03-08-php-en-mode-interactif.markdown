---
layout: post
title: "Php en mode intéractif"
date: 2015-03-08 19:24:57 +0100
comments: true
categories: PHP 
---

Je me baladai sous Github 

et je suis tombé sur ce dépot

https://github.com/borisrepl/boris

(en fait c'est un des dépots les plus actifs de ce mois pour Php)

c'est vraiment cool

Bien entendu il existe déja un mode interactif sous Php

``` sh
php -a
```

On peux le refaire en encore moins de ligne. 

Explication : La documentation parle de REPL. 
c'est l'abbreviation de **R**ead **E**val **P**rint **L**oop

et voici comment on peut l'implémenter (difficile de faire plus simple)
```
while (true) {
	echo eval(fgets(STDIN));
}
```
 avec les commentaires
```
while (true)/*Loop*/ {
	echo /*Print*/ eval(/*Read*/ fgets(STDIN));
}
```
Read Eval Print Loop. le nom donne le programme.

C'est une technique qui marche avec un peu tout les languages.
voir http://repl.it 
