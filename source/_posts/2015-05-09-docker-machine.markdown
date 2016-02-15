---
layout: post
title: "Docker-machine"
date: 2015-05-09 18:29:45 +0200
comments: true
categories: ["docker", "amazon", "linux", "virtual-box", "openstack", "digital-ocean"] 
---

## Introduction

Nous allons voir [docker-machine](https://docs.docker.com/machine/). Docker-machine permet de simplifier l'installation/gestion/déploiement de Docker. 


<!--more-->
## Installation

Tout d'abord il faut connaitre votre architecture `x86_64`ou `i386`. 

La commande classique est `uname -a`. 

Puis télécharger l'exécutable via `curl`

Sous linux.
```bash
curl -L https://github.com/docker/machine/releases/download/v0.2.0/docker-machine_linux-amd64 > docker-machine
sudo mv docker-machine /usr/local/bin/docker-machine
```

Puis le marquer comme exécutable.
```bash
sudo chmod +x /usr/local/bin/docker-machine
```

Testons notre application. 
```bash
docker-machine -v
```

Enfin vous avez besoin d'avoir [Virtual-Box](https://www.virtualbox.org/wiki/Downloads)

## Mise en route.

Voici la commande pour tout lancer

```bash
docker-machine create --driver virtualbox dev
```

Cette ligne demande à docker-machine de créer une environnement que l'on appelle **dev** qui sera sur Virtual-box.
docker-machine va télécharger une iso (boot2docker) contenant docker. Et lancer Virtualbox. 

Jusqu'à maintenant on avait installé docker sur notre ordi local.
Ici on installe docker sur une VM.
Toutes les commandes seront passer de manière transparente à la machine virtuelle.
L'avantage de cette méthode est que tout le monde utilise la même iso virtuelle (boot2docker). Il n'y a moins le risque du "chez moi ça marche" qui est une remarque au combien rageante.

Je veux travailler sur mon environnement de dev

```
eval "$(docker-machine env dev)"
```

Toutes mes commandes sont directement envoyées sur la vm à distance
```
docker run busybox echo hello
```

Je peux rajouter un environnement (ici **prod**) 
```
docker-machine create --driver amazon prod --les options qui vont bien..
```

Il suffit de changer l'environnement pour automatiquement déployer sur Amazon.
```
eval "$(docker-machine env prod)"
```

Plein de drivers sont fournis: 

 * [Amazon Web Services](https://docs.docker.com/machine/#amazon-web-services)
 * [Digital Ocean](https://docs.docker.com/machine/#digital-ocean)
 * [Google Compute Engine](https://docs.docker.com/machine/#google-compute-engine)
 * [IBM Softlayer](https://docs.docker.com/machine/#ibm-softlayer)
 * [Microsoft Azure](https://docs.docker.com/machine/#microsoft-azure)
 * [Microsoft Hyper-V](https://docs.docker.com/machine/#microsoft-hyper-v)
 * [Openstack](https://docs.docker.com/machine/#openstack)
 * [Rackspace](https://docs.docker.com/machine/#rackspace)
 * [Oracle VirtualBox](https://docs.docker.com/machine/#oracle-virtualbox)
 * [VMware Fusion](https://docs.docker.com/machine/#vmware-fusion)
 * [VMware vCloud Air](https://docs.docker.com/machine/#vmware-vcloud-air)
 * [VMware vSphere](https://docs.docker.com/machine/#vmware-vsphere)
 
## L'avantage de docker-machine

* Simplifie l'installation. il n'y a que docker-machine à installer. Arès docker-machine s'occupe de tout installer. (il installe docker sur un vm/instance)
* Simplifie le déploiement, il suffit de changer l'environnement
* Enfin il s'interface avec docker-swarm (qui permet de gérer plusieurs nodes de Dockers).

## En conclusion

Le slogan "a way to get from zero to Docker" est plutôt juste.
 
 * Cela simplifie beaucoup l'installation sous windows (pas testé :-)).
 * Et harmonise les environnements de dev (tout le monde utilise la même iso)
 * Facilite le déploiement. Il n'y a pas a se soucier si c'est du Amazon/OpenStack/Azure.. 

Dans un prochain post nous allons essayer de voir docker-swarm.

