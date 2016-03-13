---
layout: post
title: "Vim-plug : Gestion des plugins"
date: 2016-03-13 17:44:53 +0100
comments: true
categories: [VIM]
---

## Introduction
Il existe une quantité monstrueuse de plugin sur Vim. Je vais parler de la gestion de plugin. Avant de commencer une série sur les meilleurs plugins de Vim.

## Installation de Vim-plug
Nous allons utiliser un gestionnaire de plugin : [vim-plug](https://github.com/junegunn/vim-plug).

Nous allons éditer notre fichier `~/.vimrc`

<!--more-->

Nous collons les lignes suivantes au tout début de votre fichier.

```
" Auto load / install plugin manager

if !1 | finish | endif

" auto-install vim-plug
if empty(glob('~/.vim/autoload/plug.vim'))
    echo "Installing VimPlug..."
    silent !curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
    autocmd VimEnter * PlugInstall
endif

" VimPlug 
call plug#begin('~/.vim/plugged')


" VIMPROC 
Plug 'Shougo/vimproc', { 'do': 'make' }

" Syntastic  
" https://github.com/scrooloose/syntastic
Plug 'scrooloose/syntastic'

" Fugitive
" fugitive.vim: a Git wrapper so awesome, it should be illegal
" https://github.com/tpope/vim-fugitive
Plug 'tpope/vim-fugitive'

" Surround
" surround.vim: quoting/parenthesizing made simple
" https://github.com/tpope/vim-surround
Plug 'tpope/vim-surround'


call plug#end()
" Required:
filetype plugin indent on

```

Voila exactement ce que fais le programme.

 * Si Vim-plug n'existe pas, On va le télécharger
 * On donne la liste des plugins à télécharger. Pour rajoutez un plugin il suffit de le rajouter entre `call plug#begin('~/.vim/plugged')` et `call plug#end()`

Par exemple si je souhaite rajoutez le plugin Nerdtree (permet un d'avoir une affichage en arbre des fichiers)

```
call plug#begin('~/.vim/plugged')
...
Plug 'scrooloose/nerdtree'
...
call plug#end()
```

Pour installer dans vim 
```
:PlugInstall
```

Normalement tout les plugins s'installent en parallèles avec des jolies barres de progressions.

Pour updater les plugins
```
:PlugUpdate 
```

Pour supprimer les plugins inutiles
```
:PlugClean
```

Enfin il existe une vue spécifique pour voir le status des plugins
```
:PlugStatus
```
## Mais encore..

Ce qui est sympa avec Vim-plug est que l'on peut mettre des conditions dans les plugins

```
Plug 'SirVer/ultisnips' | Plug 'honza/vim-snippets'
```
ici `vim-snippets` dépends de `ultisnips`

On charge paresseusement les plugins

```
Plug 'StanAngeloff/php.vim', { 'for': 'php' }
```
Je n'ai besoin du plugin `php-vim` que si j'utilise un fichier PHP.

```
Plug 'scrooloose/nerdtree', { 'on':  'NERDTreeToggle' }
```
Je charge le plugin que si je l'appelle.


## Les alternatives 
 - [Pathogen](https://github.com/tpope/vim-pathogen) historiquement le premier. Il suffisait de créer un dossier `bundle` dans `.vim` et de cloner le plugin vim. Et le plugin était installé. Pour supprimer le plugin, il suffit faire un rm du dépôt

```sh
mkdir  .vim/bundle
cd .vim/bundle
// on clone le plugin
git clone https://github.com/tpope/vim-surround.git
git clone https://github.com/scrooloose/nerdtree.git
```
J'ai utilisé ceci pendant des années et cela me suffisait 

 - [NeoBundle](https://github.com/Shougo/neobundle.vim) Un peu près la même chose que vim-plug. Il est censé être un peu plus complet que [vim-plug](https://github.com/junegunn/vim-plug).

## Commiter son `.vimrc`

 L'idée est de versionner son fichier `.vimrc` sur github. Et de partager les raccourcis claviers, Il n'est pas rare d'avoir des fichiers de 1000 lignes. Je suis en train de refaire le mien. 

 Un vimrc c'est pour résumer.

  1. Je copie/colle tout les `.vimrc` que je trouve
  2. J'installe 70 plugins
  3. Je me rend compte que j'utilise 5 plugins à peine.
  4. Je n'utilise aucun raccourci donc je supprime tout.
  5. heu..  je ne suis plus du tout à jours sur les plugins,.. On recommence à l'étape 1

Nous allons continuer avec les plugins dans une future série d'articles.

Merci de m'avoir lu, Je m'excuse pour les fautes. 
