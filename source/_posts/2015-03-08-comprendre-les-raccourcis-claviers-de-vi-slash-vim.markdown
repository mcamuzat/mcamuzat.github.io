---
layout: post
title: "Comprendre les raccourcis claviers de vi/vim"
date: 2015-03-08 18:37:20 +0100
comments: true
categories: [Vim, Ide, Linux]
---


#Comprendre les raccourcis claviers Vim : la méthode des deux tableaux

Ajourd'hui nous allons essayer de comprendre les raccourcis de vi/vim. je vous demande de prendre de deux feuilles. Je vais essayer de vous monter comment se combinent les touches.

- sur la feuille 1 dessinez deux colonnes: touche description.
- Titre de la feuille 1 : Mouvement
- Tire de la feuille 2 : Action deux colonnes : touche et description.

lancez `vim` et ouvrez un fichier texte existant.

##Les mouvements :
`w` signifie **w**ord
appuyez sur `w` vous passez au mot suivant.

ajouter à votre tableau mouvement
w **w**ord  passe au mots suivant

`b` signifie **b**ack. vous allez au mot précédent

vous ajoutez a la feuille b **b**ack

`e` signifie **e**nd va à la fin du mot

###les directions
J'ai un peu peur de vous perdre ici.
dans le temps les clavier n'avait pas de touche de direction. donc les touche sont `h`, `j`, `k`, `l` pour repectivement `gauche`, `bas`, `haut`, `droite`

moyen mnemotechnique `j` va vers le bas, `k` va vers le haut. 

Vous pouvez utiliser les flèches. Mais un des avantage d'utiliser les `j` et `k` est que vos doigts ne quitte jamais le milieu du clavier.

vous avez maintenant 7 lignes à votre tableau

###Un quiz !
que se passe t'il si je tape `3w` ou `5j` ?

réponse je me déplace de `3 mots` ou `5 ligne vers le bas`
modifions notre tableau en rajoutant le (n).

###Se déplacer dans le fichier.
`gg` vous ramène au début du fichier (`g` pour **g**o)
`G` vous emmène à la fin du fichier
`50G` vous emmène à la ligne 50 (variante `:50` marche aussi)

vous rajoutez les 3 lignes dans votre tableau.

###Début de ligne, fin de ligne
si vous connaissez vos regex `^a` vous donnes tout les occurences qui commencent par `a` et `a$` qui finissent par `a`
`^` début du texte sur la ligne actuelle
`$` fin de la ligne
`0` colonne 0 (tout début de la ligne)

###A la recherche du mot perdu

pour chercher de vi on utilise la touche `/`, vous mettez le mot que vous souhaitez, appuyez sur `n` pour suivant ou `N` pour précédent. 

nous avons le tableau final

| Touche      | Description |
| ------------- |:-------------:| 
| `w`               | **w**ord  mot suivant|
| `(n)w`            | n **w**ord  (ex: `3w` 3 mots)|
| `b`      | **b**ack   mots précédent  | 
| `e` | **e**nd fin du mot|
| `h`,`j`, `k`,`l` | gauche, bas, haut, droite|
| `(n)j` | 3 fois bas|
| `gg` | début du fichier|
| `G` | fin du fichier|
|`20G`| ligne 20|
|`^`|Début de ligne|
|`$`| fin de la ligne|
| `/mot`| cherche `mot`. `n` pour suivant `N` pour précédent| 

Nous avons la feuille 1.

###Un demi conclusion :  

- Il existe plus de mouvement. 
- mais le but ici n'est pas d'etre exhaustif. J'ai besoin des mouvements pour introduire la feuille suivante.

##Les Actions.

`d` signifie **d**elete (en fait c'est un couper)

Petit quizz : que se passe t'il si j'appuie sur les touches suivantes ? `d3w`
une traduction **d**elete **3** **w**ords. j'efface trois mots. vous comprenez pourquoi j'ai absolument besoin de la table d'avant. car en fait et c'est une des vérités qui vous aidera dans Vi/Vim. Un combinaison dans Vi c'est **action** + **mouvement**.

Je donne les autres actions

* `y`pour **y**ank pour copier `yw` copie un mot. `y3j` copie 3 lignes. pour coller on utilise la touche `p` comme **p**aste
* `c` pour **c**hange. vous souhaiter changer un mot. avant vous appuyer sur `i` pour vous effacer le mot, puis vous rajouter le votre. si vous taper `cw` comme **c**hange **w**ord. vi supprime le mot et met directement en mode *insertion*.
* `>` et `<` deplace à droite et a gauche le texte `<4j` deplace 4 ligne à gauche.
* `=` re-indente le texte, c'est un pictogramme le deux lignes du égale sont alignées. pour réindenter tout le texte `gg=G` soit `gg` va au début du fichier `=G` réindente jusqu'a la fin du fichier.

Table 2

|Touche      | Description|
| ------------- |:-------------:|
| `d`               | **d**elete|
| `c`               | **c**hange efface et passe en mode insertion|
| `y`            |  **y**ank (copier) `p` pour **p**aste coller|
| `>`      | décale le texte à droite | 
| `<` | décale à droite|
| `=`| reindente le code|
| `v` | **v**isual selectionne le texte  `y` copie `d` coupe etc ..|

#Conclusion
* Pour faire une combinaison de touche il suffit de prendre une lettre de la table 2 + un mouvement de la table 1. 
par exemple je veux effacer 5 lignes: 
`delete 5 lines` -> d5j
je veux changer un mot
`change word` -> cw

* Recopier les deux tableaux. l'astuce est vraiment d'avoir la feuille sous les yeux. 
* Les touches sont les mêmes pour `man` et surtout `less`

#Un peu de philosophie.

 - Je n'ai pas utilisée une seul fois la touche controle et alt.
 - En 1 sens, je n'utilise pas la souris. Je veux indenter le texte je dis *indente le texte* `=G` et pas selectionner le texte, puis boutton droit ou un raccourci clavier. En un sens Vi est plus direct. 
- J'espère que cela dé-mystifie un peu l'utilisation du clavier sur vi

##Teaser
il existe d'autre mouvement ! je ferai une version deluxe, l'idée ici est de comprendre la combinaison de mouvements. une touche apprise c'est de dizaine de nouvelles possibilités
par exemple `di(` `i` signifie inside. donc delete inside paraenthèse. efface le texte dans une parenthese. 

##Faq
j'appris que pour effacer une ligne c'est `dd` ? je ne le vois pas dans le tableau.
en fait 

* `dd` efface une ligne.
* `yy` copie la ligne.
* `cc` change la ligne.

Il existe un mouvement qui s'appelle `_` qui represente la ligne actuelle (c'est d'ailleurs le pictogramme d'une ligne)
donc en fait si on tape `d_` on efface la ligne actuelle (essayez !). mais la plupart du temps c'est un peu compliqué à taper pour une operation plutot courante (supprimer une ligne) . donc il a été décidé que `dd`, `yy` , `cc`
 sont les raccourcis de `d_`, `y_` et `c_`.

Il y a en fait une multitude de racourcis
par exemple:
* `c$` donne `C`(Change tout la ligne à partir du curseur).
* `d$` donne `D`(Efface toute la ligne à partir du Curseur).
* `^i` donne `I` insera au début de la ligne.
* `$a` donne `A` ajoute à la fin de la ligne.


