---
layout: post
title: "Les Monades en PHP c'est possible.."
date: 2015-11-11 19:28:39 +0100
comments: true
categories: ["PHP", "Programmation Fonctionnelle", "Haskell", "Categorie"] 
---

## Introduction
Nous allons voir ensemble les monades. Nous allons voir la monade **Identity**. elle n'est pas très utile mais nécessaire si vous voulez comprendre la monade **Maybe** qui j'espère va changer votre facon de voir votre code mais ce sera dans le post suivant.

Les monades sont des structures de la programmation fonctionnelle. Très utilisé dans le language [Haskell](https://www.haskell.org/). En pratique Haskell serait quasi inutilisable sans cette structure. 

<!--more-->

Je ne sais pas trop les définir puisque il existe un nombre incalculable de définitions

 * C'est un triplet d'après [wikipédia en français](https://fr.wikipedia.org/wiki/Monade_%28informatique%29)
 * Une Interface, de l'injection de dépendances, Structure, Une base spatiale, Un [burrito](https://byorgey.wordpress.com/2009/01/12/abstraction-intuition-and-the-monad-tutorial-fallacy/)
 * Des catégories


Il existe une infinité de tutoriels dessus (Le site officiel de Haskell à un compteur [plutôt amusant](https://wiki.haskell.org/Monad_tutorials_timeline) pour quantifier l'avalanche de tuto), écris par les plus grands Douglas Crowford [Youtube](https://www.youtube.com/watch?v=b0EF0VTs9Dc) (La référence du Javascript). Donc probablement que mon explication ne sera pas forcément la meilleure. 

Pour comprendre les monades je vais vous parler de container (Rien à voir avec [docker](https://www.docker.com/), ni container de [Symfony](https://symfony.com/)).

## des valeurs sympas et pas sympa.

J'ai des valeurs sympas, et des valeurs **pas sympas**.

{% img center /images/sympa-passympa.png 600 233 'des valeurs sympas et pas sympas' 'des valeurs sympas et pas sympas' %}

Par **pas sympa**, j'entends toute les valeurs que je ne maitrise pas trop
par exemple

 - la variable n'est pas instanciée le fabuleux `Null`
 - le résultat n'est pas forcément le même. je lance la fonction deux fois, je n'ai pas le même résultat.
 - le résultat dépend d'autre chose, par exemple la lecture d'un fichier (le réseau est coupé, le disque dur est plein, etc ..) et peux entrainer des erreurs et des exceptions.
 - le résultat n'a pas forcement la même taille. je pense à un résultat de base de donnée, je peux avoir 0 lignes commes des milliards.
 - enfin le résultat utilise des ressources qui sont partagés avec d'autre programme.


## La solution le container

La solution : 
>> utiliser un container ou un emballage

{% img center /images/valeurdanscontainer.png 600 450 'Ma valeur dans un container' 'Ma valeur dans un container' %}

L'idée est simple, je mets en **quarantaine** ma valeur.

Ainsi je suis protégé des effets néfastes.

{% img center /images/valeurquicasse.png 600 450 'Si problème..' 'Si problème..' %}

Voici le début de l'implémentation
 
``` php
class Container {

   /**
     * @var mixed
     */
    protected $value;

    public function __construct($value)
    {
        $this->value = $value;
    }

    public static function of($value)
    {
        return new static($value);
    }
}

```

J'ai deux méthodes:  un constructeur, et une factory statique. 
deux possibilités 

``` php
$valueNotSecure = "Je ne suis pas sympa";
var_dump(new Container($valueNotSecure));
var_dump(Container::of($valueNotSecure));
```

``` php
class Container#1 (1) {
  protected $value =>
  string(20) "Je ne suis pas sympa"
}
class Container#1 (1) {
  protected $value =>
  string(20) "Je ne suis pas sympa"
}
```

Ma valeur est dans un container, la propriété est `protected`. Donc impossible à atteindre de l'extérieur, à priori on ne craint pas grand chose..

Mais voila mon container aussi sécure qu'il soit ne sert à rien. Puisque rien ne sort, mais rien de rentre..

## Un Sas de décontamination.

Je vais ajouter un sas de décontamination à ma structure via l'instruction `map` qui prend une fonction. Il applique la fonction à la valeur à l'intérieur. Il a une petite particularité. Il ne donne pas le résultat mais un nouveau container qui contient le résultat.

{% img center /images/containeravecsas.png 600 450 'J'ajoute un sas' 'J'ajoute un sas' %}

Soit la fonction suivante qui ajoute 1 à la valeur en entrée.

```
function addOne($value) {
    return $value + 1;
}
```

Regardons le dessin suivant:

{% img center /images/containeravecsasexemple.png 600 450 'Je place la fonction +1 dans le sas' 'Je place la fonction +1 dans le sas' %}

 * je crée un container qui contient la valeur "5".
 * je mets la fonction addmap dans le `map`. je fais le calcul. que je m'empresse de remettre dans un container tout neuf. 
 * j'ai un Container avec "6".

{% img center /images/containertoutneuf.png 600 450 'J'ajoute un sas' 'J'ajoute un sas' %}

Voici l'implémentation de `map` dans ma classe container.

``` php 
    public function map($function)
    {
        // call_user_func => $function($this->value)
        return static::of(call_user_func($function,$this->value));
    }

```

Et le code d'exemple.

``` php
$output = Container::of(5)
    ->map("addOne")
var_dump($output);

```
Et le résultat
``` php
class Container#2 (1) {
  protected $value =>
  int(6)
}

```


Quelques remarques

 * Comme le résultat n'est pas sure, Je remet le résultat dans un nouveau container. Je ne réutilise plus l'ancien container (puisque contaminé). Comme on ne peux changer le contenu, il est **immutable**

{% img center /images/containertoutneuf.png 600 450 'des valeurs sympas et pas sympas' 'des valeurs sympas et pas sympas' %}

 * Le container avec l'instruction `map` par définition **Chainable**.

{% img center /images/chainagecontainer.png 600 450 'J'ajoute un sas' 'J'ajoute un sas' %}


``` php
$output = Container::of(5)
    ->map("addOne")
    ->map("addOne")
    ->map("addOne")
    ->map("addOne")
var_dump($output);

//class Container#3 (1) {
//  protected $value =>
//  int(9)
//}

```

Bien sur il est parfaitement possible d'utiliser des callbacks

``` php
$output = Container::of(5)
    ->map("addOne")
    ->map(function($value) { return $value * 4;});
var_dump($output);


//class Container#3 (1) {
//  protected $value =>
//  int(24)
//}

```

Donc j'ai un Sas d'entrée qui me permet d'interagir avec ma valeur. Je n'ai toujours pas fais sortir la fonction.


## Une sortie.

C'est pas très spectaculaire, j'ajoute une fonction extract() qui n'est qu'un simple return

``` php 
    ...
    public function extract()
    {
        return $this->value;
    }
    ...

```

Exemple 

``` php
var_dump(
     Container::of("je suis tranquille")
         ->map(strtoupper)->extract()
);
 //string(18) JE SUIS TRANQUILLE
```


## Une application : Le décorateur de texte.

Nous allons utiliser la capacité de chainage de notre container pour faire un pseudo-décorateur.

soit les fonctions suivantes
``` php 
function h1($text) 

$output = Container::of("  la réponse est Non   ")
    ->map("trim")
    ->map("htmlentities")
    ->map("h1")
    ->map("body")
    ->map("html")
    ->extract();

echo($output);
```
Voici le fonctionnement

 * je supprime les espaces en trop avec [trim](http://php.net/manual/fr/function.trim.php)
 * Je code en Html [htmlentities](http://php.net/manual/fr/function.htmlentities.php)
 * j'encadre de "h1" puis "body" puis "html".

Le résultat

```html
<html><body><h1>la r&eacute;ponse est Non</h1></body></html>
```

En image 



## Conclusion

Si vous avez compris le container, vous pouvez le renommer en IdentityMonad. Car c'est exactement la même chose. Nous venons de croiser notre première monade. 

Dans le prochain post nous allons implémenter une nouvelle monade la Monade Maybe.


Elle nous permettra de refactoriser le code suivant

``` php
function getAbonnementByUserConnected() {
    $user = getUserConnected();
    // l'utilisateur est anonyme pas d'abonnement
    if (null === $user)  {
        return null;
    }
    // l'utilisateur n'a pas d'abonnement
    $abonnement = getAbonnementByUser($user);
    if (null === $abonnement) {
        return null;
    }

    return $abonnement;
}

function getPromotion() {
    $abo = getAbonnementByUserConnected();
    if (null === getAbonnementByUser()) {
        return new Promotion();
    }
    return $abo->getPromotion();
}
```

Pour devenir 
``` php
$promotion = Maybe::of("getUserConnected")
    ->map("getAbonnementByUser")
    ->map("getPromotion")
    ->orElse(new Promotion());
```

Je me suis lancé dans une tache bien compliqué mais passionnante. 

Je vous remercies de m'avoir lu..


