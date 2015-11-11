---
layout: post
title: "Des commandes au top"
date: 2015-09-27 16:04:40 +0200
comments: true
categories: ["linux"] 
---

J'utilise souvent le programme htop. mais il y en a d'autre.

## atop

Plus austère. Beaucoup d'information sur toutes les ressources. C'est plus un outils d'audit. Le logicel donne toutes informations possibles. processeurs, disques, carte réseau. En pratique il peut même être lancer au démarrage. En pratique on parle de `sar` (**S**ystem **A**ctivity **R**eport). Il permet de surveiller la tailles des processus avec la colonne `VGROW` (*Virtual Memory Grow*) et `RGROW`(Resident memory Grow)
{% img center /images/atop.png 600 381 'atop' 'atop' %}

voir [sar](https://en.wikipedia.org/wiki/Sar_%28Unix%29)

## vtop

Un clone en Nodejs. voir le screenshot c'est vraiment très joli

{% img center /images/vtop.png 600 383 'vtop' 'vtop' %}

installation via npm
```sh
sudo npm install -g vtop
```

dépot [Github](https://github.com/MrRio/vtop)

<!--more-->
## Htop

### Quelques options

### filtrer par utilisateur

```sh
$ htop -umarc
```

ou `u` dans Htop

#### Sélectionner un process

Utilisez la barre d'espace pour sélectionner un process. Cela permet de le suivre.

 * `F7` ou  `F8` pour augmenter la priorité du process
 * `F9` ou `k` pour killer un process
 * `F5` affichage en arbre.
 * `a` pour assigner le process à un CPU.
 * `U` pour désélectionnér tous les process

#### Personnalisez l'affichage

La touche magique ici est `<F2>`. Vous pouvez personnaliser les deux colonnes avec les raccourcis claviers suivants. La colonne toutes à droite donne les widgets disponibles

 * `<F5>` ajouter le widget à la colonne de droite.
 * `<F6>` ajouter le widget à la colonne de gauche.

Sur une colonne vous pouvez sélectionniez le type d'affiche (texte simple, histogramme, etc..) via la touche `<F4>`

Voir le screenshot (`Text`, `Graph`,`Led`, `Bar`)
{% img center /images/typeaffichage.png 600 133 'les quatre types d'affichage' 'les quatre types d'affichage' %}

### Les codes couleurs

La touche `h` permet d'obtenir de l'aide, les raccourcis claviers et la significations des couleurs.

{% img center /images/codecouleur.png 600 104 'les différentes couleurs et leurs significations' 'les différentes couleurs et leurs significations' %}

## En conclusion

J'espère que vous appris un nouveau raccourci sur htop.
