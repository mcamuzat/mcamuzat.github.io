---
layout: post
title: "Copier Coller dans Vim"
date: 2015-05-30 23:23:37 +0200
comments: true
categories: ["linux", "vim"] 
---


## Pour commencer : comment sélectionner sous vim

### solution n°1 : utiliser la souris
tapez :
```bash
set mouse=a
```
vous pouvez sélectionnez avec la souris. Pour copier appuyer sur `y` comme **y**ank

### solution n°2 : utiliser le mode visuel
avec la touche `v` ou `V` pour utiliser la ligne entière. Puis les flèches ou les [mouvements](/blog/2015/03/08/comprendre-les-raccourcis-claviers-de-vi-slash-vim/)


### solution n°3 : utiliser les touches mouvements

Quelques exemples:

 * `y3w` copier trois mots (**y**ank **3** words)
 * `yG` copier jusqu'à la fin du fichier (**y**ank fin
 * `y5j` copier 5 lignes vers le bas (**y** **5** lignes vers le bas `j`)
 * `yi(` pour copier le texte entre parenthèse (**y**ank **i**nside `(`)


Pour coller on utilise la touche `p` pour **p**aste ou `P` (colle avant le curseur)

<!--more-->

## Les presse-papiers sous vi ou les registres

La notion de presse-papier est appelle registre dans Vi.

Pour voir l'état des registres (et si il ne fallait retenir qu'une seule commande..)

```bash
:register ou :reg
```

Vous devez voir quelques choses dans le genre:

```
"" dernier texte )
"0 dernier texte copié
... les dix derniers textes copiés
"9 ..  
"a contenu du registre "a" (s'il existe)
...
"% noms du fichier
". dernier texte inséré
"/ dernier texte recherché
": derniere commande.
```

 * Pour coller le texte contenue dans le registre `a` il faut taper`"ap` pour le registre `"a` + `p` paste.
 *  Pour copier le texte dans le registre a c'est `"ay`
 * Avec les mouvements de vi `"ay3w` dans le registre a (`"a`) copier (`y` comme *yank*) 3 mots (3w pour 3 words).

Un registre intéressant le registre `+` ou le registre `*` les deux registres sont associés au clipboard de Linux ou celui de windows.

## Pour résumer

 * Pour voir les registres. Il suffit de taper `:register`.
 * Pour coller un registre c'est `"<nom du registre>p`.
 * Pour copier c'est `"<nom du registre>y(+mouvement)`.
 * Le registre `+` est le presse-papier de windows ou linux. Pour copier/coller du presse-papier il suffit de taper `"+p` et `"+y`.
 * On a 26 presse-papiers de `a` à `z` personnellement j'en ai rarement utilisé plus de deux registres en même temps.

Nous reparlerons des registres avec les macros dans un prochain post.


