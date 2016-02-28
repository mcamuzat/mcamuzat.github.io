---
layout: post
title: "Installation d'un proxmox 4.0"
date: 2016-02-28 16:47:39 +0100
comments: true
categories: ["proxmox", "linux", "LXC" , "Docker", "virtualisation"] 
---

## Introduction

{% img center /images/proxmox-logo.png 292 35 'logo de proxmox' 'logo de proxmox' %}

J'ai acheté un serveur dédié chez OVH. Au travail nous utilisons un [Proxmox](https://www.proxmox.com/en/proxmox-ve) En solution de virtualisation. Étant plutôt content du logiciel. J'ai tout naturellement installé Proxmox sur mon serveur dédié. J'en ai profité pour passer à la version 4.

Mais ...

Proxmox V4 utilise [LXC](https://help.ubuntu.com/community/LXC)  alors que la version précédente utilise [OpenVZ](https://openvz.org/Main_Page). Et tous les tutoriaux sont globalement sur les versions 3.

Je suis arrivé à installer le logiciel. Voici le résumé de la première partie

 * Installation du Proxmox
 * Reglage du Réseau
 * Création d'un première instance.

<!--more-->

## Installations de proxmox

J'ai acheté la machine déjà installée sur [OVH](https://www.ovh.com/fr/). 

Pas grand chose à dire dessus.

## Réglage du réseau
se connecter en ssh
```
ssh root@<ip_de_la_machine>
```
dans le `/etc/network/interfaces`
C'est un peu la ou j'ai eu beaucoup de mal. Par défaut il y a normalement déjà deux interfaces virtuelles (des bridges pour être plus précis) déjà installées. `vmbr0` et `vmbr1`.

 * **Ne touchez pas à `vmbr0`** 

Voici la configuration de mon serveur.

```
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

# The loopback network interface
auto lo
iface lo inet loopback

# for Routing
auto vmbr1
iface vmbr1 inet static
        #post-up /etc/pve/kvm-networking.sh
        address 192.168.15.20/24
        bridge_ports none
        bridge_stp off
        bridge_fd 0


# vmbr0: Bridging. Make sure to use only MAC adresses that were assigned to you.
auto vmbr0
iface vmbr0 inet static
      ... l'ip de la machine ..

iface vmbr0 inet6 static
      ... la même chose en ipv6..
```

Je n'ai rajouté que les lignes suivantes dans vmbr1
```
        address 192.168.15.20/24
        bridge_ports none
        bridge_stp off
        bridge_fd 0
```

Il faut aussi activer l'IP forwarding
pour ce faire il faut éditer le fichier `/etc/sysctl.conf`
```
/etc/sysctl.conf:
net.ipv4.ip_forward = 1
```

Il faut lancer le programme pour prendre en compte les modifications

```
sysctl -p /etc/sysctl.conf
```

Et redémarrer le service network

```
service network restart
```

## Mise en place de Iptable.

Voici le script dans le répertoire
`/root/iptables_start.sh`

```sh
#!/bin/bash

#vider table NAT
iptables -F
iptables -X
iptables -t nat -F
iptables -t nat -X
iptables -t mangle -F
iptables -t mangle -X
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT

# On laisse sortir les vm
iptables -t nat -A POSTROUTING -s 192.168.15.0/24 -o vmbr0 -j MASQUERADE
```

La première partie vide toute les règles de redirection.

La seconde partie va permettre au vm dont l'ip est entre 192.168.15.1 à 192.168.15.254 de sortir sur le réseau.


## Résumé
 * Nous avons réglé `vmbr1` avec une adresse en 192.168.15.20/24
 * Nous avons activé le forward d'Ip
 * Nous avons créer un script `iptables_start.sh` qui supprime les anciennes regles et qui permet au réseau 192.168.15.0/24 de récuperer le net.


## Créer sa Première instance.

Allons sur la dashboard de votre proxmox (voir mail OVH)

### Récupérer des Isos/templates
Normalement vous avez une icônes avec un disque dur avec normal(..) cliquer sur `content->menu Template`

{% img center /images/choixtemplate.png 600 407 'choix des isos templates' 'choix des isos templates' %}

Téléchargeons une Ubuntu.

### Créer un container 

Cliquer sur l'icone `create CT` en haut à droite.

#### Général
Normalement avoir l'image suivante.
{% img center /images/creationvmgeneral.png 600 379 'création nouveau container' 'création nouveau container' %}
Mettons le noms de l'instance `nginxproxy` et mettons un mots de passe. Normalement on vous propose `VM ID 101` si vous n'avez aucun container.

#### Template 
Mettre l'iso Ubuntu que vous venez de télécharger

#### Root Disk
8 giga c'est très bien

#### CPU
C'est ici que l'on regle le nombre de cpu

#### La mémoire
Vous pouvez rajouter de la mémoire ici (2Giga en Memoire, Swap 512)

#### Network

C'est ici que c'est un peu plus compliqué

{% img center /images/creationvmreseau.png 600 390 'réglage du réseau' 'réglage du réseau' %}
dans le bridge mettre **vmbr1**

dans l'ip c'est simple c'est `192.168.15.<numéro d'instance>/24`. si vous avez l'instance 101 alors l'ip est `192.168.15.101/24`

dans la gateway c'est `192.168.15.20` la même ip défini dans le `vmbr1`

#### Dns
Rien à faire..

Vous n'avez plus qu'a confirmer. Normalement en 15 secondes la vm est crée.

#### Finition.
Avant d'allumer la vm cliquer sur l'icône de la vm puis `Options` puis cliquez sur la ligne `Start at boot` puis `Edit` et `yes` si le proxmox redémarre, on relance la machine (ce qui n'est pas le cas par défaut)

#### Allumage
Vous pouvez lancer la vm cliquez sur start.

## Conclusion

Nous avons réglé le proxmox, installé la première instance. dans la partie 2 nous nous connecterons sur l'instance pour terminer les réglages. Nous mettrons en place aussi le nginx pour rediriger les urls vers les bonnes instances.

