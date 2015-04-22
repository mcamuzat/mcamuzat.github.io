---
layout: post
title: "Correction orthographique et VIM"
date: 2015-04-22 22:26:43 +0200
comments: true
categories: Vim 
---

J'ai assisté à un Meetup sur VIM.

Le speaker parlait de la correction orthographique sous VIM. Je savais que c'était possible, mais je ne m'en suis jamais servis. J'ai donc décidé de réessayer.. Et j'ai vu de façon différente mes posts sur ce blog. Je suis repasser un peu sur tout.

Voici la commande pour activer/installer la correction automatique

```
set spell spelllang=fr
```

Normalement Vim va télécharger pour vous les différents fichiers sur le Ftp officiel.  Il y a une commande interactive pour vous aider dans l'installation. Si jamais il a un souci le wiki français indique de se placer dans son répertoire `spell` (chez moi `~/.vim/spell`) et de lancer les commandes suivantes

``` sh
wget http://ftp.vim.org/vim/runtime/spell/fr.latin1.spl
wget http://ftp.vim.org/vim/runtime/spell/fr.latin1.sug
wget http://ftp.vim.org/vim/runtime/spell/fr.utf-8.spl
wget http://ftp.vim.org/vim/runtime/spell/fr.utf-8.sug
```

La touche magique ici est `z=` puis les touches du `0..n`. `]s` pour aller à l'erreur suivante `[s` pour l'erreur précédente. Il y a différentes touches pour ajouter à son propre dictionnaire. J'avoue que cela ne m'intéresse pas trop de suite. 

On peut aussi faire de l'auto complétion ou plutôt de l'auto correction pendant la saisie avec les touche `Ctrl-x` + `s`.

##Résumé
 
| Touche      | Description |
| ------------- |:-------------:| 
| `z=`               | auto correct si `spell` est activée|
| `[s`               | erreur précédente|
| `]s`               | erreur suivante|
| `Crtl-x s`      |auto complétion via le dictionnaire |


Warning : `Ctrl-s` parfois freeze le terminal (option pratique quand je regarde les tests fonctionnels passés). Pour *defreezer* le terminal c'est `Ctrl-Q`
 
##Conclusion:

Il manque le correcteur grammatical :-) 
