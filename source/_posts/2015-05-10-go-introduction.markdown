---
layout: post
title: "Go Introduction"
date: 2015-05-10 18:21:31 +0200
comments: true
categories: ["go", "docker", "github", "introduction"]
---

## Introduction

J'ai regardé Docker. Docker est en Go ainsi que pas mal de projets en fait. J'ai donc décidé d'essayer.

Dans ce chapitre nous n'allons pas trop discuter du langage mais surtout mettre en place tout les outils.


<!--more-->

## Installation(linux)

* Nous allons télécharger les fichiers [ici](https://golang.org/dl/)

* On décompresse le fichier
```bash
sudo tar -C /usr/local -xzf go1.4.2.linux-amd64.tar.gz
```

Créer un répertoire go dans votre `/usr/local`

* enfin il faut l'ajouter à votre `$PATH` en modifiant le `.profile`

```
export PATH=$PATH:/usr/local/go/bin
```

* testons dans notre ligne de commande.

```
$ go version
go version go1.4.2 linux/amd64
```

## Hello world !

Ouvrons un fichier `hello.go`

``` go
package main

import "fmt"

func main() {
    fmt.Printf("hello, world\n")
}
```

Pour l'exécuter 
``` sh
go run hello.go
hello, world
```

Tout va bien ! Nous avons installé Go

## Organisation d'un projet

L'organisation d'un projet sous Go est fixe ! Comprendre qu'il faut un **workspace**

Nous allons ensemble créer le projet `Hello`

 * Créer un répertoire `go`

 * Assigner la variable d'environnement `$GOPATH`

```
 export GOPATH=$HOME/go
```

 * enfin rajouter le $GOPATH/bin dans le PATH
```
export PATH=$PATH:$GOPATH/bin
```

 * Nous voulons sauvegarder notre code quelques part. ici github!
```
mkdir -p $GOPATH/src/github.com/<votreusername>/hello
```

Votre `username` est votre namespace pour les packages (un peu comme java). 

* dans notre répertoire `src/github.com/<votreusername>/hello` nous allons copier notre `hello.go`

* Tout est en place. Il n'y a plus qu'a taper `go install github.com/user/hello`. 

* autre possibilité se rendre dans le répertoire `src/github.com/<votreusername>/hello`
``` bash
go install
```

* Nous pouvons vérifier que dans le répertoire `$HOME/go` il y a un dossier `bin/`
```
$GOPATH/bin/hello
hello world
# de manière plus simple puisque nous avons ajouter dans le path $GOPATH/bin
hello
hello world
```
voici la structure finale

```
.
├── bin
│   └── hello
└── src
    └── github.com
        └── mcamuzat
            └── hello
                ├── hello.go
                ├── LICENSE
                └── README.md
```

## Sauvegarde d'un projet

Nous allons sauvegarder celui-ci sous [Github](https://github.com/). Ce n'est pas obligatoire.
```
cd $GOPATH/src/github.com/user/hello
git init 
git add .
git commit -m"create project"
```

Sous github, j'ai crée un nouveau repository `hello-go` 

```
git remote add origin git@github.com:mcamuzat/hello-go.git
git pull --rebase
git push origin master
```

## Conclusion

Nous avons d'installer Go.

Je viens juste de m'y mettre, Je ne sais pas encore ce que la suite nous réserve..
