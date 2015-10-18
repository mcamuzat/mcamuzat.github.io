---
layout: post
title: "Les fractales en php Mandelbrot"
date: 2015-10-18 20:02:28 +0200
comments: true
categories: [PHP] 
---


Voici le code : 

``` php
class Mandelbrot
{


    function Mandelbrot()
    {
        for ($x = -19; $x < 19; $x++) {
            echo("\n");
            for ($y = -19; $y < 19; $y++) {
                if (($out = $this->iterate($x/20.0,$y/20.0)) == 0)
                    echo("*");
                else
                    echo("_");

            }
        }
    }

    function iterate($x,$y)
    {
        $cr = $y-0.5;
        $ci = $x;
        $zi = 0.0;
        $zr = 0.0;
        $i = 0;
        while (true) {
            $i++;
            $zr2 = $zr * $zr;
            $zi2 = $zi * $zi;
            // Calul de la nouvelle valeur de z
            list($zr, $zi) = array(
                $zr2 - $zi2 + $cr,
                2 * ($zr * $zi) + $ci
            );
            // Si le module est supérieur à 2
            if ($zi2 + $zr2 > BAILOUT)
                return $i;
            // si cela fait la millieme boucle.
            if ($i > MAX_ITERATIONS)
                return 0;
        }

    }


}

$m = new Mandelbrot();
?>
    
```

Voici le résultat

``` 
______________________________________
______________________________________
___________________________*__________
_________________________****_________
_________________________****_________
_________________________****_________
______________________________________
___________________*__**********______
___________________**************_**__
___________________*****************__
__________________*****************___
_________________*******************__
________________*********************_
________________*********************_
______*__*_____**********************_
______*******__**********************_
_____*********_**********************_
_____*********_**********************_
___*_*********_*********************__
***********************************___
___*_*********_*********************__
_____*********_**********************_
_____*********_**********************_
______*******__**********************_
______*__*_____**********************_
________________*********************_
________________*********************_
_________________*******************__
__________________*****************___
___________________*****************__
___________________**************_**__
___________________*__**********______
______________________________________
_________________________****_________
_________________________****_________
_________________________****_________
___________________________*__________
_____________________________________
```

Comment cela marche.

Rappel sur les complexes


 * un nombre complexe est composé d'une partie réelle et une partie imaginaire : `a + i b` ici **a** est la partie réelle et **b** est la partie imaginaire
 * le module d'un nombre complexe représente la distance entre les coordonnées du point et le centre. `|module|^2 = a^2 + b ^2`
 * la multiplication d'un nombre complexe donne `(a + ib)^2 = (a^2-b^2)+2ab * i`
 
##La version simple 

il existe une video en anglais qui explique cela très bien.

<iframe width="560" height="315" src="https://www.youtube.com/embed/NGMRB4O922I" frameborder="0" allowfullscreen></iframe>

* Je crée un tableau (x,y) de 20 * 20 qui va de [1, -1] en largeur et en hauteur
 * j'effectue la boucle suivante. 
 - je calcule la valeur `z1` qui est égale à `z0^2 + c` avec c qui est `x+i*y`.
 - puis je calcule la valeur de `z2 = z1^2 + c` puis `z3`..
 - je quitte la boucle pour deux raisons. 
   * si le module est supérieur à 2, le module devient de plus en plus grand et dépasse 2. 
   * au bout de 1000 boucles la valeurs n'a toujours pas dépassé 2. Je renvoie 0

C'est le code de la fonction `iterate`. le php ne connaît pas les complexes(c'est natif en python..) donc le chiffre z est divisé en 2 `zr` la partie réelle et `zi` la partie imaginaire.

```
    function iterate($x,$y)
    {
        $cr = $y-0.5;
        $ci = $x;
        $zi = 0.0;
        $zr = 0.0;
        $i = 0;
        while (true) {
            $i++;
            $zr2 = $zr * $zr;
            $zi2 = $zi * $zi;
            // Calul de la nouvelle valeur de z
            list($zr, $zi) = array(
                $zr2 - $zi2 + $cr,
                2 * ($zr * $zi) + $ci
            );
            // Si le module est supérieur à 2
            if ($zi2 + $zr2 > BAILOUT)
                return $i;
            // si cela fait la millieme boucle.
            if ($i > MAX_ITERATIONS)
                return 0;
        }

    }


```

Si on compte le nombre d'étapes pour dépasser 2 on obtient le schéma suivant.

``` php
    function Mandelbrot()
    {
        for ($x = -19; $x < 19; $x++) {
            echo("\n");
            for ($y = -19; $y < 19; $y++) {
                if (($out = $this->iterate($x/20.0,$y/20.0)) == 0)
                    echo(" ");
                else
                    echo(chr(41+$out%16));

            }
        }
    }

```

Voici le résultat

``` 
--------------.......///1241410/....--
-------------.......///023.2520//....-
------------.......///053)1 ,*1///....
-----------.......//00128    ,20////..
----------......//0001130    ,2100///.
--------......//012222348    7432000,/
-------.....///017+644.*1865+1/73222)1
------....////00164 *4          .68)*)
-----..//////00024-              6  23
---..///////0002868                 .1
-../000///00011/23                 ,61
.//1611111111125.                   +3
//01)3326422224,                     8
//014*.67-753352                     )
//113) 2) 73757                      8
/0125,       )*                      )
1+34-         0                      6
26780         +                      2
48/ 6         )                     41
                                   621
48/ 6         )                     41
26780         +                      2
1+34-         0                      6
/0125,       )*                      )
//113) 2) 73757                      8
//014*.67-753352                     )
//01)3326422224,                     8
.//1611111111125.                   +3
-../000///00011/23                 ,61
---..///////0002868                 .1
-----..//////00024-              6  23
------....////00164 *4          .68)*)
-------.....///017+644.*1865+1/73222)1
--------......//012222348    7432000,/
----------......//0001130    ,2100///.
-----------.......//00128    ,20////..
------------.......///053)1 ,*1///....
-------------.......///023.2520//....-
```

C'est ce qui est assez amusant dans les fractales, les formules sont très simples. Mais le résultat est très impressionnant.

## Des liens

 * article [wikipédia](https://en.wikipedia.org/wiki/Mandelbrot_set)
 * les videos hypnotiques de [electric sheep](https://en.wikipedia.org/wiki/Electric_Sheep)
