# Git / Github / Github desktop

## 1 - GIT

- https://git-scm.com/
- SCM: Source Control Management - Gestion de la mise à jour des fichiers
- Distributed Version Control System (VCS)
- Local & remote repository
- Coopération avec plusieurs développeurs
- Qui a fait quelle modification, quand et pourquoi
- Retour sur une version antérieure

Concepts:

- Suivi de l'historique du code et des fichiers
- prendre un "snapshot" de vos fichiers et faisant un commit
- vous pouvez "stage" des fichiers avant un "commit"
- Vous pouvez consulter n'importe quel commit

## Création de votre compte github

https://github.com/


## Installation de git

https://git-scm.com/downloads

Pendant l'installation: 
- sélectionner branche par défaut "main"
- sélectionner fin de ligne "unix style LF"

Configuration de l'utilisateur github et des sauts de ligne.
```
$ git --version

$ git config --global user.name "votre_github"
$ git config --global user.email"votre_github@example.com"
$ git config --global core.autocrlf true
$ git config --global core.safecrlf false
```

## créer un repository sur github - remote
Un fois connecté sur github, dans le menu / Your repositories / New

Renseigner: 
- le nom du repository: project
- Une description
- la visibilité public/private
- Ajout de fichier README.md, page par défaut affichée lorsque l'on accède au repository
- Ajout fichier .gitignore, il existe des templates assez pratiques

## Créer votre repository local
Dans votre dossier contenant votre code: création du dossier qui contiendra le projet et initialisation de git
```
$ cd /c/code
$ mkdir project
$ cd project
$ git init
```
Pour consulter l'état de votre repository
```
$ git status
```
## Connexion avec votre remote repository

Connexion du remote, à ne faire qu'une fois
```
$ git remote add origin https://github.com/votre_github/project.git
$ git remote -v
```
Commande pour recevoir les MAJ du repository remote
```
$ git pull origin main
```
Commande pour envoyer les MAJ sur le repository remote
```
$ git push origin main
```

## staging files
C'est un ensemble de fichiers préparés pour l'enregistrement dans un commit

Ajouter dans cette liste
```
git add index.html
git add *.html
git add .
git add --all
```
Enlever des fichiers de cette liste
```
git rm --cached *.html
```

## .gitignore
Le fichier .gitignore permet d'exclure des fichiers. Ces fichiers ne seront pas suivis par le repository et ne feront pas partie des commit. Le fichier .gitignore est partagé dans le projet et est donc commun à tous les utilisateurs du repository.
```
$ cat .gitignore
node_modules/
toto*
```
Il existe aussi le fichier .git/info/exclude qui a le même rôle. Mais ce fichier est spécifique à ce repository local.
```
$ cat .git/info/exclude
# git ls-files --others --exclude-from=.git/info/exclude
# Lines that start with '#' are comments.
test.php
```

## Commit
Faire un commit, c'est faire un snapshot du projet. Un commit doit impérativement comporter un message indiquant la description de la MAJ ou un numéro de ticket par exemple.

```
$ git commit -m "initial commit"
$ git log
commit 2ffb1900325e5f23fe5798e1b1094f2d41d2101d (HEAD -> main, origin/main)
Author: fabrice1618 <fabrice1618@gmail.com>
Date:   Sat Dec 12 09:37:03 2020 +0100

    Initial commit
```
Ajoute automatiquement les fichiers dont git assure déjà le suivi.
```
$ git commit -a -m "modification README"
```

## git log
Affiche la liste des commit
```
$ git log
commit bdcca60fdfb4a64cf1b9e23005d279e039ecea81 (HEAD -> main)
Author: fabrice1618 <fabrice1618@gmail.com>
Date:   Sun Dec 13 12:08:38 2020 +0100

    modification README

commit 2ffb1900325e5f23fe5798e1b1094f2d41d2101d (origin/main)
Author: fabrice1618 <fabrice1618@gmail.com>
Date:   Sat Dec 12 09:37:03 2020 +0100

    Initial commit
```

