---
layout: post
title: "des tests Behat plus rapides sous symfony"
date: 2015-07-06 19:46:43 +0200
comments: true
categories: ["php", "apache", "Behat","symfony"] 
---

## Introduction

Nous allons voir comment multiplier par 5 voir 10 les tests et l'environnement de test et de dev. L'astuce ici est de mettre le répertoire `cache` et `log` dans la Ram de l'ordinateur. Ainsi plus d'écriture sur le disque dur. C'est très pratique aussi pour les tests Behat. Attention à ne pas utiliser cette astuce sur une instance en production.

## Mise en Oeuvre

### Méthode 1 : modification du code

Il suffit de modifier le `AppKernel.php`

``` php
public function getCacheDir()
{
    if (in_array($this->environment, array('dev', 'test'))) {
        return '/run/shm/<MonProjet>/cache/' . $this->environment;
    }

    return parent::getCacheDir();
}

public function getLogDir()
{
    if (in_array($this->environment, array('dev', 'test'))) {
        return '/run/shm/<Monprojet>/logs';
    }

    return parent::getLogDir();
}

```

Il faut bien entendu créer les répertoires suivants. `/run/shm/<Monprojet>/logs` et `/run/shm/<Monprojet>/cache`.

Le problème de cette méthode est que l'on modifie le code. Et que ce genre de modification ne marche pas très bien avec les tests automatiques Jenkins. Bref pratique sur son poste mais pas en production.

### Fixer les problèmes de droit.

Suivant comme est configuré votre projet et votre apache. Il peux y avoir des problème de droit.

#### Solution a : mettre les permissions à l'utilisateur apache
```
chown -R www-data:www-data /run/shm/<MonProjet>
```

#### Solution b : mettre l'utilisateur actuel en utilisateur apache.

Normalement apache utilise l'utilisateur www-data, mais rien n'empêche de changer celui-ci par votre utilisateur. 

Il suffit de modifier votre `/etc/apache2/httpd.conf`

```
User <mon user> 
Group <mon user>

```
que je lance symfony de la console, ou du site c'est toujours le même utilisateur.

#### Solution c : Fixer juste pour le projet votre utilisateur.

La solution la plus simple à mettre en oeuvre est `mpm-itk`

```
sudo apt-get install apache2-mpm-itk
```

Dans mon fichier de conf `/etc/apache2/site-available/<monprojet>.conf`

J'ajoute les lignes suivantes 

```
    <IfModule mpm_itk_module>
    AssignUserId <mon user> <mon group>
    </IfModule>

```

Il faut redémarrer apache. Les plus courageux tenterons le fast-cgi et autre fpm

#### Un petit HS:

 * Vous pouvez créer un utilisateur par projet.
 * Et installer le projet dans /home/projet1
 * dans le /etc/apache2/site-available/projet1.conf

```
    <IfModule mpm_itk_module>
    AssignUserId <projet1> <projet1>
    </IfModule>

```

Si l'utilisateur *projet1* est compromis, il ne peux modifier que son home. Il y a plus de risque avec l'utilisateur apache `www-data` qui peux modifier l'ensemble de projet dans le /www/var. 



### Solution N°2 : Aucune modifaction du code.

Nous allons tout simplement supprimer les répertoires cache et logs du projet et remplacer par un lien symbolique.
```
rm -rf app/cache
ln -s /run/shm/<mon projet>/cache  app/cache
ln -s /run/shm/<mon projet>/logs  app/logs
```

Le code n'est pas modifié. Donc pas de commit bizarre et pas de problème en Prod. Le seul souci est qu'il y a souvent un `.gitkeep` sur `cache` et `log` donc les `git stash` et autre se comporte un peu bizarrement. 


### des chiffres

J'ai pris un projet de mon boulot voici la différence pour les tests Behat

``` 
Avant
49 scenarios (49 passed)
396 steps (396 passed)
5m12.80s (66.61Mb)

Après
49 scenarios (49 passed)
396 steps (396 passed)
0m23.74s (72.85Mb)
```
je suis passé de 5 minutes à 24 secondes. Les fixtures sur le SQLite sont plus rapides. Bref un gain de temps énorme.

## Conclusion
Nous avons vu deux solutions.
Un Post un peu bizarre, puisque c'est un collègue qui m'a montré l'astuce. J'avais envie d'écrire un post dessus. Merci à [lui](https://github.com/floyoops)

## Des liens

 * [le liens le plus cité](http://www.whitewashing.de/2013/08/19/speedup_symfony2_on_vagrant_boxes.html)
 * sur Npm-itk en [français](http://bibabox.fr/apache2-mpm-itk-utiliser-un-utiliser-un-utilisateur-different-pour-chaque-vhost/) ou en [anglais](http://blog.stuartherbert.com/php/2008/04/19/using-mpm-itk-to-secure-a-shared-server/) les deux sont biens.
