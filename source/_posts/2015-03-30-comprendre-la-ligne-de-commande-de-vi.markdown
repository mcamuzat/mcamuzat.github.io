---
layout: post
title: "Comprendre la ligne de commande de vi"
date: 2015-03-30 22:36:21 +0200
comments: true
categories: [Vim, Editeur, Linux]
---

#Comprendre la ligne de commande sous vim

sous Vi quand on appuie sur `:` on a la ligne de commande

tout le monde connaît 
```
:wq // quitter et enregistrer
:q! // quitter sans enregistrer
```

mais en pratique il existe plein de commandes.
par exemple : 

```
:1,10d 
```
efface la ligne 1 à 10 (`d` = delete)

```
:1,10m 10
```
bouge les lignes de 1 à 10 de 10 ligne (ici `m` = move)

le "pattern" est toujours le même
```
: (début, fin)action
```
| mouvements | traduction| 
| ------------- |:-------------:| 
| `1,10`      | entre la ligne 1 et  la ligne 1 à 10 |
| `.,10`      | `.` signifie la ligne actuelle      | 
| `10,$` | `$` signifie la dernière ligne|
| `/mot1/,/mot2/` | entre le `mot1` et le `mot2`|
| `., +5` | entre la ligne actuelle (`.`) et les 5 lignes suivantes|
| `%` | tout le fichier|

quelques actions

|racourcci| traduction|
| --- |---|
| `d` | comme **d**elete|
| `j` | comme **j**oin|
|`sort`| trier (sort) les lignes|
|`w`| pour enregistrer|
|`y`| comme yank|

##le plus connu substitute

vous avez souvent vu cette syntaxe dans les commits `s/mot1/mot2`

ici `s` signifie **substitute**.

par exemple 
```
:%s/mot1/mot2/g
```
va remplacer le mot 1 par le mot 2
le `g ` active le flag `global` et remplace si le mot apparait deux fois.

par exemple
```
$mot1 = $mot1 + 1;

// s/mot1/mot2 
$mot2 = $mot1 + 1 ; // on ne change que le premier mot

// s/mot1/mot2/g
$mot2 = $mot2 + 1 // tout les mots
```
##Encore un peu plus loin
la commande suivante permet de grouper les mots
```
:g/mot/ #donne toute les lignes contenant mot
```
`g` ici signifie **g**roup

On peut chaîner les differentes actions
par exemple 
```
:g/pattern/s/mot/mot2/g # toutes les lignes qui contiennent le pattern, remplace mot1 par mot2.

:g/pattern/d # efface toute les lignes qui contiennent le pattern suivant

:g/pattern/p # 'print toutes lignes qui contienne le pattern suivant
```

la derniere ligne est la plus connue. pattern est le plus souvent une *regex* donc la traduction `g/regex/p` ->donne la commande `grep` sous linux.

en faite, toutes les commandes que j'ai données proviennent de `sed`. mais ce n'est pas un hasard. `vi` est l'abbreviation de **V**isual **I**nteraction of Sed. un *sed interactif*.

J'espère que cela vous fera apprecier `sed` comme `vi`. on peut rester très longtemps sur toutes les commandes.

j'avais expliqué dans un précédent articles les mouvements en mode normal sont 
```
Action + Nombre de fois + Mouvement

exemple:
d5w # *d*elete *5* word
yG  # copier jusqu'à la fin du fichier (G)
=4j # indenter (=) 4 lignes vers le bas 
di( # efface entre les parenthèses *d*elete *i*nside ( 

dans le mode commande
: debut, fin action

:%d # efface tout le fichier
:1,10y # copie dans le presse-papier la ligne 1 à 10
:%s/include_one/require_once/gc

```

Il me reste à vous parler des buffers et des macros. et on aura presque fait le tour de la magie de `vi`.

##une commande de la vrai vie
```
:%s/\s\+$//
```
sur tout le fichier (`%`) remplace(`s`) un ou plus(`+`) espaces (`\s`) à la fin de la ligne (`$`) par du vide. cette commande supprime les espaces vides à la fin des lignes.. 

