---
layout: post
title: "Go hello server"
date: 2015-05-18 23:00:46 +0200
comments: true
categories: ["go", "php"] 
---


## Introduction

Je continue mon apprentissage en go. Nous allons essayer de faire un hello world via une page web.

L'exemple le plus simple de la [documentation officielle](https://gowalker.org/net/http#HandleFunc).

<!--more-->

## Mise en place

Je me place dans mon répertoire `go`

Je vais dans le répertoire `src/github.com/<username>/`

Je crée un répertoire `hello-server`

Dans ce répertoire je crée le fichier server.go

```go
package main

import (
	"io"
	"net/http"
	"log"
)

// hello world, the web server
func HelloServer(w http.ResponseWriter, req *http.Request) {
	io.WriteString(w, "hello, world!\n")
}

func main() {
	http.HandleFunc("/", HelloServer)
	err := http.ListenAndServe(":12345", nil)
	if err != nil {
		log.Fatal("ListenAndServe: ", err)
	}
}

```

Il suffit pour tester de lancer 

```
go run server.go
```

Ou sinon dans le répertoire `src/github.com/<username>/hello-server`

```
go install
```

Dans votre répertoire `bin` vous avez un exécutable `hello-server`

si on reprend notre répertoire `go` avec le précédent post

```bash

.
├── bin
│   ├── hello
│   └── hello-server
└── src
    └── github.com
        └── mcamuzat
            ├── hello
            │   ├── hello.go
            │   ├── LICENSE
            │   └── README.md
            └── hello-server
                └── server.go
```


Avec votre navigateur aller à l'adresse suivante http://localhost:12345/

### Micro analyse

```
	http.HandleFunc("/", HelloServer)
```

On initialise un router qui associe à la route `/` (la route vide) un *callback* `HelloServer` (qui ne fait qu'un simple `hello world!`)

```
	err := http.ListenAndServe(":12345", nil)

```

On demande à go d'écouter le port `:12345` 

Et il n'y a pas grand chose à dire d'autre. 

## En conclusion

Cela ressemble à beaucoup de micro-framework, en tant que développeur PHP je ne suis pas vraiment perdu.
Je vais essayer de continuer un peu sur le serveur web (récupération des posts).

L'avantage ici est que le fichier `hello-server` est exécutable et n'a besoin d'aucune dépendance (donc pas besoin de apache, PHP, etc..).




