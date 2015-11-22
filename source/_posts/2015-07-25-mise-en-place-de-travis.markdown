---
layout: post
title: "Mise en place de Travis"
date: 2015-07-25 16:37:49 +0200
comments: true
categories: ["Travis", "php", "Docker", "github"] 
---

## Introduction

Après la création de la [librairie](/blog/2015/07/19/histogramme-et-ligne-de-commande/), la création et la publication du [package](blog/2015/07/24/creer-son-depot-sous-packagist/), je propose d'ajouter l'intégration continue avec Travis. Travis est gratuit pour les projets open-source. (L'url n'est d'ailleurs pas la même c'est travis-ci.org pour les projets publics, et travis-ci.com pour les projets privés)

<!--more-->

## L'intégration continue.

Il faut bien entendu s'inscrire sur Travis. On s'authentifie grâce à son identifiant github.

Nous allons rajouter le fichier `.travis.yml` dans notre dépôt.

``` yml
language: php
install: composer install
php:
  - 5.4
  - 5.5
  - 5.6
  - hhvm
  - nightly
```



Quand je synchronise mes dépôts. Il suffit de cliquer sur le slider pour activé l'intégration continue.

{% img center /images/travis_choice.png 600 234 'Activer l'intégration continue' 'Activée l'intégration continue' %}

On peux lire les logs, d'ailleurs on se rend compte que travis utilise Docker

{% img center /images/travis_log.png 600 405 'Log de travis' 'Log de travis' %}

et voici le résultat

{% img center /images/travis_depot.png 600 320 'Dashboard du projet' 'Dashboard du projet' %}

A chaque commit je lance un build. J'ai vraiment été très surpris par la simplicité de la mise en œuvre.

## En conclusion
On peux lancer un build sans passer par travis grâce à docker et [JoliCi](https://github.com/jolicode/JoliCi), Voir ce [post](blog/2015/04/18/dockers-et-ci/) à la fin

Dans le prochain article, nous allons parler de CodeSniffer.
