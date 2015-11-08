---
layout: post
title: "faire des jointures sur des fichiers csv"
date: 2015-11-08 20:27:16 +0100
comments: true
categories: ["Linux", "Bash"] 
---

## Introduction

Un problème que j'ai eu au travail.

Soit les deux fichiers suivants csv suivant

```
ID_1,Noms1,Personne1
ID_2,Noms2,Personne1
ID_2,Noms2,Personne2
ID_3,Noms3,Personne1
ID_5,Noms5,Personne5
```

```
SIRET1,Adresse1,Noms1Annuaire,ID_1
SIRET2,Adresse2,Noms2Annuaire,ID_2
SIRET3,Adresse3,Noms3Annuaire,ID_3
SIRET4,Adresse4,Noms4Annuaire,ID_4
```

Je souhaite faire un merge de ces deux csv. Comment faire ?

En solution 1 j'ai pensé.

 * Créer une table temporaire 
 * dumper les deux fichiers
 * faire un jointure sql
 * faire un export

Et puis je me suis souvenu de la commande `join`

Voici la commande que j'ai utilisé 

``` sh
join -11 -24 file1.csv file2.csv  -t,
```

Voici le détail de la commande

 * `-11` `-24` faire une jointure entre la colonne **1** du `file1.csv` et la colonne **4** du `file2.csv`
 * `-t,` définie le séparateur dans le fichier ici c'est un `csv` **C**omma **S**epared **V**alue, donc le séparateur est `,`

Voici le résultat
```
ID_1,Noms1,Personne1,SIRET1,Adresse1,Noms1Annuaire
ID_2,Noms2,Personne1,SIRET2,Adresse2,Noms2Annuaire
ID_2,Noms2,Personne2,SIRET2,Adresse2,Noms2Annuaire
ID_3,Noms3,Personne1,SIRET3,Adresse3,Noms3Annuaire
```

C'est une jointure classique `ID_5` n'apparait pas dans le résultats pas plus que `Adresse4`


## Faire un select

On précise les colonnes que l'on souhaite avec l'option `-o`

```
join -11 -24 file1.txt file2.txt  -t, -o1.2,2.1
```
`-o1.2,2.1` la colonne **2** du `file1.csv` et la colonne **1** du `file2.csv`

```
Noms1,SIRET1
Noms2,SIRET2
Noms2,SIRET2
Noms3,SIRET3
```

## Left-join et Right Join

C'est possible à émuler avec l'option `-a` et `-e` `-a` affiche le fichier **1** ou **2**  même s'il n'y a pas de jointure et  et `-e`  la valeur par défaut à l'affichage

deux possibilités



``` sh
# Left-join
join -11 -24 file1.txt file2.txt  -t, -o1.2,2.1,2.2 -a 1 -e NULL
```

``` 
Noms1,SIRET1,Adresse1
Noms2,SIRET2,Adresse2
Noms2,SIRET2,Adresse2
Noms3,SIRET3,Adresse3
Noms5,NULL,NULL

```

``` sh
# Right-join
join -11 -24 file1.txt file2.txt  -t, -o1.2,2.1,2.2 -a 2 -e NULL
```

```
Noms1,SIRET1,Adresse1
Noms2,SIRET2,Adresse2
Noms2,SIRET2,Adresse2
Noms3,SIRET3,Adresse3
NULL,SIRET4,Adresse4
```

## Conclusion

Si un jour vous avez besoin de merger deux fichiers sans forcement sortir un script complexe, penser à linux.

Pour le csv et PHP, c'est inclus dans l'instruction `fgetcsv` mais ce n'est pas la joie, la librairie suivante [thephpleague/csv](http://csv.thephpleague.com/) rend cela nettement plus agréable.


