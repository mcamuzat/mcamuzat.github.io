---
layout: post
title: "VIM : formater le JSON"
date: 2016-05-08 17:51:22 +0200
comments: true
categories: ["VIM", "Javascript", "Python"]
---

Aujourd'hui nous allons voir comment formater un json un peu crade pour le transformer en quelques choses de plus sympathique.

Soit le JsonSchema suivant
```javascript
{"$schema":"http://json-schema.org/draft-04/schema#","type":"object","properties":{"address":{"type":"object","properties":{"streetAddress":{"type":"string"},"city":{"type":"string"}},"required":["streetAddress","city"]},"phoneNumber":{"type":"array","items":{"type":"object","properties":{"location":{"type":"string"},"code":{"type":"integer"}},"required":["location","code"]}}},"required":["address","phoneNumber"]}
```
Pas de retour à la ligne.

Grâce à la commande suivante.
```
:%!python -m json.tool
```
Le fichier devient
```javascript
{
    "$schema": "http://json-schema.org/draft-04/schema#",
    "properties": {
        "address": {
            "properties": {
                "city": {
                    "type": "string"
                },
                "streetAddress": {
                    "type": "string"
                }
            },
            "required": [
                "streetAddress",
                "city"
            ],
            "type": "object"
        },
        "phoneNumber": {
            "items": {
                "properties": {
                    "code": {
                        "type": "integer"
                    },
                    "location": {
                        "type": "string"
                    }
                },
                "required": [
                    "location",
                    "code"
                ],
                "type": "object"
            },
            "type": "array"
        }
    },
    "required": [
        "address",
        "phoneNumber"
    ],
    "type": "object"
}
```

On utilise en pratique python pour réécrire le code.

Vous pouvez essayer
```
python -m json.tool <le fichier>.json
```
Ce qui est pratique est que python est installé par défaut sous Linux. Il existe aussi [jq](https://stedolan.github.io/jq/), mais il n'est pas par défaut.

