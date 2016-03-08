---
layout: post
title: "Installation d'un proxmox 4.0 : Nginx"
date: 2016-03-08 21:35:36 +0100
comments: true
categories: ["proxmox", "linux", "nginx", "virtualisation"] 
---

Dans le [post précédent](/blog/2016/02/28/installation-dun-proxmox-4-dot-0/), nous avons réglé le réseau pour que les instances récupèrent le réseau. Enfin nous avons crée une première instance que j'ai nommé `nginxproxy`.

Ce que je veux..

J'ai plusieurs sites et noms de domaine sur mon Proxmox. Je souhaite mettre chaque site dans un container. Ainsi tout est isolé, je peux mettre toute les versions que je veux. 

Par exemple, je souhaite 

 * blog.domaine1.fr partte vers l'instance 102 qui contient son propre apache.
 * image.domaine1.fr vers l'instance 103 etc ..

Nous allons utiliser  **Nginx** pour rediriger le trafic.
{% img center /images/nginx-logo.png 313 72 'logo de nginx' 'logo de nginx' %}


Je vais 

 * rediriger tout le trafic du port 80 du proxmox vers mon instance1 (que j'ai appellé nginxProxy)
 * installer Nginx en proxy sur cette VM. j'arrive sur la machine avec l'url blog.domaine1.fr, je renvoie vers la machine 102

<!--more-->

## Partie 1 rediriger le port 80

Il suffit de rajouter la ligne suivante dans mon fichier `iptables_start.sh` sur le **Proxmox**

```
# VM <numero de la vm> : nginx proxy/gateway
iptables -t nat -A PREROUTING -i vmbr0 -p tcp --dport 80 -j DNAT --to-destination 192.168.15.<ip de la vm>:80
```

Maintenant tout le trafic web se redirige vers le futur Nginx.

## Partie 2 installation de Nginx

Sur la vm précédemment crée.

```sh
sudo apt-get install nginx
```

Il faut rajouter la ligne suivante dans le fichier `/etc/nginx/nginx.conf`

```
      ##
        # Virtual Host Configs
        ##
        ## la ligne à rajouter ##
        include /etc/nginx/proxy_params;
        ## 
        include /etc/nginx/conf.d/*.conf;
        include /etc/nginx/sites-enabled/*;

```

Et rajouter ou créer (je ne me souviens plus ..)  le fichier `/etc/nginx/proxy_params`

```
proxy_set_header Host $http_host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto $scheme;
```

Enfin voici un exemple pour blog.domaine1.com

  - créer un fichier `/etc/nginx/sites-available/blog.domaine1.com.conf`
  - dans ce fichier recopier la conf suivante

```
server {
        server_name blog.domaine1.com.conf;

        location / {
            proxy_pass http://192.168.15.<ip de la vm>;
        }
}

```

Pas grand chose à rajouter la conf parle d'elle-même.

Il ne reste plus qu'a activer le site 
```
cd /etc/nginx/sites-enabled/
ln -s ../site-available/blog.domaine1.com.conf
```

Un Nginx restart..

```
service nginx restart
```

Il ne nous reste plus qu'a créer une VM pour héberger le blog.domaine1.com


## Conclusion

Tout cela marche bien mais je suis resté assez flou sur la façon de se connecter sur les VMs

Dans la partie 3 Je vais essayer de clarifier tout cela.. Je m'excuse pour les fautes d'orthographes. Et je vous remercie de m'avoir lu.

 * Partie 1 : [Proxmox : mise en place](/blog/2016/02/28/installation-dun-proxmox-4-dot-0/)
 * Partie 2 : [Proxmox : Nginx](/blog/2016/03/08/installation-dun-proxmox-4-dot-0-partie-2-nginx/)

