---
layout: post
title: "Cloud9 un workspace en ligne"
date: 2016-02-01 21:14:07 +0100
comments: true
categories: ["linux", "nodejs", "virtualisation"]
---

Je me suis inscris sur [FreecodeCamp](http://www.freecodecamp.com). C'est gratuit et annonce une formation du Bootstrap/Nodejs/React/D3/javascript. Bon au moment ou j'écris ces lignes D3 et React sont en *coming soon* (c'est dommage c'était surtout ca qui m'intéressait). L'idée est de former des développeur back et front pour aider des associations. L'idée me semble bonne.

{% img center /images/freecodecamp.png 600 217 'FreecodeCamp' 'FreeCodeCamp' %}


<!--more-->

Sur les projets backends pour éviter d'installer un linux, le site conseille de créer un compte sur [Cloud9](https://c9.io/).


{% img center /images/introcloud9.png 600 259 'page d\'accueil' 'page d\'accueil' %}

Je crée un nouveau workspace.

{% img center /images/choixcloud9.png 600 453 'Choix de l\'environnent de dev' 'choix de l'environnent de dev' %}

Je choisis nodejs.

{% img center /images/workspacecloud9.png 600 413 'le workspace' 'le workspace' %}

Sur cette image on voit l'explorateur de fichiers, l'éditeur et la ligne de commande. Nous sommes déjà dans un vm. Toutes les commandes Linux sont disponibles. `apt-get`, `npm` etc.. 


Dans l'onglet `windows>share`

{% img center /images/sharecloud9.png 456 142 'Url de l\'environnement de dev' 'Url de l\'environnement' %}

Ici on voit l'url de l'environment. Si j'allume le nodejs. mon application est disponible à 

```
https://<mon-env>-<monuser>.c9users.io/
```

à la création de l'environnement il est possible de choisir un dépôt.

{% img center /images/choixdepot.png 600 115 'choix en mettant le dépôt' 'choix en mettant le dépôt' %}

on précise par exemple..
```
https://github.com/johnstonbl01/clementinejs-fcc.git
```

Ainsi l'environnement est déjà prêt. 

## Conclusion

Cloud9 est gratuit et il n'y pas vraiment de raison de se priver.

Il n'y a qu'un environnement privé pour une licence gratuite. Mais c'est largement suffisant pour débuter.
 
