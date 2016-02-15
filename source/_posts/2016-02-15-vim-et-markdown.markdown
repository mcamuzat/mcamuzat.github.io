---
layout: post
title: "Vim et Markdown"
date: 2016-02-15 20:24:36 +0100
comments: true
categories: [Vim, Markdown] 
---

Une astuce que je viens d'apprendre. 

Pour écrire du code en Markdown on utilise la syntaxe suivante

```
```php
le code terminé par ```

```
C'est pas génial car on perd la coloration syntaxique, et j'ai fais pas mal d'erreurs.

Mais grâce à la commande suivante
```vim
let g:markdown_fenced_languages = ['html', 'vim', 'php', 'python', 'bash=sh']
```

Voici un avant et après
{% img center /images/avantapres.png 600 189 'avant et après' 'avant et après' %}

C'est beaucoup mieux ! Un petit bémol pour le PHP (ma joie !) il faut obligatoirement mettre `<?php`.

Bref un commande que j'aurai aimé avoir avant.

Il y a plein d'astuce de ce genre sur le site suivant

 * [til](https://til.hashrocket.com/) **TIL** est l'abbrévation de **T**oday **I** **L**ean cela vient de [reddit](https://www.reddit.com/r/todayilearned/)
