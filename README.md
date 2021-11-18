<p align="center" class="text-center"><img src="https://git-scm.com/images/logo@2x.png" height="64"></p>

## Ligne de commandes

|                                             Description de la commande                                             |         Ligne de commande               |
|--------------------------------------------------------------------------------------------------------------------|---------------------------------------|
| Cherry-pick un commit                                                                                              | git cherry-pick -n &lt;commit-sha&gt; |
| Voir les changements sur un commit                                                                                 | git show &lt;commit-sha&gt;           |
| Lister toutes les branches                                                                                         | git branch -a                         |
| Lister toutes les remote branches                                                                                  | git branch -r                         |
| Créer une branche et se placer dessus                                                                              | git checkout -b                       |
| Voir toutes les branches fusionné sur master                                                                       | git branch -r --merged master         |
| Publier une branche                                                                                                | git push origin &lt;branch&gt;        |
| Convertir un long sha en short sha                                                                                 | git rev-parse --short &lt;sha&gt;     |
| Pull la version du serveur + rebase + push (equivalent of git stash save && git pull && git stash pop && git push) | git pull --rebase && git pull         |

## Différence pour reset un fichier
Pour bien comprendre la différence entre les options du `git reset`, il faut savoir les différents états d'un fichier.  
  
Un fichier peut avoir jusqu'à 4 états différents sans parler de stash, de branches, ...
 - son contenu sur votre copie de travail (on parle de la même copie de travail que celle de Subversion),
 - son contenu sur l’index,
 - son contenu sur la branche actuelle sur le dépôt local,
 - son contenu sur la branche actuelle sur le dépôt d’origine.
  
On peut alors résumer la commande reset par :
 - l’option --soft qui ne modifie que la HEAD,
 - l’option --mixed (qui est l’option par défaut) qui modifie la HEAD et l’index,
 - et enfin, l’option --hard qui modifie la HEAD, l’index et la copie de travail (c’est l’équivalent d’un svn revert dans Subversion).

![Git Reset](http://blog.octo.com/wp-content/uploads/2010/10/git-reset.png "Git Reset")

## Repository settings

### Require expression in commit messages

```
^(feat|fix|refactor|docs|test|revert|perf|build|chore|ci|style|Merge)(:|\(\w+\):).*$
```

Example:

 - feat: login
 - ci(test): add phpunit job

### Branch name

```
^(master|main|develop)$|^(feature|release|hotfix|refactor|ref|bug|wip)\/.*$
```

Example:

 - master
 - feature/login
 - hotfix/redirect-page

### Prohibited file names

```
.*\.(exe|war|ear|msi|apk|ipa|cab|rpm|mp4|ogg|iso|class|avi|wmv|mov|deb|bar|zip|gz|mkv|mp3|rar|tar|torrent|7z)$
```

## Tips

### Diff
```bash
git diff -w
```
Voir la différence entre 2 versions sans prendre en compte les whitespaces.

### Add
```bash
git add -N
```
Avoir la possibilité de voir le fichier quand on fait un diff sans l'ajouter.

```bash
git add -p
```
Découpe les modifications du fichier en petit morceau te permettant d'executer des commandes sur chaque partie.
Cette fonction ne touche pas au fichier mais uniquement au commit. Le fichier sur ton disque reste inchangé.

### Log
```bash
git log
```
Commande très puissante (remplaçant git blame) permettant la gestion des logs.
L'option -S permet de spécifier une expression à rechercher dans les lignes actives du diff.
L'option -L permet d'afficher les logs d'une partie du fichier.

## Workflow

![Workflow Git](https://wac-cdn.atlassian.com/dam/jcr:21cf772d-2ba5-4686-8259-fcd6fd2311df/05.svg?cdnVersion=ex "Workflow Git")

Pour avoir un repo propre et un arbre joli, un workflow doit être mis en place.

### Ajout d'une branche develop
La première étape est de créer la branche develop qui servira de branche principale pour le développement.
```bash
git branch develop 
git push -u origin develop
```

### Ajout d'une nouvelle fonctionnalité
Lorsque l'on souhaite ajouter une fonctionnalité, nous passons par une nouvelle branche.
> Nomenclature : feature_x

```bash
git checkout -b feature/x develop
```

On travaille sur notre fonctionnalité :
```bash
git status 
git add <file>
git commit
```

Quand on a fini la fonctionnalité, on pousse tout ça :
```bash
git pull origin develop
git checkout develop 
git merge --no-ff feature/x
git push
git branch -d feature/x
```

### Création d'une release
Cette partie n'est pas nécessaire mais il se peut que l'on ai à le faire.
> Nomenclature : release-x

On commence par créer la release :
```bash
git checkout -b release/0.1 develop
```

Une fois la release fini on merge la release sur la branche master et on l'a pousse (si on a les droits) :
```bash
git checkout master
git merge release/0.1
git push
```

On la pousse aussi sur la branche develop pour que tout le monde soit à jour :
```bash
git checkout develop
git merge release/0.1
git push
git branch -d release/0.1
```

### Ajout d'un tag
Le tag nous permet de faire un "snapshot" du projet.

Pour le créer, il faut utiliser la commande suivante :
```bash
git tag -a 0.1 -m "Initial public release" master
git push --tags
```

### Pousser un tag en prod
```bash
git fetch --all --tags --prune
git tag
git checkout tags/<tag>
```

### Un bug doit être corrigé ?
Il est fort probable qu'un bug apparaît dans l'application.
> Nomenclature : issues-x

```bash
git checkout -b issue/#001 master
```

Une fois corrigé, il faut juste la merger :
```bash
git checkout master
git merge issue/#001
git push
```

Pour que toute l'équipe soit à jour, il faut aussi le pousser sur develop :
```bash
git checkout develop
git merge issue/#001
git push
git branch -d issue/#001
```

### Récupérer les branches remote à jour

```bash
git remote update origin --prune
```

## Lien(s) utile(s)

 - [Mon fichier .gitconfig](https://raw.githubusercontent.com/Spooky063/dotfiles/master/config/.gitconfig)
 - [Migrer un projet SVN vers GIT](http://www.yterium.net/Migrer-un-projet-SVN-vers-GIT "Migrer un projet SVN vers GIT")

  
Enjoy :)
