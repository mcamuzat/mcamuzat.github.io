---
layout: post
title: "Les Monades (suite): Le Functor Maybe.."
date: 2015-11-15 16:43:42 +0100
comments: true
categories: ["php", "programmation fonctionnelle", "haskell", "catégorie"]
---

{% img center /images/lesdeuxcontainerMaybe.png 512 313 'Il y a deux container' 'Il y a deux containers' %}

Nous avons vue dans le précédent [post](blog/2015/11/11/les-monades-en-php-cest-possible-dot/) un pseudo-container qui nous permet d'emballer nos valeurs. Nous allons muscler un peu notre container mais partons d'un exemple.

Je souhaite récupérer le mail du client "bob" ou afficher "pas de mail"

```php
function getMail($name) {
$mail = getUserByName($name)->getAddress()->getMail();
if (null === $mail) {
   return "pas de mail"; 
}
return $mail;
}
```

Facile non ?

<!--more-->
Si `getAdress()` renvoie null, Outch ...

```
PHP Fatal error: Call to a member function getMail() on a non-object..
```

L'utilisateur n'existe pas forcement et puis l'adresse est peut-être vide.. Une implémentation naïve

```php
function getMail($name) {
    $user = getUserByName($name);
    if ($user) {
        $address = $user->getAddress();
        if ($address) {
            //etc ...
            return $adresse->getMail();
        }
   }
   return "pas de mail";

}


```

Ce code vous le connaissez, vous l'avez probablement déjà écris, il y a moyen d'optimiser de faire plus propre.

## Deux containers pour le prix d'un.
Le Maybe à la rescousse..

Voici le Maybe en dessin.

{% img center /images/lesdeuxcontainerMaybe.png 512 313 'Il y a deux container' 'Il y a deux containers' %}

J'ai un container `Some` et un Container `Nothing`.

Le container `Nothing` est un container qui n'a aucune valeurs. La méthode `map` renvoie toujours un container `Nothing`.

```php
class Nothing extends Container{
    public function map($function)
    {
       return static::of(null);
    }
    public static function of($value)
    {
        return new static($value);
    }
    
    public function bind($transformation)
    {
        return static::of(null);
    }

    public function getOrElse($default)
    {
        return $default;
    }
}

```

Le container `Some` le résultats de `map` est un nouveau container `Some` s'il y a un résultat non-null sinon c'est un container `Nothing`.

```php 
class Some extends Container{
    public function map($function)
    {
        $result = $this->bind($function);
        if ($result === null) {
            return Nothing::of(null);
        }
        return static::of($result);
    }
    public static function of($value)
    {
        return new static($value);
    }

    public function bind($transformation)
    {
        return call_user_func($transformation, $this->value);
    }

    public function getOrElse($default)
    {
        return $this->value;
    }
}

```

Enfin j'ai besoin d'un helper 

```
function maybeFromValue($value) {
 if ($value === null) 
   return Nothing::of(null);
 return Some::of($value);
}

```

Notons que j'ai une méthode qui me permet de sortir avec une valeurs par défaut

Quelques exemples:

```php
echo maybeFromValue(null)->map("ucfirst")->getOrElse("non!!");
// non!!
echo maybeFromValue("oui!!")->map("ucfirst")->getOrElse("non!!");
// Oui!!
echo Some::of("oui")->map("ucfirst")
   ->map(function($value) {return null;})
   ->getOrElse("Non!!");
// Non !!
```

Nous pouvons simplifier notre problème 

En le refactorisant ainsi

```php
// example
// method("name") return function($obj) {return $obj->getName()};

function method($name)
{
    return function ($obj) use ($name) {
        return $obj->$name();
    };
}

$mail = maybeFromValue(getUserByName($name))
    ->map(method("getAddress")) // $value->getAdress()
    ->map(method("getMail"))
    ->getOrElse("pas de mail");
```

Quelques dessins
Le cas ou tout marche

{% img center /images/maybechainageok.png 516 260 'chainage tout va bien' 'chainage tout va bien' %}

Le cas ou `getUser()` renvoie null

{% img center /images/chainagepasok.png 516 216 'getAdress renvoie null, on prend la valeur par défault' 'getAdress renvoie null, on prend la valeur par défaults' %}


Sympa la refactorisation. On peux supprimer ainsi une partie de la logique (la plupart des if, les nulls ont tous disparus).

## Une librairie toute faite

Je vais parler de [php-option](https://github.com/schmittjoh/php-option). Si vous faite du [symfony2](https://symfony.com/) vous l'avez déja dans votre `/vendor` et vous ne le saviez pas.

La syntaxe est un peu près le même

Mais il y a plein de fonctionnalités

```php
$entity = $this->findSomeEntity()->getOrElse(new Entity());
$entity = $this->findSomeEntity()->getOrCall('createAnNewAddress');
$entity = $this->findSomeEntity()->getOrThrow(new \Exception('ha!!!!!'));
```

Il y a aussi des possibilité de chainer les réponses si pas de résultats
```php
$entity = $this->findSomeEntity()->orElse($this->findSomeOtherEntity())
            ->orElse($this->createEntity());
```

Nous n'utilisons que l'instruction `map` pour le moment. Donc nous n'utilisons pas le container en tant que monade mais plutôt en tant que functor. Nous verrons cela dans le troisième post.

## Conclusion

Je suis désolé si certain termes sont inexacts comme le container. Je ne suis pas un expert, mais j'admets bien volontiers mon erreur.

Si vous avez un code ou vous vérifiez tout le temps si les valeurs sont nulles. Il y a probablement moyen que cette structure vous aide.

Dans le prochain Post nous utiliserons le Maybe avec l'instruction `bind`.

 * Partie 1 : [Monade/Functor](/blog/2015/11/11/les-monades-en-php-cest-possible-dot/)
 * Partie 2 : [Le functor Maybe](/blog/2015/11/15/les-monades-suite-le-functor-maybe-dot/)
 * Partie 3 : [Le functor Maybe avec le Bind](/blog/2015/11/22/les-monades-3-le-maybe-suite/)
 * Partie 4 : [Les listes](/blog/2015/11/29/les-monades-les-listes/)
 * Interlude : [Les évaluations partielles](/blog/2015/12/06/les-monades-evaluation-partielle/)
 * Partie 5 : [Les applicatives](/blog/2015/12/20/les-monades-5-les-applicatives/)
 * Partie 6 : [Les applicatives et les listes](/blog/2016/01/25/les-monades-applicative-et-les-listes/) 

