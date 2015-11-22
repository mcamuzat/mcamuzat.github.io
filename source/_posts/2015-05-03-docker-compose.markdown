---
layout: post
title: "Docker-compose"
date: 2015-05-03 21:33:31 +0200
comments: true
categories: ["docker", "docker-compose"] 
---


## Docker-compose

Dans le post précèdent sur docker, j'avais expliqué comment automatiser la création des containers grâce au `Dockerfile` aujourd'hui je vais expliquer `docker-compose`

<!--more-->

## Intro.

Dans un projet, il n'y a rarement qu'une seule instance. Voir il peut y avoir plusieurs fronts et une seule base de donnée. docker-compose est la justement pour organiser cela.

Par exemple le `docker-compose.yml` suivant:

``` yml
web:
  image: php:5.6-apache
  links:
    - db:db
  volumes:
    - .:/var/www/html

db:
  image: postgres
```

On déclare deux type de containers le container type **web** et le container de type **db**. à noter la ligne `links` qui fait que le container web partage/vois le container db

Il suffit de faire `docker-compose up` (comme un `vagrant up`)  pour automatiquement lancer deux containers. un container web avec une image php-5.6, et un container avec PostGresSQL


## Installation

Il existe deux façons de l'installer soit passer par `pip` (pip est l'équivalent de `npm` pour le python).

``` bash
sudo pip install -U docker-compose
```

Ou de passer par curl 

``` bash
curl -L https://github.com/docker/compose/releases/download/1.2.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

## Quelques commandes

Lancer les containers en mode démon (`-d`) 

```
docker-compose up -d
```
Cela crée deux containers

Que l'on peux surveiller grâce à la commande suivante
```
docker-compose ps 
    Name                   Command               State    Ports   
-----------------------------------------------------------------
compose_db_1    /docker-entrypoint.sh postgres   Up      5432/tcp 
compose_web_1   apache2-foreground               Up      80/tcp  
```

On peux voir les logs
```
docker-compose logs
```

Plus intéressant on peux rajouter des containers..

```
docker-compose scale web=3
Creating compose_web_2...
Creating compose_web_3...
Starting compose_web_2...
Starting compose_web_3...
```

Si je re-liste mes containers
```
docker-compose ps
    Name                   Command               State    Ports   
-----------------------------------------------------------------
compose_db_1    /docker-entrypoint.sh postgres   Up      5432/tcp 
compose_web_1   apache2-foreground               Up      80/tcp   
compose_web_2   apache2-foreground               Up      80/tcp   
compose_web_3   apache2-foreground               Up      80/tcp
```

Je viens de multiplier par 3 le nombre de container en une simple commande et sans stopper le système.

## Conclusion
Si vous n'avez qu'un seul container docker-compose ne sert pas à grand chose. Mais si vous avez plusieurs containers. Cela ne vaux pas la peine de se priver. Docker-compose contient en fait la liste des container ainsi que leurs configurations si on devait les lancers en la ligne de commande.

Dans le prochain post sur docker, je vais tenter `docker-machine`.

