---
layout: post
title: "Docker Je débute"
date: 2015-04-12 22:25:27 +0200
comments: true
categories: [linux, container, docker] 
---

## Docker 

Suite à une présentation à une conférence. J'ai commencé à m'y mettre. J'écris ce post en tant que grand débutant.. 

Docker est une solution de virtualisation d'instance, plus précisément de container. Il y a pas mal de différence avec les différentes visualisations de Virtual box/Xen/VmWare. On isole juste les process et le file-system. Ce qui fait que l'on consomme très peu de processeurs.

##Installation

Installer docker n'est pas très compliqué sous ubuntu 14.04.

``` bash
sudo apt-get install docker.io
```

Et c'est tout !

Pour éviter de préfixer `sudo` à chaque commande il est plus facile d'ajouter son utilisateur au group docker.

``` bash
sudo addgroup <votre user> docker
```

## Hello world sous docker


Lancer docker 

```
docker run ubuntu:14.04 /bin/echo 'Hello world'
```

Cette commande fait plusieurs choses:

* si l'image `ubuntu:14.04` n'existe pas, elle va la télécharger.
* Puis on lance le container
* Puis on exécute echo 'Hello world'

D'ailleurs si on relance la même commande, on constate que l'image est déjà sur le disque dur.

la commande prend moins d'une seconde. Pourtant on a chargé un container et lancer une commande !

Essayons la commande suivante
```
docker run ubuntu:14.04 - it /bin/bash 
root@b2634b81c3dc:/# 
```

Nous avons un bash intéractif. `-i` mode interactif, et `-t` affiche un pseudo terminal

Il faut comprendre que  dès que la commande principale est finie, l'instance aussi. Dans le cas `Echo 'Hello World'` la commande se finit de suite. Dans le cas de `/bin/bash` On spécifie le mode interactif. Donc l'instance continue tant que l'on a pas quitté le bash.

Ouvrons un nouveau terminal
```
docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
b2634b81c3dc        ubuntu:14.04        /bin/bash           43 seconds ago      Up 42 seconds                           sharp_archimedes
```
On voit que mon image est toujours en cours.

La commande docker ps affiche tout les containers allumés pour afficher tout les containers allumés pendant la session. C'est `dockers ps -a`

On peux inspecter une instance avec la commande 
```
docker inspect sharp_archimedes
docker inspect b2634b81c3dc
```
Cela renvoie un json.

On peux aussi lister les images disponibles via la commande

```
docker images
```

Vous voulez télécharger des images
```
docker pull centos
```

Vous cherchez une images particulières
```
docker search php56
```

La liste des images disponibles est disponible sur [Docker Hub](https://hub.docker.com/) . D'ailleurs Dockers est très couplé avec DockerHub. DockerHub est un le GitHub pour Docker. On peux très bien faire du Git sans GitHub. C'est la même chose pour docker. 

Docker et Git partage aussi la notion de commit et de push.

relancons notre instance
```
docker run -it ubuntu:14.04 /bin/bash
root@f5882f7f608d:/# 
```

Installons un paquet au hasard
```
sudo apt-get install vim
```

On quitte `Exit`

puis on commit 
``` sh
sudo docker commit f5882f7f608do marc/vim
4f177bd27a9ff0f6dc2a830403925b5360bfe0b93d476f7fc3231110e7f71b1c
```

Si je liste les images.
``` sh
docker images
```

je vois apparaitre mon `marc/vim`

Je peux ainsi réutiliser mon container ainsi
``` sh
sudo docker run -it marc/vim vim
```

On peux ainsi se créer ses propres containers. Mais c'est un peu laborieux. Docker utilise des `DockerFile` pour automatiser le process. Cela fera un prochain post

## Résumé des commandes

* `docker run -i -t ubuntu:14.10 /bin/bash` lance en mode interactif et un terminal avec la commande Bash.
* `docker run -i -t ubuntu:14.10 'hello world'`
* `docker ps` liste les containers en cours.
* `docker ps -a` liste tout les containers.
* `docker images` liste toutes les images.
* `docker pull centos` pour télécharger une image (tout les images officielles sont sur le [Hub](https://hub.docker.com/) 
* `docker inspect uuid` affiche les informations sur l'instance.
* `docker commit uuid name`pour commiter.

## Conclusion

Bon j'avoue que je débute depuis 2 jours.  Ce qui m'impressionne dans Docker c'est la vitesse (moins d'une seconde pour démarrer). C'est plutôt simple d'utilisation. La notion de commit a l'air sympa (Même si au fond c'est l'équivalent d'un snapshot sous vmware). J'espère pouvoir faire mon premier dépôt sous DockerHub bientôt.


 

