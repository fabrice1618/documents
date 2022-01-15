
# Cours UNIX: Commandes de base

L'utilisation de ce document ne remplace pas la consultation du manuel UNIX (voir commande man), d'où il est tiré (c'est en fait un condensé de la version française du manuel Linux)


## 1 - Exécution de commandes

### ➔ Lancement d'une commande, arguments

Il existe trois types de commandes exécutables depuis un shell: 

- les commandes internes au shell 
- les commandes externes (sous forme de programmes exécutables) 
- les scripts

Une exécution de commande se compose du nom de la commande suivi éventuellement des paramètres séparés par des espaces. Les paramètres qui commencent par un tiret sont généralement des options spécifiques à la commande.

Exemple:
```
$ cat -b /etc/profile
```

### ➔ Enchaînement des commandes

Plusieurs commandes peuvent êtres enchaînées sur une même ligne en les séparant par un point-virgule.

Exemple:
```
$ clear; pwd; echo "Hello world"
```

### ➔ Lancement en arrière plan

Une commande peut être exécutée en arrière plan en plaçant l'opérateur & en fin de commande (après les paramètres).
Exemple:
```
$ sleep 10 &
```

## 2 - Manipulation des répertoires

Sur UNIX, les noms des répertoires sont séparés par le caractère / (division). Le répertoire
courant est appelé. (point), le répertoire parent .. (deux point), le répertoire racine /
(division) et le répertoire home (le répertoire de l'utilisateur) ~ (tilde).


### ➔ ls


Afficher le contenu d'un répertoire.

La commande ls affiche tout d'abord l'ensemble de ses arguments fichier autres que des
répertoires, puis affiche l'ensemble des fichiers contenus dans chaque répertoire indiqué.
Si aucun argument (autre qu'une option) n'est fourni, le contenu du répertoire en cours (`.')
est affiché.

Syntaxe:
```
ls [options] [fichier...]

Options couramment utilisées:
-a Afficher tous les fichiers, y compris les fichiers cachés.
-l En plus du nom, afficher le type du fichier, les permissions d'accès, le nombre de liens physiques, le nom du propriétaire et du groupe, la taille en octets, et la date de dernière modification.
-L Afficher les informations concernant les fichiers pointés par les liens symboliques et non pas celles concernant les liens eux-mêmes.
```

Exemple: afficher la description du fichier /bin/cp et le contenu du répertoire /var.
```
$ ls -l /bin/cp /var
-r-xr-xr-x 1 root wheel 66440 Apr 21 09:05 /bin/cp
/var:
total 32
drwxr-xr-x 2 root wheel 512 Apr 21 09:02 account
drwxr-xr-x 4 root wheel 512 May 4 16:56 at
drwxr-x--- 2 root wheel 512 May 10 03:01 backups
...
```

### ➔ cd

Changer le répertoire courant. 

Cette commande est en réalité une commande interne des shells.

Syntaxe:
```
cd répertoire
```

Exemple: « aller » à la racine de l'arborescence du système.
```
$ cd /
```

### ➔ mkdir

Créer des répertoires correspondants à chacun des noms passés en paramètre.

Syntaxe:
```
mkdir [options] répertoires...

Options couramment utilisées:
-p Créer les répertoires parents manquants.
```

Exemple: créer le répertoire toto dans le répertoire courant.
```
$ mkdir toto
$ ls -l
total 2
-rwx------ 1 michelon users 503 May 14 15:57 burn.sh
drwxr-xr-x 2 michelon users 512 May 14 15:58 toto
```

### ➔ rmdir

Supprimer des répertoires vides. 

Supprimer chacun des répertoires passés en paramètres, uniquement si ils sont vides.

Syntaxe:
```
rmdir [options] répertoires...
```

Exemple: efface le répertoire (vide) toto qui se trouve dans le répertoire courant.
```
$ ls -l
total 2
-rwx------ 1 michelon users 503 May 14 15:57 burn.sh
drwxr-xr-x 2 michelon users 512 May 14 15:58 toto
$ rmdir toto
$ ls -l
total 2
-rwx------ 1 michelon users 503 May 14 15:57 burn.sh
```

### ➔ find

Rechercher des fichiers de façon récursive. 

find parcourt les arborescences de répertoires commençant en chacun des chemins passés en paramètre, en évaluant les expressions fournies pour chaque fichier trouvé.

Syntaxe :
```
find [chemins...] [expressions...]
```

Tests couramment utilisées dans les expressions
```
-name motif Fichiers dont le nom (sans les répertoires) correspond au motif du shell.
-regex motif Fichiers correspondants à l'expression régulière motif.
-user utilisateur Fichiers appartenant à l'utilisateur indiqué (nom ou UID).
-newer fichier Fichiers modifiés plus récemment que le fichier indiqué.
```

Actions couramment utilisées dans les expressions:
```
-exec commande ; Exécute la commande. La chaîne {} est remplacée par le nom du fichier trouvé.
-print Affiche le nom complet de chaque fichier trouvé.
```

Exemple: éditer avec vi tous les fichiers C++ trouvés dans le répertoire de l'utilisateur.
```
$ find /etc -name "*.conf" -exec tac {} \;
```

## 3 - Manipulation des fichiers

### ➔ rm

Effacer des fichiers. 

rm efface chaque fichier passé en paramètre. Par défaut, il n'efface pas les répertoires.

Syntaxe:
```
rm [options] fichiers...

Options couramment utilisées:
-i Interactif: demander à l'utilisateur de confirmer l'effacement de chaque fichier.
-f Force. Annule -i.
-r Récursif. Supprimer récursivement le contenu des répertoires. A utiliser avec précaution!
-v Afficher le nom de chaque fichier/répertoire avant de supprimer.
```

Il est conseillé de créer un alias sur cette commande, de façon à toujours avoir une confirmation (UNIX étant un système multi-utilisateurs, il n'existe pas de commande "undelete" vraiment efficace):
```
$ alias rm='rm -i'
```

Exemple: sélectionner les fichiers à effacer dans le répertoire /tmp.
```
$ rm -i /tmp/*
```

### ➔ cp

Copier des fichiers. 

Permet de copier des fichiers et des répertoires. Si le dernier argument correspond à un nom de répertoire, cp copie dans ce répertoire chaque fichier indiqué en conservant le même nom.
Les autorisations d'accès des fichiers et des répertoires crées seront les mêmes que celles des fichiers d'origine masquées avec un ET binaire avec 0777, et modifiées par le umask de l'utilisateur.

Syntaxe:
```
cp [options] fichiers... destination

Options couramment utilisées:
-i Interactif. Interroger l'utilisateur avant d'écraser la destination.
-p Conserver le propriétaire, le groupe, les permissions d'accès et les dates du fichier original.
-R Récursif. Copier récursivement les répertoires.
```

Exemple: Faire un miroir du répertoire personnel (et de ses sous-répertoires) dans /tmp.
```
$ cp -R -p ~ /tmp
```

### ➔ mv


Déplacer ou renommer des fichiers. 

Si le dernier argument est le nom d'un répertoire existant, mv placera tous les autres fichiers à l'intérieur de ce répertoire, en conservant leurs noms. Sinon, s'il n'y a que deux fichiers indiqués, il déplacera le premier pour remplacer le second.

Syntaxe:
```
mv [options] source... destination

Options couramment utilisées:
-i Interactif. Demander la confirmation pour écraser tout fichier existant.
-f Force. Annule -i.
-v Affiche le nom des fichiers avant de les déplacer.
```

### ➔ ln


Créer des liens entre fichiers. 

ln crée des liens entre fichiers. Par défaut il s'agit de liens physiques. Si l'on utilise l'option -s, les liens seront symboliques (logiques).

Un fichier peut avoir plusieurs noms. Un lien physique est simplement une manière de nommer un fichier. Un fichier n'est effacé réellement que lorsque son dernier nom est supprimé.

Un lien symbolique est un petit fichier spécial, qui contient un chemin d'accès vers un fichier ou répertoire. Un lien symbolique ne pointe pas nécessairement vers un fichier existant.

Si l'on n'indique qu'un seul nom de fichier, un lien vers ce fichier est crée dans le répertoire courant. Si le dernier argument indique un répertoire existant, ln créera des liens sur chacun des fichiers source indiqués dans ce répertoire.

Par défaut, ln ne supprime pas les fichiers ni les liens symboliques existants.

Syntaxe:
```
ln [options] source... [destination]

Options couramment utilisées:
-f Forcer l'écrasement du fichier destination s'il existe.
-s Créer des liens symboliques et non pas des liens physiques.
```

Exemple: donne un nom supplémentaire au fichier .profile du répertoire home.
```
$ $ ls -l -a ~/.profile*
-rw-r--r-- 1 fab fab 807 sept.  1 12:20 /home/fab/.profile
$ ln .Xresources .Xdefaults
$ ls -l -a ~/.profile*
-rw-r--r-- 2 fab fab 807 sept.  1 12:20 /home/fab/.profile
-rw-r--r-- 2 fab fab 807 sept.  1 12:20 /home/fab/.profile_bis
```

### ➔ cat

Concaténer des fichiers et les afficher. 

Affiche sur la sortie standard le contenu de chacun des fichiers passés en paramètre.

Syntaxe:
```
cat [options] fichiers...
```

Exemple: afficher le contenu de .profile
```
$ cat .profile
```

## 4 - Droits & permissions

### ➔ chmod

Modifier les droits d'accès à un fichier. 

chmod modifie les permissions d'accès de chacun des fichiers passés en paramètre en fonction du paramètre mode.

Syntaxe:
```
chmod [options] mode fichiers...
```
Le paramètre mode peut être:
```
- un nombre octal représentant l'état des bits.
- une représentation symbolique sous la forme [ugoa][[+-=][rwxs]:
    - u, g, o, a: change les droits pour l'utilisateur, le groupe, les autres ou tout le monde.
    - -,+,=: enlève ou ajoute un droit sans toucher aux autres ou change tous les droits.
    - r,w,x,s: le(s) droit(s) à changer: lecture, écriture, exécution et set-uid bit.
```

### ➔ chown

Modifier le propriétaire d'un fichier. 

chown modifie l'utilisateur de chacun des fichiers passés en paramètre. Le propriétaire peut être mentionné par son nom ou par son UID.

Syntaxe:
```
chown [options] propriétaire fichiers...

Options couramment utilisées:
-v Décrire les changements.
-R Effectuer les changements récursivement.
```

### ➔ chgrp

Changer le groupe propriétaire d'un fichier. 

chgrp change l'appartenance de chacun des fichiers indiqués pour qu'ils deviennent propriétés du groupe indiqué. Le groupe peut être mentionné par son nom ou par son GID.

Syntaxe:
```
chgrp [options] groupe fichiers...

Options couramment utilisées:
-R Effectuer les changements récursivement.
```

### ➔ id

Afficher les UIDs et GIDs effectifs et réels. 

id affiche les informations concernant l'utilisateur indiqué, ou l'utilisateur du processus appelant si aucun utilisateur n'est mentionné.

Syntaxe:
```
id [options] [utilisateur]

$ id
uid=1000(fab) gid=1000(fab) groupes=1000(fab),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),108(kvm),120(lpadmin),132(lxd),133(sambashare),134(docker),136(libvirt)
```

### ➔ sudo

Exécuter une commande comme un autre utilisateur. 

sudo permet à un utilisateur d'executer temporairement une commande en tant qu'un autre utilisteur ou root.

Syntaxe:
```
sudo [options] -u user [command]
```

Exemple: mettre à jour le système et ouvrir un shell root
```
$ sudo apt update && sudo apt upgrade
fab@desktop:~$ sudo bash
root@desktop:/home/fab# 
```

## 5 - Affichage

### ➔ echo

Afficher une ligne de texte.

Syntaxe:
```
echo [options] [message...]
Options couramment utilisées:
-n Ne pas effectuer le saut de ligne final.
```

### ➔ more, less

Filtre lecteur de fichier. 

More est un filtre permettant de se déplacer dans un texte écran par écran. La commande less est plus puissante que more car elle permet, entre autres, de ce déplacer librement d'avant en arrière. Généralement utilisée avec le redirecteur pipe |.

Exemple: afficher le log système page par page:
```
$ dmesg | less
```

## 6 - Édition de flux d'entrée / sortie et de fichier

Toutes les commandes de ce chapitre peuvent évidement s'utiliser seule mais elle sont généralement utilisées, comme more et less, avec le redirecteur pipe |.

### ➔ tee

Copier l'entrée standard sur la sortie standard et, en même temps, dans un fichier. 

Permet de voir le résultat d'un commande tout en l'enregistrant dans un fichier.

Exemple: sauver la sortie du log système dans le fichier monlog tout en le visualisant page par
page.
```
$ dmesg | tee monlog | less
```

### ➔ sort

Trier les lignes d'un fichier texte. 

sort trie, regroupe ou compare toutes les lignes des fichiers indiqués. Par défaut, elle trie dans l'ordre alphanumérique (codes ASCII).

Syntaxe:
```
sort [options] [FILE]

Options couramment utilisées:
-b Ignorer les blancs en début de ligne.
-f Ignorer la casse des caractères.
-n Trier suivant la valeur arithmétique (numérique).
-r Inverser l'ordre de tri.
-t carac Utiliser le caractère carac afin de distinguer les champs
+POS1 [-POS2] Indiquer un ou plusieurs champs à utiliser comme clé de tri plutôt que la ligne entière.
```

Exemple: afficher les utilisateurs du système dans l'ordre alpha
```
$ cat /etc/passwd | sort
```

### ➔ tail, head

Afficher la première/dernière partie d'un fichier.

tail affiche la dernière partie (par défaut : 10 lignes) de chacun des fichiers indiques,

head affiche la première partie (10 lignes par défaut) de chacun des fichiers indiqués.

Syntaxe:
```
tail [OPTION]... [FILE]...
head [OPTION]... [FILE]...

Options couramment utilisées:
-n N Afficher les N premières lignes.
```
Exemple: afficher les trois plus gros fichiers du répertoire courant.
```
$ ls -l | sort -r -n +4 | head -n 3
```

### ➔ cut

Supprimer une partie de chaque ligne d'un fichier.

Syntaxe:
```
cut OPTION... [FILE]...

Options couramment utilisées:
-f start-[end] Afficher seulement la ou les colonnes données.
-d carac Utiliser le caractère carac comme séparateur de colonne.
-c start-[end] Afficher uniquement les caractères au positions données.
```

### ➔ uniq

Éliminer les lignes dupliquées dans un fichier trié. 

Uniq affiche les lignes uniques d'un fichier préalablement trié, en ne conservant qu'un seul exemplaire de chacune d'elles.

Syntaxe:
```
uniq [OPTION]... [INPUT [OUTPUT]]

Options couramment utilisées:
-c Afficher également le nombre d'occurrences de chaque ligne.
```

Exemple: Liste des shells utilisés sur le système
```
$ cat /etc/passwd | cut -d ':' -f 7 | sort | uniq -c
```

### ➔ tr

Transposer ou éliminer des caractères. 

Les arguments chaine1 et (éventuellement) chaine2 décrivent des ensembles ordonnés de caractères.

Syntaxe:
```
tr [options] chaine1 [chaine2]
Options couramment utilisées:
-d supprime les caractères chaine
```

Exemple d'ensembles: 
```
a, aaa, a-z, '[:lower:]', '[:alnum:]', '[:blank:]'
```

Exemple: afficher le contenu du fichier /etc/passwd en remplaçant les caractère ':' en '@'.
```
$ cat /etc/passwd | tr : @
```

### ➔ wc

Afficher le nombre d'octets, de mots et de lignes d'un fichier. 

Par défaut wc affiche les trois valeurs. Les options permettent de n'en afficher que certaines d'entre elles.

Syntaxe:
```
wc [OPTION]... [FILE]...

Options couramment utilisées:
-c Afficher uniquement le nombre d'octets.
-w Afficher uniquement le nombre de mots.
-l Afficher uniquement le nombre de sauts de lignes.
```

Exemple:compter le nombre de ligne du fichier /etc/passwd.
```
$ cat /etc/passwd | wc -l
```

## 7 - Environnement de travail

### ➔ pwd

Afficher le nom du répertoire de travail en cours.

Syntaxe:
```
$ pwd
/home/fab
```

### ➔ date

Afficher ou configurer la date et l'heure du système. 

date sans argument affiche la date et l'heure actuelles. Si un argument commençant par un `+' est indiqué, la date et l'heure sont affichées avec un format contrôlé par cet argument.La structure de la chaîne précisant le format est semblable à celle utilisée avec la fonction C strftime. La page man de date décrit précisément ce format.

Si l'on fournit un argument ne commençant pas par `+', date le considère comme une heure à utiliser pour configurer l'horloge système.

Syntaxe:
```
date [options] [+FORMAT] [MMJJhhmm[[SS]AA][.ss]]
```

Exemple: afficher la date sous la forme jour/mois/année.
```
$ date +%d/%m/%Y
15/01/2022
```

### ➔ who, w

Montrer qui est connecté. 

who affiche les informations suivantes pour chaque utilisateur connecté :
- nom de connexion
- terminal
- heure de connexion
- nom d'hôte distant, ou numéro de terminal X

La commande w permet de savoir qui est connecté et que font ces utilisateurs.

Exemple:
```
$ who -H
NOM      LIGNE        HEURE            COMMENTAIRE
fab      :0           2022-01-14 13:02 (:0)
```

Exemple:
```
$ w
 17:00:49 up 1 day,  3:59,  1 user,  load average: 0,21, 0,24, 0,18
UTIL.    TTY      DE               LOGIN@   IDLE   JCPU   PCPU QUOI
fab      :0       :0               ven.13   ?xdm?   1:44m  0.02s /usr/lib/gdm3/gdm-x-session --run-script env
```

### ➔ passwd

Changer le mot de passe d'un utilisateur. 

passwd permet de changer de façon interactive le mot de passe d'un utilisateur (de l'utilisateur courant si aucun utilisateur n'est précisé).

Syntaxe:
```
passwd [options] [utilisateur]
```

## 8 - gestion des processus

### ➔ ps

Afficher l'état des processus en cours. 

ps présente un cliché instantané des processus en cours. Les options sont complètement différentes suivant le type d'UNIX (Système V, BSD, Linux).

Syntaxe:
```
ps [options]
Options couramment utilisées (BSD, Linux):
l affichage long (plus détaillé).
u infos utilisateurs.
x y compris les processus non attachés à un terminal (daemons)
a y compris les processus des autres utilisateurs
r seulement les processus en cours d'exécution.
```

Exemples:
```
$ ps au
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
fab         5181  0.0  0.0  19640  4244 pts/0    Ss+  janv.14   0:00 /bin/bash --login
fab        13664  0.0  0.0  19628  5380 pts/1    Ss   janv.14   0:00 bash
fab        25675  0.0  0.0  19512  5088 pts/2    Ss+  10:11   0:00 /bin/bash
fab        25683  0.0  0.0  19512  5184 pts/3    Ss   10:11   0:00 /bin/bash
fab        45867  0.0  0.0  18508  4180 pts/1    S+   16:58   0:00 man w
fab        45877  0.0  0.0  17076  2316 pts/1    S+   16:58   0:00 pager
fab        46174  0.0  0.0  20224  3480 pts/3    R+   17:06   0:00 ps au

$ ps -ef | grep bash
fab         5181    4975  0 janv.14 pts/0  00:00:00 /bin/bash --login
fab        13664   13656  0 janv.14 pts/1  00:00:00 bash
fab        25675   25664  0 10:11 pts/2    00:00:00 /bin/bash
fab        25683   25664  0 10:11 pts/3    00:00:00 /bin/bash
fab        46212   25683  0 17:09 pts/3    00:00:00 grep --color=auto bash
```

### ➔ pgrep

Afficher les processus en cours correspondants aux critères en paramètre

Syntaxe:
```
pgrep [options] pattern

$  pgrep -u fab bash
```


### ➔ kill

Envoyer un signal à un processus. 

kill envoie le signal indiqué aux processus mentionnés (PID seulement). Si on ne précise pas de signal, TERM est envoyé.

Syntaxe:
```
kill [options] [pid]
Options couramment utilisées:
-s signal Indique le signal à envoyer. Celui-ci peut être spécifié par son nom ou par son numéro.
-l Affiche une liste des noms de signaux connus.
```

Exemple: 
```
$ kill -s SIGUSR1 46288
$ man signal
```

### ➔ killall, pkill

Envoyer un signal à des processus indiqués par leurs noms. 

killall envoie un signal à tous les processus en train d'exécuter les commandes mentionnées. Si aucun signal n'est précisé, TERM est envoyé.

Cette commande est remplacée par la commande pkill équivalente sur certains UNIX. Attention cependant, car souvent
lorsque ces deux commandes sont présentes en même temps, killall sert plutôt à tuer TOUS les processus d'un seul coup!

Syntaxe:
```
killall [options] [-signal] processus...

Options couramment utilisées:
-l Affiche une liste des noms de signaux connus.
-v Affiche un compte-rendu de l'émission du signal.
```

Exemple: 
```
$ killall -USR1 bash
```

## 9 - Gestion des disques et partitions

### ➔ mount

Monter un système de fichiers. 

La commande mount permet d'attacher un système de fichiers sur un périphérique quelconque à l'arborescence du système.

Utilisée sans paramètres ni options, mount affiche la liste des périphériques déjà montés, avec leur paramètres de montage.

La forme standard de la commande mount est:
```
mount [options] périphérique | répertoire

Options couramment utilisées:
-a monte tous les systèmes de fichiers listés dans /etc/fstab qui ne sont pas encore montés.
-t spécifie un type de périphérique (type du système de fichier en général).
```

Exemple: affiche la liste des périphériques montés.
```
$ mount
```

### ➔ umount

Démonter des systèmes de fichiers. 

La commande umount détache le(s) système(s) de fichiers mentionné(s) de la hiérarchie des fichiers.

Syntaxe:
```
umount [options] périphérique | répertoire

Options couramment utilisées:
-a Tente de tout démonter.
```

Exemple: démonter le disque USB /dev/sdb1
```
$ umount /dev/sdb1
```

### ➔ df

Affiche des informations sur l'espace libre et occupé des systèmes de fichiers. 

Sans arguments, df indiquera les quantités correspondant à tous les systèmes de fichiers montés.

Syntaxe:
```
df [options] [fichier...]

Options couramment utilisées:
-h Plus lisible, affiche en octets, ko, mo et go au lieu du nombre de blocs.
```

### ➔ du

Statistiques sur l'utilisation des répertoires. 

du affiche la quantité d'espace disque utilisée par chacun des arguments, et pour chaque sous-répertoire des répertoires indiqués en argument.

Syntaxe:
```
du [options] [répertoires...]

Options couramment utilisées:
-h Plus lisible, sur certains systèmes affiche en octets, Ko, Mo ou Go au lieu du nombre de blocs.
```

## 10 - Exécution différées & programmées

### ➔ at

Mémoriser un travail à exécuter ultérieurement. 

at lit, depuis l'entrée standard, ou depuis un fichier (option -f) des commandes qui seront exécuterées ultérieurement, en utilisant le shell /bin/sh. Par défaut, cette commande n'est pas installée.

At permet d'indiquer l'heure de lancement de manière assez complète. Par exemple: HHMM , HH:MM (heure, minute) MMJJAA, MM/JJ/AA, JJ.MM.AA. (jour, mois, années)

Syntaxe:
```
at [-f fichier] [options] HEURE

Options couramment utilisées:
-f fichier Lire la commande à exécuter dans le fichier.
```

Exemple: exécuter la commande sleep dans 10 minutes.
```
$ sudo apt install at
$ echo sleep 100 | at -m now + 10 minutes
```

### ➔ atq

Lister les travaux programmés avec la commande at.

Exemple:
```
$ atq
```

### ➔ atrm

Supprimer des travaux programmés.

Exemple:
```
$ atrm 1
```

### ➔ crontab

Gérer son fichier crontab personnel.

Le démon cron permet de lancer des commandes différées et surtout répétitives. cron s'éveille toutes les minutes, examine les tables crontab mémorisées, et vérifie chaque commande pour savoir s'il doit la lancer dans la minute à venir.

crontab est le programme utilisé pour installer, supprimer, ou afficher le contenu des tables permettant de piloter le fonctionnement du démon cron. Chaque utilisateur dispose de sa propre table crontab.

Si l'option -u est indiquée, elle permet de préciser le nom de l'utilisateur dont la table crontab doit être manipulée, sinon c'est la table crontab de l'utilisateur courant.

Si un nom de fichier est indiqué, le contenu de ce fichier remplace celui de la table crontab courante.

Syntaxe:
```
crontab [options] [-u utilisateur] [fichier]

Options couramment utilisées:
-l liste la table crontab courante.
-r supprime la table crontab courante.
-e lance un éditeur de texte pour éditer la table crontab courante. Utilise la variable d'environnement EDITOR pour choisir l'éditeur à lancer.
```

La page du manuel sur crontab dans la section 5 contient toutes les informations concernant la
syntaxe d'un fichier crontab:
```
$ man crontab
$ man 5 crontab
```

## 11 - Aide

### ➔ apropos, whatis

Rechercher une chaîne de caractère sdans la base de données whatis. 

La base de données whatis est créée à partir des pages du manuel UNIX pour faciliter et accélérer la recherche.

Syntaxe:
```
$ apropos crontab
anacrontab (5)       - configuration file for anacron
crontab (1)          - maintain crontab files for individual users (Vixie Cron)
crontab (5)          - tables for driving cron

$ whatis  -w '*' | sort
```
### ➔ man

Afficher une page du manuel UNIX.

Syntaxe:
```
man [section] page
```

## 12 - Archivage

### ➔ gzip, gunzip, zcat, bzip2, bunzip

Compresser ou décompresser des fichiers.

gzip réduit la taille des fichiers nommes en utilisant le codage Lempel-Ziv (LZ77).
Quand c'est possible, chaque fichier est remplacé par un autre fichier portant l'extension .gz, en gardant les mêmes modes de permissions, et les mêmes dates de dernier accès et de modification.
bzip2 et bunzip2 on la même fonction que gzip et gunzip mais utilisent un algorithme de compression différent qui donne dans la plupart des cas de meilleurs résultats.

Syntaxe:
```
gzip|gunzip|zcat [options] [fichiers...]
```

### ➔ tar

Utilitaire de gestion d'archives.

tar est utilisé pour créer et restaurer des fichiers a partir d'une archive connue sous le nom de _tarfile_. Cette commande est très complète et ne peut être expliquée complètement ici. 

Syntaxe:
```
tar [options] [fichiers...] [répertoires...]
```

Créer une archive du répertoire personnel dans un fichier:
```
$ tar -c -v -f fichier.tar $HOME
```

Restaurer l'archive précédente dans le répertoire courant:
```
$ tar -x -v -f fichier.tar
```

Lister le contenu de l'archive précédente:
```
$ tar -t -v -f fichier.tar
```




