# Git / Github / Github desktop

## 1 - GIT

- <https://git-scm.com/>
- SCM: Source Control Management - Gestion de la mise à jour des fichiers
- Distributed Version Control System (VCS)
- Local & remote repository
- Coopération entre plusieurs développeurs
- Qui a fait quelle modification, quand et pourquoi
- Retour sur une version antérieure

Concepts:

- Suivi de l'historique du code et des fichiers
- prendre un "snapshot" de vos fichiers et faisant un commit
- vous pouvez "stage" des fichiers avant un "commit"
- Vous pouvez consulter n'importe quel commit

## Création de votre compte github

- Créer votre compte github <https://github.com/>
- Permet de créer des repository publics or privés

## Installation de git

<https://git-scm.com/downloads>

Important: pendant l'installation modifier les paramètres par défaut suivants:

- sélectionner branche par défaut "main"
- sélectionner fin de ligne "unix style LF"

Configuration de l'utilisateur github et des sauts de ligne.

```bash
git --version

git config --global user.name "votre_github"
git config --global user.email"votre_github@example.com"
git config --global core.autocrlf true
git config --global core.safecrlf false
```

## créer un remote repository sur github

Un fois connecté sur github, dans le menu / Your repositories / New

Renseigner:

- le nom du repository: project
- Une description
- la visibilité public/private
- Ajout de fichier README.md, page par défaut affichée lorsque l'on accède au repository
- Ajout fichier .gitignore, il existe des templates pré-définis assez pratiques

## Créer votre repository local

Dans le dossier contenant votre code:

- création du dossier qui contiendra le projet
- initialisation de git
- consulter l'état de votre repository

```bash
cd /c/code
mkdir project
cd project

git init

git status
```

## Connexion avec votre remote repository

Connexion du remote repository, à ne faire qu'une fois

```bash
git remote add origin https://github.com/votre_github/project.git
git remote -v
```

## Mise à jour repository local et remote repository

Commande pour recevoir les MAJ du repository remote

```bash
git pull origin main
```

Commande pour envoyer les MAJ sur le repository remote

```bash
git push origin main
```

## staging files

C'est un ensemble de fichiers préparés pour l'enregistrement dans un commit

Ajouter dans cette liste

```bash
git add index.html
git add *.html
git add .
git add --all
```

Enlever des fichiers de cette liste

```bash
git rm --cached *.html
```

## Exclure des fichiers de la gestion par git

### 1 - Exclusion pour tous les utilisateurs du repository

Le fichier .gitignore permet d'exclure des fichiers. Ces fichiers ne seront pas suivis par le repository et ne feront pas partie des commit. Le fichier .gitignore est partagé dans le projet et est donc commun à tous les utilisateurs du repository.

```bash
cat .gitignore
node_modules/
toto*
```

### 2 - Exclusion pour le repository local uniquement

Il existe aussi le fichier .git/info/exclude qui a le même rôle. Mais ce fichier est spécifique à ce repository local.

```bash
cat .git/info/exclude
# git ls-files --others --exclude-from=.git/info/exclude
# Lines that start with '#' are comments.
test.php
```

## Commit

Faire un commit, c'est faire un snapshot du projet. Un commit doit impérativement comporter un message indiquant la description de la MAJ ou un numéro de ticket par exemple.

```bash
git commit -m "initial commit"
git log
commit 2ffb1900325e5f23fe5798e1b1094f2d41d2101d (HEAD -> main, origin/main)
Author: fabrice1618 <fabrice1618@gmail.com>
Date:   Sat Dec 12 09:37:03 2020 +0100

    Initial commit
```

Ajoute automatiquement les fichiers dont git assure déjà le suivi.

```bash
git commit -a -m "modification README"
```

## git log

Affiche la liste des commit

```bash
git log
commit bdcca60fdfb4a64cf1b9e23005d279e039ecea81 (HEAD -> main)
Author: fabrice1618 <fabrice1618@gmail.com>
Date:   Sun Dec 13 12:08:38 2020 +0100

    modification README

commit 2ffb1900325e5f23fe5798e1b1094f2d41d2101d (origin/main)
Author: fabrice1618 <fabrice1618@gmail.com>
Date:   Sat Dec 12 09:37:03 2020 +0100

    Initial commit
```

```bash
git log --oneline
bdcca60 (HEAD -> main) modification README
2ffb190 (origin/main) Initial commit
```

## git diff

Affiche la différence entre l'état actuel du répertoire et le dernier commit

```bash
git diff
diff --git a/README.md b/README.md
index dfa4d72..7226e0d 100644
--- a/README.md
+++ b/README.md
@@ -1,2 +1,3 @@
 # Project
 Utilisation de git et de github
+utilisation de git en ligne de commande
```

