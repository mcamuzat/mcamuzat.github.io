---
layout: post
title: "Docker et Dockerfile"
date: 2015-04-26 19:44:44 +0200
comments: true
categories: ["docker", "git", "dockerhub", "linux", "tig"]
 
---

##Introduction

Je continue dans mon exploration de Docker, aujourd'hui nous allons voir comment automatiser la création d'un container à l'aide d'un `Dockerfile`.

Nous allons créer un container pour [tig](http://jonas.nitro.dk/tig/).. C'est un interface git qui marche sous un terminal. Pour moi, c'est un magnifique outil de travail. Je m'en sers très souvent (surtout la vue de status (touche `S`) puis `u` pour ajouter, `!` pour reverter, `C` pour commit, `e` pour lancer mon éditeur (Bien entendu Vim)

Nous allons faire
 
* L'installation à la main
* Puis écrire le `Dockerfile` qui automatise la partie 1
* Optimiser un peu celui-ci en utilisant une autre distribution
* Faire des commit sous Github, puis sous DockerHub


## Création du container à la main
Je commence avec une Ubuntu que je lance en mode interactif.

``` sh
docker run -it ubuntu:14.10
```
Je mets à jour ma distribution
```
apt-get update
```

J'installe tig (il est dans les dépôts officiels)

``` sh
apt-get install -y tig
```

A cause du `apt-get update` Ubuntu a téléchargé toutes les sources des dépôts dans le répertoire `var/lib/apt/lists/` pour ne pas alourdir le container je vais effacer celui-ci
```
rm -rf /var/lib/apt/lists/*
```

Je lance `tig`
```
root@0a475b7fbed7:/# tig
tig: Not a git repository
```

Il n'y a pas de dépot git à la racine c'est normal.

je quitte mon container et je liste 

```
docker ps -a
CONTAINER ID        IMAGE                                         COMMAND                CREATED             STATUS                            PORTS                                                                         NAMES
0a475b7fbed7        ubuntu:14.10                                  "/bin/bash"            13 minutes ago      Exited (130) About a minute ago
```

Je vais le committer.
```
docker commit -m "add tig" -a "mcamuzat" admiring_yonath mcamuzat/tig:v1
```

Je vais le relancer avec la commande suivante.
```
docker run -t -i -v `pwd`:/project mcamuzat/tig:v1
```

En gros j'ai crée un lien symbolique qui pointe le répertoire courant de mon ordinateur vers le répertoire `project` du container.

je me place dans le répertoire project.
```
cd /project
```

et je lance tig
```
tig
```

Si dans le répertoire courant il y a un dépôt git. Normalement l'interface de Tig apparait.

Voila j'ai placé tig dans un container. 

## Automatisation via un DockerFile.

On se place dans un répertoire vide

Je crée un fichier `DockerFile` avec le contenu suivant

```
FROM ubuntu:14.10
MAINTAINER Marc Camuzat <marco@crans.org>
RUN apt-get update \
    && apt-get install -y mysql-client \
    && rm -rf /var/lib/apt/lists/*
WORKDIR /project
VOLUME  /project
ENTRYPOINT ["tig"]
```

`WORKDIR` et `VOLUME` indique à Docker que le répertoire par défault est `project` et que l'on lance tig

On va maintenant demander à docker de *builder* l'image à l'aide de la commande suivante

```
docker build -t mcamuzat/tig:v2 .
```

On attend un peu.. Et on relance 
```
docker run -t -i -v `pwd`:/project mcamuzat/tig:v2
```

C'est beaucoup plus rapide.

## Optimisons la taille.

Quand je liste mon image via la commande suivante

```
REPOSITORY              TAG                     IMAGE ID            CREATED             VIRTUAL SIZE
mcamuzat/tig            v2                      103a05c16a2b        3 minutes ago       234.6 MB
```

Mon container fait 234 méga ! C'est beaucoup pour un simple utilitaire. 
pour simplifier je vais utiliser une autre distribution [alpine-linux](https://www.alpinelinux.org/) (que je ne connaissais pas ..) et le dockerhub [suivant](https://registry.hub.docker.com/u/gliderlabs/alpine/) qui réduit la distribution à 5 méga !


Voici mon `DockerFile`

```
FROM gliderlabs/alpine:3.1
RUN apk --update add tig
WORKDIR /project
VOLUME  /project
ENTRYPOINT ["tig"]
```

Je relance un build.

```
docker build -t mcamuzat/tig:v3 .
```

Maintenant mon container ne fait plus que 24 Méga !

## Publions sous Github

Nous allons créer un nouveau dépôt avec un README.

Que je vais cloner.
```
git clone https://github.com/mcamuzat/tig-docker.git
```

Je vais ajouter mon DockerFile, Commiter et Pusher
```
git add Dockerfile
git commit -m"initial commit"
git push origin master
```

Et c'est tout.

Résultat [ici](https://github.com/mcamuzat/tig-docker)

## Publions sur DockerHub

Je pars du principe que vous avez un compte sur DockerHub.

* Cliquer sur le bouton `Add Repository->Automated Build`
* choisir Github.
* Puis On va vous demander de relier votre compte DockerHub à Github.
* DockerHub va vous demander quel projet vous souhaitez builder automatiquement. 

Résultat [ici](https://registry.hub.docker.com/u/mcamuzat/tig/) 

## Conclusion

Le Dockerfile sert à automatiser la création d'image. il est plus simple de stocker le `Dockerfile` que le container (puisque Dockerhub s'occupe de faire build)

J'ai crée mon premier dépôt. Ce n'est pas très compliqué. Dans un prochain article, je vais essayer d'expérimenter `docker compose`. 
