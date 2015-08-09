---
layout: post
title: "Gmail Vim mêmes Raccourcis"
date: 2015-08-08 18:52:17 +0200
comments: true
categories: ["Gmail", "Vim"]
---

## Les raccourcis de Gmail ressemblent à vi.

*Résumé : appuyer sur `?` dans gmail pour avoir le tableau complet, les raccourcis claviers suivant ne sont pas activés par défaut*

Le titre est un peu exagéré.. Je vais essayer de montrer que la *philosophie* est un peu la même.

Mais sur Vi On utilise les flèches de direction `h`, `j`, `k`, `l` pour `gauche`, `bas`, `haut`, `droite` pour se déplacer (enfin surtout `j` et `k`). Et bien les touches `j`(bas)  et `k`(haut) marchent pareilles sous Gmail. 

Pour chercher sous Vi on utilise la touche `/` , essayez sous Gmail et vous aller directement dans la barre de recherche.

## Une lettre = une idée..

Comme dans vi ou `w` signifie **w**ord, `d` signifie **d**elete, `u` **u**ndo on retrouve la même idée.

Par exemple `c` est l'abréviation de **c**omposer.

 * `c` pour **c**ompose Nouveau message
 * `n` signifie **n**ext Message suivant 
 * `p` signifie **p**revious Message précédent.
 * `r` pour reply **r**eply Répondre
 * `a` pour tous **a**ll. Repondre à tous
 * `f` pour transférer **f**orward.
 * `o` pour ouvrir le conversation.

## La lettre en majuscule = la version minuscule en plus forte

Dans vi `i` insere du texte, `I` insere au début de la ligne, `a` ajoute du texte , `A` ajoute le texte à la fin de la ligne.

 * `C` compose un nouveau message dans une nouvelle fenêtre
 * `R` Répondre dans une nouvelle fenêtre
 * `A` Répondre à tous dans une nouvelle fenêtre. 
 * `F` Transférer dans une nouvelle fenêtre.

## Une lettre = un pictogramme.

Sous Vi certaine lettre sont des pictogrammes. Par exemple la touche `'` représente le marque page. 

Je n'ai encore jamais parlé des marques pages sous vi pour faire simple

 * Ouvrez un fichier texte très long
 * Allez vers le mileu du fichier  appuyer sur `m` puis `a`
 * Re-deplacez vous et appuyer sur `m` puis `b`.
 * tapez `'a` vous ramene ou point a et `'b` au point b, `d'a` (effacer jusqu'au marque-page a) 

{% img center /images/quote.png 431 174 'Le guillement est une pictogramme de marques-pages' 'le guillemet est le pictogramme du marque-page' %}

La lettre `=` est le pictogramme de deux lignes indentées. Quand j'appuie sur `=` j'indente le texte.

{% img center /images/indentation.png 600 276 'Indentation' 'Pour indenter on utilise la touche = ' %}

La lettre `z` correspond au repli de texte 

{% img center /images/repli.png 600 230 'z = repli de texte ;' 'Pour plier déplier le texte on utilise la touche z' %}

 * `z` + `c` pour replier (c = close)
 * `z` + `o` pour deplier (o = open)
 * `z` + `a` pour alterner.

{% img center /images/plier.png 595 214 'z = repli de texte' 'Pour plier déplier le texte on utilise la touche z' %}

Sous gmail, c'est encore la même idée.  la touche `x` correspond à une checkbox. appuyer sur x pour sélectionner la conversation. le `#` correspond à supprimer, le `!` à SPAM!!!.

{% img center /images/checkanddelete.png 543 193 'le x est le pictogramme d'un checkbox' 'Mon terminal` %}

## Le cas de `*`

Pour tout selectionner tout les fichiers textes on utilise la commande `*.txt`

 * `*` puis `a` : Sélectionner toutes les conversations
 * `*` puis `n` : Désélectionner toutes les conversations
 * `*` puis `r` : Sélectionner les conversations lues
 * `*` puis `u` : Sélectionner les conversations non lues
 * `*` puis `s` : Sélectionner les conversations dont le suivi est activé
 * `*` puis `t` : Sélectionner les conversations dont le suivi n’est pas activé

puis après:

 * `e` : Archiver
 * `m` : Ignorer la conversation
 * `!` : Signaler comme spam
 * `#` : Supprimer
 * `z` : Annuler la dernière action
 * `I` : Marquer comme lu
 * `U` : Marquer comme non lu
 * `+` ou `=` : Marquer comme important
 * `-` : Marquer comme non important


## Une lettre = Un verbe
la lettre g signifie **g**o 

* `g` puis `i` : Ouvrir la boîte de réception (**i**nput)
* `g` puis `s` : Ouvrir les conversations dont le suivi est activé
* `g` puis `t` : Ouvrir le dossier "Messages envoyés"
* `g` puis `d` : Ouvrir le dossier "Brouillons" (**d**raft)
* `g` puis `a` : Ouvrir le dossier "Tous les messages"
* `g` puis `c` : Ouvrir le dossier "Contacts"

## Conclusion

A mon travail, on utilise Gmail, Je recois beaucoup de mail (BitBucket, Hipchat, Slack, Redmine, Jira, Wordpress, Meetup, Ovh). J'avoue que des que j'ai décidé de traiter les mails via raccourcis clavier, j'ai trouvé cela assez pratique.

On peux retrouver un tableau qui résume tout en tapant "?" dans Gmail.

