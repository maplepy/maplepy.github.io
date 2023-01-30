---
title: "Les aliases"
date: 2023-01-29
description: Comment utiliser et configurer les aliases.
# toc: false
tags:
  - alias
  - shell
  - QoL
  - fr
categories:
  - shell
# series:
# images:
---


Dans ce premier volet, nous aborderons l'utilisation et la mise en place d'alias. C'est une pratique relativement simple à mettre en place et pourtant très puissante dans le quotidien d'un développeur.


### Les raccourcis

Pour commencer en douceur, nous allons voir comment réduire des plus ou moins grandes commandes en quelques lettres pour nous faire gagner du temps.

Même si taper `nvim` pour ouvrir neovim n'est pas très long, n'avoir qu'à taper seulement `v` est nettement plus rapide. Pour faire ceci, il suffit de créer un simple alias de neovim dans le fichier de config de votre shell favori ou de simplement taper la commande suivante[^1]:
- `alias v='nvim'`


### Les paramètres souvent utilisés

Ce point vise notamment les commandes ou les mêmes paramètres sont utilisés plusieurs fois par jour/semaine, `ls -a` par exemple pour voir les fichiers caches avec ls ou `norminette -R CheckForbiddenSourceHeader` pour vérifier la norme avec le flag demande.
Pour cela rien de plus simple :
- `alias la='ls -a'`
- `alias norm='norminette -R CheckForbiddenSourceHeader'`

D'autres exemples particulièrement pratiques :
- `alias l='ls -l' # ls in a list format`
- `alias la='ls -A' # ls with hidden files`
- `alias ll='ls -lA' # detailed ls with hidden files`
- `alias lt='ls --tree'`
- `alias code='open -a Visual\ Studio\ Code'` (connu pour résoudre le problème sur les Mac a 42)
Vous remarquerez que j'ai mis des commentaires avec le '#' à la suite des aliases, ceux-ci ne seront pas pris en compte dans la commande de l'alias et peuvent donc aider à la compréhension de commandes compliquées.


### Les commandes à rallonge

Tous les jours ou presque, on utilise plusieurs commandes à la suite, quand une seule pourrait faire l'affaire, c'est le sujet de ce chapitre. Imaginez que vous voulez vérifier que votre code est la norme, mais d'autres fichiers dans votre dossier ou sous dossiers ne le sont pas (*cc la minilib*), la solution est de vérifier tous les fichiers séparément, mais ça peut vite être fastidieux sur de gros projets. Heureusement, les aliases vont vous aider encore une fois là-dessus.
Imaginons que nous voulions tester tous nos fichiers `.c` dans le dossier courant, nous pourrions faire `norminette *.c` mais nous verrons tous les fichiers à la norme aussi, pas sûr que ça soit très utile. On peut alors utiliser un grep pour enlever ces `OK!` pour n'avoir que les fichiers par normes. Voilà ce que ça donne dans un alias :
- `alias checkc="norm *.c | grep -v OK"` ceci vérifiera tous les fichiers en `.c` dans votre dossier courant et n'affichera que les erreurs sur les fichiers non normés.
- `alias checkc="norm *.c - | grep '.c' | grep -v OK"` celui-ci n'affichera que **les fichiers** non normés et pas leurs erreurs.

Un autre exemple pour ceux qui aiment avoir leurs fichiers PDF et leurs testers dans le repo pour tout avoir sous la main, cette commande pourrait vous aider à nettoyer ceux-ci lors d'un push sur la vogsphere:
- `alias findextra='find . -iname "*.sh" -o -iname "*.pdf"'` plus qu'à suivre cette commande d'un `-delete` pour tout supprimer.

  → ça donnerait `findextra -delete`


### Les variables

Il est tout à fait possible d'utiliser des variables dans vos aliases, qu'elles soient définies par vous-même ou par l'environnement.
Par exemple :
- la variable `$HOME`, équivalente de `~` ou de `/home/foo` pourra être utilisée dans un alias par exemple.

