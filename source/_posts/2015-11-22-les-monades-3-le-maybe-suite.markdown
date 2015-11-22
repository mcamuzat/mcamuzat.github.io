---
layout: post
title: "Les Monades 3 Le Maybe (suite)"
date: 2015-11-22 16:59:31 +0100
comments: true
categories: ["php", "haskell", "programmation fonctionnelle"] 
---

 * partie 1 : [Monade/Functor](blog/2015/11/11/les-monades-en-php-cest-possible-dot/)
 * partie 2 : [Le functor Maybe](blog/2015/11/15/les-monades-suite-le-functor-maybe-dot/)

Dans la partie de 3 : Nous allons utiliser le functor Maybe avec l'instruction `bind`.

Soit le tableau suivant.

``` php
$data = [
    ['id_article' => 1, 'titre' => 'titre1', 'meta' => ['images' => ['//first.jpg', '//second.jpg']]],
    ['id_article' => 2, 'titre' => 'titre2', 'meta' => ['images' => ['//third.jpg']]],
    ['id_article' => 3, 'titre' => 'titre3'],
];
```
nous voulons afficher une liste avec une titre et et une image.

Nous allons utilisez la fonction suivante

``` php 
function get($key)
{
    return function ($array) use ($key) {
        return isset($array[$key]) ? $array[$key] : null;
    };
}
```

Exemple d'utilisation.

``` php
$getTitre = get("titre");
foreach ($data as $line) {
  var_dump $getTitre($line");
}
```

Le résultat

``` sh 
string (6) "titre1"
string (6) "titre2"
string (6) "titre3"
```

Pour extraire les images utilisons notre Maybe
``` php
foreach ($data as $line) {
    maybeFromValue($line)->map(get("meta"))
       ->map(get("images"))
       ->map(get(0))
       ->getOrElse("noimage.png");
}

```
Le résultat

``` php
string(11) "//first.jpg"
string(11) "//third.jpg"
string(3) "no-image.png"
```

## Avec le `bind`

Ré-ecrivons pour utiliser le bind. (Nous utilisons l'idée que la fonction que j'injecte dans le container renvoie elle-mème un `Some` ou `Nothing`)

{% img center /images/functionretourneSome.png 600 450 'la fonction renvoie un maybe' 'la fonction renvoie un maybe' %}


``` php
function get($key)
{
    return function ($array) use ($key) {
        return isset($array[$key]) ? Some::of($array[$key]) : Nothing::of(null);
    };
}

```

La fonction devient.

``` php
foreach ($data as $line) {
   var_dump(maybeFromValue($line)->bind(get("meta"))
       ->bind(get("images"))
       ->bind(get(0))
       ->getOrElse("noimage.png"));
}
```

## En conclusion

 * Si j'utilise la fonction `map` (parfois on parle aussi de `fmap`) j'utilise le Maybe en tant que Functor.
 * Si j'utilise la fonction `bind` j'utilise le Maybe en tant que Monad.

Nous allons continuer notre voyage avec les listes dans le prochain post.