```
$ git log --oneline
bdcca60 (HEAD -> main) modification README
2ffb190 (origin/main) Initial commit
```

## git diff

Affiche la différence entre l'état actuel du répertoire et le dernier commit
```
$ git diff
diff --git a/README.md b/README.md
index dfa4d72..7226e0d 100644
--- a/README.md
+++ b/README.md
@@ -1,2 +1,3 @@
 # Project
 Utilisation de git et de github
+utilisation de git en ligne de commande
```

# git branch
Les branches sont utilisées pour ajouter de nouvelles fonctionnalités ou pour faire des corrections. Grâce aux branches, nous disposons de plusieures versions parallèles de notre projet pour lesquelles nous pourrons faire un "merge" individuel de chaque nouvelle fonction ou nouvelle correction. C'est la méthode pour faire en parallèle différentes modifications sans relation entre elles.

Les branches sont aussi le moyen d'identification des mises à jour lors d'un pull request.
```
$ git branch tuto

$ git branch
* main
  tuto
$ git checkout tuto
Switched to branch 'tuto'
```
Une fois les modifications faites, vous pouvez faire un merge dans votre branche principale et supprimer la branche.
```
$ git checkout main
Switched to branch 'main'
$ ls
README.md
$ git merge tuto
Updating 9281d40..c5790a3
Fast-forward
 git.md | 2 ++
 1 file changed, 2 insertions(+)
 create mode 100644 git.md
$ ls
README.md  git.md

$ git branch -d tuto
$ git branch -D tuto
```

## Consulter une ancienne version du projet

```
$ git log --oneline
c5790a3 (HEAD -> main, tuto) tuto git2
0a12d8b tuto git
9281d40 README update
bdcca60 modification README
2ffb190 (origin/main) Initial commit

$ git checkout 9281d40
Note: switching to '9281d40'.
$ ls
README.md

$ git checkout main
Previous HEAD position was 9281d40 README update
Switched to branch 'main'
$ ls
README.md  git.md
```


# git init - - bare

```
git init --bare 
git remote add origin F:\project_repo
git push origin main
git pull origin main
git branch -r
```

# git clone
Création d'une copie locale d'un repository. Il n'existe aucun lien avec le repository d'origine.
```
cd /c/code
git clone https://github.com/fabrice1618/project.git clone-project
cd clone-project
git log --oneline
```

## 2 - GITHUB Desktop - VS Code
Il existe une interface graphique pour faire les opérations ci-dessus.

https://desktop.github.com/

VS Code dispose aussi de modules d'extension permettant de gérer votre repository.

## 3 - GITHUB

- https://github.com/

- Créer votre compte github

- Créer des repository publics or privés


## Fork / Pull request

Pour contribuer à un projet

## Issues

- Gestion des erreurs
- Gestion des tâches

## github pages

- Permet de publier un site statique lié à chaque projet

- Vous disposez d'un site web par compte github en plus

- Il suffit de spécifier une branche et un dossier racine

- URL du site https://votre_github.github.io/project/

- CNAME domain definition: Ajouter votre nom de domaine

- Permet d'utiliser le générateur de sites Jekyll


## ssh keys

Pour rendre la connexion automatique entre votre repository local et votre compte github.

```
$ cd ~/.ssh
$ ssh-keygen -t rsa -b 4096 -C "fabrice1618@gmail.com"
$ ls
github_rsa 
github_rsa.pub 
id_rsa 
id_rsa.pub 
known_hosts
```

installer la clé id_rsa.pub sur public key


# Pour aller plus loin:

• Lire l’ebook progit chapitres 1,2,3 et 6 https://git-scm.com/book/en/v2
    - Chapitres: 1 notions générales, 2: utilisation de git, chapitre 3: les branches et 6 : github

• La documentation https://git-scm.com/doc