## git branch

Les branches sont utilisées pour ajouter de nouvelles fonctionnalités ou pour faire des corrections. Grâce aux branches, nous disposons de plusieures versions parallèles de notre projet pour lesquelles nous pourrons faire un "merge" individuel de chaque nouvelle fonction ou nouvelle correction. C'est la méthode pour faire en parallèle différentes modifications sans relation entre elles.

Les branches sont aussi le moyen d'identification des mises à jour lors d'un pull request.

```bash
git branch tuto

git branch
* main
  tuto
git checkout tuto
Switched to branch 'tuto'
```

Une fois les modifications faites, vous pouvez faire un merge dans votre branche principale et supprimer la branche.

```bash
git checkout main
Switched to branch 'main'
ls
README.md
git merge tuto
Updating 9281d40..c5790a3
Fast-forward
 git.md | 2 ++
 1 file changed, 2 insertions(+)
 create mode 100644 git.md
ls
README.md  git.md

git branch -d tuto
git branch -D tuto
```

## Consulter une ancienne version du projet

```bash
git log --oneline
c5790a3 (HEAD -> main, tuto) tuto git2
0a12d8b tuto git
9281d40 README update
bdcca60 modification README
2ffb190 (origin/main) Initial commit

git checkout 9281d40
Note: switching to '9281d40'.
ls
README.md

git checkout main
Previous HEAD position was 9281d40 README update
Switched to branch 'main'
ls
README.md  git.md
```

## Création de remote repository

Il est possible de créer votre propre remote repository sans utilisation d'un service externe type gitlab, github ou bitbucket.

```bash
git init --bare 
git remote add origin /data_repository/project_repo
git push origin main
git pull origin main
git branch -r
```

## git clone

Création d'une copie locale d'un repository. Il n'existe aucun lien avec le repository d'origine.

```bash
cd /c/code
git clone https://github.com/fabrice1618/project.git clone-project
cd clone-project
git log --oneline
```

## 2 - GITHUB Desktop - VS Code

Il existe une interface graphique pour faire les opérations ci-dessus.

<https://desktop.github.com/>

VS Code dispose aussi de modules d'extension permettant de gérer votre repository.

## Fork / Pull request

Pour contribuer à un projet

## Issues

- Gestion des erreurs
- Gestion des tâches

## github pages

- Permet de publier un site statique lié à chaque projet

- Vous disposez d'un site web par compte github en plus

- Il suffit de spécifier une branche et un dossier racine

- URL du site <https://votre_github.github.io/project/>

- CNAME domain definition: Ajouter votre nom de domaine

- Permet d'utiliser le générateur de sites Jekyll

## ssh keys

Pour rendre la connexion automatique entre votre repository local et votre compte github.

Documentation : <https://docs.github.com/en/github-ae@latest/github/authenticating-to-github/about-ssh>

- vérifier si on dispose d'une clé publique
- créer une nouvelle clé
- Insérer la clé publique github.pub sur github dans profile settings/SSH et GPG keys

```bash
ls -al ~/.ssh
drwx------ 2 fab fab 4096 Jan 13 14:18 .
drwxr-xr-x 6 fab fab 4096 Mar  3 11:07 ..
-rw-r--r-- 1 fab fab  222 Jan 13 14:18 known_hosts

cd ~/.ssh
ssh-keygen -t ed25519 -C "fabrice1618@gmail.com" -f github
ls -l
-rw------- 1 fab fab 464 Mar  4 10:16 github
-rw-r--r-- 1 fab fab 103 Mar  4 10:16 github.pub
-rw-r--r-- 1 fab fab 222 Jan 13 14:18 known_hosts

eval "$(ssh-agent -s)"

ssh-add ~/.ssh/github
Enter passphrase for /home/fab/.ssh/github:
Identity added: /home/fab/.ssh/github (fabrice1618@gmail.com)

git remote -v
origin  https://github.com/fabrice1618/flop-security.git (fetch)
origin  https://github.com/fabrice1618/flop-security.git (push)

git remote set-url origin git@github.com:fabrice1618/flop-security.git

git remote -v
origin  git@github.com:fabrice1618/flop-security.git (fetch)
origin  git@github.com:fabrice1618/flop-security.git (push)
```

## Pour aller plus loin

• Lire l’ebook progit <https://git-scm.com/book/en/v2>

- chapitre 1: notions générales
- chapitre 2: utilisation de git
- chapitre 3: les branches
- chapitre 6: github

• La documentation <https://git-scm.com/doc>
