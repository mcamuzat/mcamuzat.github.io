---
layout: post
title: "Remettre au propre son dépot git"
date: 2016-02-14 17:03:58 +0100
comments: true
categories: [git] 
---

Lorsque l'on souhaite sauvegarder son travail. Il y a toujours des fichiers modifiés que l'on ne souhaite pas commiter.

{% img center /images/gitmenage.png 387 490 'avant et après' 'avant et après' %}

<!--more-->

## Pour éviter de tracker des fichiers.

**Règle N°1 : éviter `git add .`**

Personnellement j'utilise tig. (voir le [post](/blog/2015/10/04/tig-status/))

Solution bis :

```
git add -p 
```

## Pour reverter les fichiers modifiés
**Règle N°2 : éviter le `git reset --hard`**

La solution est un peu trop radicale. Comme le `git add .` vous allez le regretter un jour, car vous perdez tout votre travail. Ce que vous voulez c'est remettre certain fichier au propre et ce n'est pas forcement la bonne commande.

```
git checkout -- .
```

A noter que cela réverte tout vos fichiers, mais s'il faut filtrer.

```
git checkout -- /web/.
```

Bien entendu il existe le mode interactif

```
git checkout -p
```

## Reverter un commit.

**Attention si le commit est déja pushé. Vous serez obliger de faire un `push -f` donc on évite de faire cela sur les branches master, staging, develop**

Vous avez commiter mais le commit est pas bon.

Si c'est juste le message qui est faux
```
git commit --amend
```

Si un des fichiers n'est pas bon
```
git reset HEAD^ --soft
```

Vous remarquez que j'utilise `--soft` au lieu `--hard`. Le reset soft me remets avant que j'ai commité. Je ne perds pas mon travail, même mieux les fichiers sont déjà prêt à être commités.

## Supprimer les fichiers qui ne sont pas trackés
Vous avez un `export.sql`, `toto.txt`, `npm-debug.log`

*Attention à ne pas perdre du travail.*

```
git clean -f -d -x
git clean -fdx
```

Avec `-f` force **Obligatoire** `-d` pour directory(répertoire) `-x` virer les fichiers ignorés de git. 

Pour voir ce qui pourrait être effacé pendant la commande.

```
git clean -n
```

Enfin il existe aussi un mode interactif
```
git clean -i 
```