Là où les variables peuvent devenir intéressantes, c'est dans le cas où elles sont dites ***dynamiques***. Une variable qui change en fonction du dossier dans lequel vous êtes par exemple.
- Vous connaissez surement tous la commande `pwd` (*print working directory*) mais saviez que vous pouvez l'utiliser dans un script ou un alias par exemple avec `$PWD`.
- Un autre exemple est `$EDITOR` qui référencera votre éditeur de choix (`nvim`, `vim`, `emacs` ou `nano` pour les psychopathes) qu'on pourra utiliser pour rapidement accéder à ses fichiers de configurations, exemple :
- `alias modfish='v ~/.config/fish/config.fish'`

Même si `v` est correct puisque nous l'avons déclaré plus tôt, on pourrait avoir des problèmes à changer toutes nos lignes l'utilisant. Si on change `neovim` pour `vim`, pas de réel souci, on peut laisser `v` pour `vim`, mais si on venait à utiliser `emacs`, pas sûr que `v` soit encore très explicite. L'alternative, c'est d'utiliser `$EDITOR`, qui référencera `vim`, `neovim` ou encore `emacs`, à nous de choisir. Voici un exemple avec `$EDITOR` pour modifier ma configuration d'`alacritty`:
- `alias modalacritty='$EDITOR ~/.config/alacritty/alacritty.yml'`


### Fichiers de config

C'est bien beau d'avoir tous ces aliases si on ne peut pas les utiliser sans faire la commande `alias` dans le terminal. Voici donc les chemins des configs des différents shells :
- **BASH**: `~/.bashrc`
- **ZSH**: `~/.zshrc`
- **FISH**: `~/.config/fish/config.fish`

Pour ajouter ces aliases de façon permanente, il faudra réécrire la même commande que vous avez tapé dans le terminal
```bash
alias v='nvim'
alias norm='norminette -R CheckForbiddenSourceHeader'
```

### Pourquoi vos aliases ne fonctionnent pas alors que vous avez bien suivi le tutoriel ?
Eh bien tout simplement parce que les fichiers de config ne s'actualisent pas sur le shell lors d'une mise à jour de ceux-ci. Il faut explicitement dire à votre shell de recharger le fichier de configuration. Pour ce faire, rien de plus simple :
- `source ~/foo` ou foo est le fichier de config de votre shell.

Et puis maintenant qu'on sait tout ça, autant utiliser un alias pour faire cette commande à notre place, non ? On pourra utiliser la commande suivante par exemple :
- `alias fishreload='source ~/.config/fish/config.fish'`
Dès que vous modifierez vos fichiers de config du shell, tapez cette commande et c'est prêt ! Si vous voulez afficher un message comme quoi la mise à jour a été faite, on peut faire un petit `echo` avec
- `alias fishreload='source ~/.config/fish/config.fish && echo "fish config has been updated"'`

### Attention !
Lors de l'ajout d'aliases, il est important de comprendre la syntaxe de ceux-ci. Même s'il parait simple à implémenter, il est aussi à casser si vous ne suivez pas les règles d'un bon alias.

#### Simple ou double guillemets ?

Tout dépendra de l'utilisation, si vous utilisez des doubles, vous ferez ce qu'on appelle sur substitution de toute variable utilisée. Dans le cas de simple guillemet, vous empêcherez au shell de faire du `variable expanding`, la substitution en soi. Exemple :
```shell
$ echo $PWD
/home/max
$ alias dirA="echo Working directory is $PWD"
$ alias dirA
alias dirA='echo Working directory is /home/max'

$ alias dirB='echo Working directory is $PWD'
$ alias dirB
alias dirB='echo Working directory is $PWD'
```
Maintenant si on changeait de dossier
```shell
$ cd 42
$ dirA
Working directory is /home/max
$ dirB
Working directory is /home/max/42
```
Comme on peut le voir, dans `dirB`, la variable s'actualise alors qu'avec `dirA`, ça n'est pas le cas.

#### Bash et les arguments

Bash n'accepte pas d'arguments dans ses aliases, il veut utiliser les commandes [en tant que tel](https://youtu.be/Tj_DHw6L9BQ), donc sans aucun mot qui suit la commande. Pour cela, il faudra donc utiliser des fonctions.

[^1]: Attention, si vous tapez simplement la commande, elle ne sera pas gardée en mémoire lors d'une future ouverte de votre shell. Pour cela, il faut suivre la section [Fichiers de config](#fichiers-de-config)
