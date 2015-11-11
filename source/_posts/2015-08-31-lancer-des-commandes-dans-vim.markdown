---
layout: post
title: "Lancer des commandes dans Vim"
date: 2015-08-31 23:49:17 +0200
comments: true
categories: [Vim, Linux, Bash, Php, Ruby, Python] 
---

Soit le fichier texte suivant:

``` 
 * Alpha
 * Foxtrot
 * Charlie
 * Delta
 * Echo 
 * Bravo
```

Dans VIM il suffit de taper.

``` 
:%sort ou :%!sort
```

Pour obtenir
```
 * Alpha
 * Bravo
 * Charlie
 * Delta
 * Echo 
 * Foxtrot
```
<!--more-->

On peux aussi lancer plein de commandes amusantes

```
 * Doublon
 * Pas unique
 * Doublon
 * 
 * ...
```

```
!sort | uniq -c | tr "[A-Z]" "[a-z]"
```

Pour ceux qui ne se rappelle plus trop les commandes de Bash

 * `sort` trie le texte
 * `uniq -c` prend toute les valeurs et les comptes c'est l'équivalent d'un `GROUP BY` en SQL
 * `tr` est l'abréviation de **tr**anspose je remplace les lettres en `[A-Z]` par leur équivalent en minuscule.

```
      1  * 
      1  * ...
      2  * doublon
      1  * pas unique
```

Si vous sélectionnez le texte avec `v` et que vous appuyer sur `:`

Alors vous devez voir la commande suivante
```
:'<,'>
```
et Ajoutez la commande que vous allez appliquer à la sélection. Par exemple `:'<,'>!sort`

Plus rigolo. On peux appeler des langages que l'on veut dans VIM 

``` php
<?php echo "bonjour";
```

Tapez `!!`
vous devriez voir apparaître
```
:.!
```
Compléter avec `:.!php`

votre texte va se remplacer
```
bonjour
```

Cela marche aussi avec python 

``` python
print "olleh"[::-1]
```

Avec le curseur sur la ligne, appuyer sur `!!` puis ajoutez `:.!python`

La ligne devient

``` 
hello
```

## Exécuter une commande Bash depuis VIM

La commande suivante

```
php app/console cache:clear --env=prod
```

Si vous voulez exécuter la commande mais ne pas modifiez la ligne.

```
:.w !bash
```

C'est un peu moins simple.

 * `:.` représente la ligne actuelle.
 * `w` représente une écriture
 * `!bash` via Bash.

La documentation de VIM `:help :w_c`

## en résumé

 * Si vous voulez appliquer votre commande sur tout le fichier `:%!commande`
 * S vous voulez juste la ligne `:.!commande` ou tapez `!!`.
 * Si vous voulez sur une sélection `v` ou `V` puis `:` vous deviez voir ceci `:'<,'>`, ajoutez la commande souhaitée.
