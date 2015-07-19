---
layout: post
title: "Histogramme et ligne de commande"
date: 2015-07-19 18:16:57 +0200
comments: true
categories: ["php", "bash", "linux"] 
---

## Un petit utilitaire.

J'ai reprogrammé en php un clone de [spark](https://github.com/holman/spark).

Un petit exemple est plus parlant

```
spark([1,2,3,4,4,5,6,1,2]); // ▂▃▅▆▆▇█▂▃
spark([1,2,3,4,5,1,2,3,4,5]); //▂▄▅▇█▂▄▅▇█
```

Voici l'implémentation

``` php
function spark($array) {
    $bars = array('▁','▂','▃','▄','▅','▆','▇','█');
    $divide = max($array);
    if ($divide == 0) {
        $divide = 1;
    }
    $countBars = count($bars)-1;
    $out = '';
    foreach ($array as $tick)
        $out .= $bars[round(($tick / $divide) * $countBars)];
    echo $out;
}
```

## L'appeler en ligne de commande.

La documentation de spark donne cette ligne de commande
```
spark 0 30 55 80 33 150
```

Est ce qu'on peux faire la même chose ? Il suffit de rajouter les deux lignes suivantes.
``` php
$iDontCare =array_shift($argv);
spark($argv);
return 0;
```

On utilise la variable `$argv` qui est le tableau de paramètres passer dans la ligne de commande. L'argument `$argv[0]`est le noms du programme, c'est pour cela que l'on fait un `array_shift` cela supprime la première valeur su tableau.

```
php spark.php 0 30 55 80 33 150
▁▂▄▅▃█
```

## L'appeler via les pipes

Un peu plus compliqué via les pipes du Shell.

Les implémentations des Pipes se présentent toujours un peu de la même manière. On utilise `STDIN`  qui représente l'entrée standard.

Un exemple qui traduit les caractères accentués. `é->&eacute`

``` php
#!/usr/bin/env php
<?php
while (!feof(STDIN)) {
     echo htmlentities(fgets(STDIN));
}
```

La ligne `!/usr/bin/env php` s'appelle le [shebang](https://fr.wikipedia.org/wiki/Shebang)

Puis rendre exécutable le fichier 
```
chmod +x htmlentities.php
```
Des exemples 
```
$ echo 'énergie' | ./htmlentities.php

&eacute;nergie

$ cat file.txt | ./htmlentities
```

Ce qui est cool c'est que l'on peux chainer les opérateur.

Un programme qui passe la première lettre en majuscule.

``` php
#!/usr/bin/env php
<?php
while (!feof(STDIN)) {
     echo ucfirst(trim(fgets(STDIN)));
}
```

Un programme qui aime crier !!!!.

``` php
#!/usr/bin/env php
<?php
while (!feof(STDIN)) {
     echo trim(fgets(STDIN)).'!!!!';
}
```

```
$ echo 'récuperation' | ./shoot.php | ./capitalize.php | ./htmlentities.php
R&eacute;cup&eacute;ration!!!!
```

C'est un peu plus compliqué dans la vrai vie avec les retours à la ligne vide. Mais j'espère que vous avez compris mon idée.

Retour à notre script.

Voici la partie pour récupérer de la ligne de commande.
``` php
// si je n'ai aucun argument ..
if (count($argv) == 0) {
    $str = '';
    // recupère le flux d'entrée
    while (!feof(STDIN)) {
        $str .= fgets(STDIN);
    }
    // explode laisse la derniere ligne vide.
    // d'ou le array_filter
    spark(array_filter(explode("\n", $str),'strlen'));
    return 0;
}
```

Essayons une commande sur le dépôt git du blog que vous lisez.
```
git shortlog -s | cut -f1 | php ~/prog/spark/spark.php
```
``` sh
$ git shortlog -s
(...)
   2  Manu
   37  Marc Camuzat
   1  Marcus Young
(..)
```

On ne garde que la colonne 1 avec `cut -f1` puis on passe au script php

On obtient
```
▁▁▁▁▁▁▁▁▁▁▁▁▃▁▁▁▁▁▁█▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▃▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▂▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▂▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁
```

## Conclusion

J'avais besoin pour un futur article (le prochain ?) de cette fonction.
La philosophie de Linux est de créer plein de petits programmes et que ceux-ci communiquent via une interface très simple et universelle qui est le fichier texte. ainsi aucune dépendance le programme 1 est en bash, le programme 2 est en C, le programme 3 est en PHP. et tout cela ne pose aucun problème.
