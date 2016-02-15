---
layout: post
title: "Insérer avec classe dans VIM"
date: 2016-02-07 19:20:36 +0100
comments: true
categories: ["vim", "linux"] 
---

*tl;dr : sous vi utilisez `a` au lieu de `i`*


Tout le monde utilise VI pour éditer ses fichiers de configs sous linux ou les fichiers sur un serveur distant (il y a aussi `nano` qui marche super bien). Moi personnellement je code aussi du php/js avec. Pour insérer du texte on utilise la touche `i` comme insérer puis `ESC` pour quitter le mode insertion. Je vais parler des autres touches pour insérer du texte.

{% img center /images/viminser.png 517 220 'Il y a 6 touches pour insérer' 'Il y a 6 touches pour insérer' %}

<!--more-->


## Insérer.

Le problème de la touche `i` c'est justement que l'on insère le texte. Le curseur n'est pas à la bonne position. On souhaite plutôt ajouter du texte après le curseur. Et c'est le principe de la touche `a` (comme **A**ppend ou **A**jouter).

Si c'est rajouter une ligne vide. La touche `o` comme **o**pen une nouvelle ligne.


### Un concept important dans VIM
**la lettre majuscule est la version plus _musclée_ que la minuscule**

 * `i` insère au **début du curseur**.
 * `I` insère au **début de la ligne**
 * `a` ajoute du **texte à la fin du curseur.**
 * `A` ajoute du **texte à la fin de la ligne**.

**la lettre majuscule est le contraire de la version minuscule**

 * `o` ouvre une ligne **après le curseur**
 * `O` ouvre une ligne **avant le curseur**


{% img center /images/viminser.png 517 220 'Il y a 6 touches pour insérer' 'Il y a 6 touches pour insérer' %}

En résumé 

 * Si vous appuyez sur `i` et `->` : utiliser `a`
 * Si vous voulez commenter une ligne `I` suivi de`//`
 * vous avez oublié une virgule à la fin de la ligne. alors `A,`
 * vous voulez rajouter une ligne au lieu de `i` et `<enter>` , la touche `o`. 

## Se répéter

Essayons de commenter les trois lignes.

```
instruction 1 <-mon curseur est à cette ligne.
instruction 2
instruction 3
```

J'appuie sur `I` puis `//` pour commenter

J'obtiens
```
//instruction 1
instruction 2
instruction 3
```

J'appuie sur `j` ou `bas`

```
//instruction 1
instruction 2 <-mon curseur
instruction 3
```

Si j'appuie sur la touche `.` je répète la dernière instruction. La touche `.` est probablement la touche la plus utile.

```
//instruction 1
//instruction 2 <-mon curseur
instruction 3
```

Et ainsi de suite..

Mais il y a beaucoup de manière sur VIM pour faire la même chose.

Par exemple sélectionnons le texte avec la touche `v` ou la souris( `set mouse=a`). puis appuyons sur `:`

Vous deviez voir 
```
'<,'>`
```
alors complétons la ligne par `'<,'>norm I//` et tout le texte sélectionné est commenté!
La commande précédente se lit sur la zone sélectionnée `'<,'>`  appuyez(`norm`)  sur `I` puis `\\`.

## Un dernier raccourci-clavier..

La touche `gi` vous emmène au dernier endroit ou vous avez inséré du texte et place directement en insertion.

En conclusion, Il n'y pas que le touche `i` dans Vi. En fait on se sert assez peu de cette touche.. C'est pourtant la plus connue..
