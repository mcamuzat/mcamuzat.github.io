---
layout: post
title: "Diff et patch, pas besoin de git"
date: 2015-11-02 22:24:03 +0100
comments: true
categories: ["linux", "git", "mercurial"] 
---


Nous allons jouer un peu avec les diff et les patchs.

# Mise en place

Soit le fichier suivant `README.md`

```
Ceci est un exemple.


Ceci est une ligne ajoutée


```

Modifions le fichier par ceci.
```
Ceci est un exemple.







j'ai ajouté cette ligne
```

Enregistrons celui-ci en `README2.md`

Et lançons la commande suivante

```sh
diff README.md README2.md
```

Nous obtenons le résultat suivant

```diff
4d3
< Ceci est une ligne ajoutée
6a6,9
> 
> 
> 
> j'ai ajouté cette ligne
```

Il y a en fait plusieurs formats

Essayez la commande suivante

```sh
diff -u README.md README2.md > readme.diff
```

On obtient le fichier suivant
```diff
--- README.md   2015-11-02 22:07:43.728854981 +0100
+++ README2.md  2015-11-02 22:13:58.244839112 +0100
@@ -1,6 +1,9 @@
 Ceci est un exemple.
 
 
-Ceci est une ligne ajoutée
 
 
+
+
+
+j'ai ajouté cette ligne
```

<!--more-->
Cette vision doit vous être assez familière si vous faite du git, svn ou mercurial

c'est la sortie d'un `git diff` si on avait modifié ce fichier.

## Jouer un patch
Pour ce convaincre nous allons demander à linux de jouer le fichier `.diff` qui est tout simplement un patch

Grâce à la commande suivante.

```sh
$ patch -p0 < README.diff 
patching file README.md
```

Comme toute modification je peux avoir des conflits. Si je modifie mon `README.md`
```
Ceci est un exemple.

Ceci est une autre ligne
Ceci est une ligne ajoutée
Mais celle ci aussi

```

```
patching file README.md
Hunk #1 FAILED at 1.
1 out of 1 hunk FAILED -- saving rejects to file README.md.rej
```

Vous retrouvez les fameux `.orig` et `.rej` qui pourrissent un peu la vie de ceux qui font du mercurial ou git (`hg purge` et `git clean` est probablement ce que vous cherchez). 

## Merger 

Si vous voulez un merge. c'est possible
```
$ patch -p0 --merge < README.diff
```
le résultat
```
Ceci est un exemple.

<<<<<<<
Ceci est une autre ligne
Ceci est une ligne ajoutée
Mais celle ci aussi
=======
>>>>>>>





j'ai ajouté cette ligne

```
L'option `-p0` ou le plus souvent `-p1` demande à la commande `patch` d'ignorer le chemin sur un niveau.

Voici un exemple avec git et le même fichier

```diff
diff --git a/README.md b/README.md
index 1030f85..5e8e5f9 100644
--- a/README.md
+++ b/README.md
@@ -1,6 +1,9 @@
 Ceci est un exemple.
 
 
-Ceci est une ligne ajoutée
 
 
+
+
+
+j'ai ajouté cette ligne
```
on voit que la ligne `a/README.md` et `b/README.md`, ici pour appliquer le patch il faut utiliser `-p1` pour ignorer le premier niveau (supprime le `a/` et `b/`) 

Sous Git c'est plus simple

```bash
$ git apply lefichier.diff
```
## Reverter 

Enfin soyons complet il est possible de reverter un patch avec `-R`
```bash
patch -R -p1 < mon patch
```

Avec git 

```bash
git apply -R <mon fichier diff>
```

## Conclusion

Pas besoin d'avoir git/mercurial/svn pour créer ou jouer des patchs. 
