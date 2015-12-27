---
layout: post
title: "Haskell : Stack"
date: 2015-12-27 18:39:17 +0100
comments: true
categories: ["haskell", "stack", "docker"]
---

## Introduction
Je ne développe pas en [Haskell](https://www.haskell.org/) mais je regarde beaucoup. La série que j'écris sur la programmation fonctionnelle me force à regarder le langage de plus près. Le monde haskell s'est enrichi d'un nouvel outil **Stack**.

## Stack et Haskell

Stack est un logiciel qui gère l'installation/les packages/la création/le build/les tests d'un projet Haskell.

Il a plusieurs avantages:

 * Il a été pensé pour automatiser le build. 
 * Il a une ligne de commande sympathique que nous allons voir par la suite.
 * Il utilise un fichier `stack.yml` c'est l'équivalent d'un `composer.json` en php ou un `package.json` en node.
 * Tout est installé dans le `~/.stack` tout les programmes sont *isolés* et n'interfèrent pas avec les autres logiciels déjà pré installés. 

<!--more-->

## Mise en place.

Il suffit d'ajouter les clés/et le dépôt sous ubuntu :  [voir la documentation](http://docs.haskellstack.org/en/stable/README.html#how-to-install)
Une fois que le logiciel est installé

### Etape 1 : structure d'un projet.

``` 
$ stack new my-project
```

crée une arborescence toute faite

```
.
├── LICENSE
├── Setup.hs
├── app
│   └── Main.hs
├── my-project.cabal
├── src
│   └── Lib.hs
├── stack.yaml
└── test
    └── Spec.hs

    3 directories, 7 files
```

A noter qu'il existe des templates d'applications (Un peu comme [Yeoman](http://yeoman.io/))

Par exemple pour créer une application Yesod (Pour faire un serveur web) et Mysql 

```
$ stack new mon-projet yesod-mysql
```

Pour lister les différents templates
```
$ stack templates
chrisdone
...
...
yesod-minimal
yesod-mongo
yesod-mysql
yesod-postgres
yesod-postgres-fay
yesod-simple
yesod-sqlite
```

## Etape 2 : Installation des librairies


```
$ cd mon-projet
$ stack setup
``` 

Si Haskell n'est pas encore installé dans `~/.stack`, le logiciel s'occupe de tout, il installe aussi toutes les dépendances.

## Etape 3 : build et compilation

```
$ stack build
```
Le haskell est un language compilé. Le logiciel compile tout le projet.

## Etape 4 : Lancer le programme

Pour lancer le programme

```
$ stack exec mon-projet
```

Pour lancer les tests. Tout les projets viennents avec des tests
```
$ stack test

```

## Etape 5 : Installer le programme
un peu comme un `make install` 

```
$ stack install
```

## Etape 6 : Docker 

Docker c'est cool et pratique.

dans le `stack.yml`
```
...
...
image:
  container:
    # Image de base
    base: "fpco/ubuntu-with-libgmp:14.04"
    # Noms de l'iso.
    name: "mcamuzat/mon-projet"
    # Nom du programme à lancer 
    entrypoints:
      - mon-projet

```

Si l'image de base existe déjà

Alors
 
```
$ stack image container
``` 

Cela génère le container.

Il ne reste plus qu'à lancer le container
```
sudo docker run -t -i mcamuzat/mon-projet mon-projet
```

## Listes de commandes

 * `stack new nom-du-projet nom-du-template` crée un nouveau projet
 * `stack setup` mise en place du projet
 * `stack build` compile le projet
 * `stack exec nom-du-programme` lance le programme
 * `stack repl` Lance le mode interactif
 * `stack test` Lance les tests
 * `stack install` installe le programme.
 * `stack templates` liste tout les templates.

## Conclusion

Je me mets au Haskell. C'est assez passionnant. Le langage n'est pas simple (je me casse un peu les dents dessus). Je suis pour l'instant juste sur les premiers problèmes de [codewars](http://www.codewars.com/). J'espère faire des post sur haskell par la suite. Il y a déja plein de tutoriels.


## Liens
 * [Le site officiel](http://docs.haskellstack.org/en/stable/README.html)
 * [Une introduction en anglais](http://conscientiousprogrammer.com/blog/2015/11/30/haskell-tidbits-24-days-of-hackage-2015-day-1-introduction-and-stack/)
 * [Un second article an anglais](http://www.stephendiehl.com/posts/haskell_2016.html)
 * [Créer le container Docker](https://www.fpcomplete.com/blog/2015/11/kubernetes)


