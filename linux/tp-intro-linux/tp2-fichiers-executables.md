# TP OS n°2 - Les fichiers exécutables

## L’éditeur de texte vim

La version incluse dans Linux est en fait vim( vi improved ) qui comporte quelques différences avec vi. vi/vim comprend deux modes de fonctionnement : le mode commande et le mode insertion.
Après le lancement devi/vim, c’est le mode commande qui est actif. Pour passer en mode insertion (de texte évidemment) il faut appuyer sur la touche ioua. On sait que l’on est en mode insertion par l’affichage de INSERT en bas de la fenêtre. Pour revenir au mode commande, il faut appuyer sur la touche Echap et l’affichage de INSERT en bas de la fenêtre disparaît.
```
Quelques commandes intéressantes :
:q! : sortie sans sauvegarde
:wq : sortie avec sauvegarde
:x : sortie avec sauvegarde
$ : se déplacer sur le dernier caractère de la ligne
ZZ : sortie avec sauvegarde
:w : sauvegarde sans sortie
Ctrl f : afficher la page suivante
Ctrl b : afficher la page précédente
Ctrl d : afficher la demi-page suivante
Ctrl u : afficher la demi-page précédente
e : se déplacer à la fin du mot
b : se déplacer au début du mot
w : se déplacer au début du mot suivant
H : se déplacer en haut de l’écran
L : se déplacer en bas de l’écran
M : se déplacer au milieu de l’écran
z. : décaler l’affichage avec la ligne courante au centre
z (return) : décaler l’affichage avec la ligne courante en haut
z- : décaler l’affichage pour que la ligne courante
:num_ligne : se déplacer à la ligne num_ligne
G (ou :$) : aller à la fin du fichier
u : annulation de la dernière modification
dd : suppression de la ligne courante
2dd : suppression des deux lignes suivantes
D : suppression de la fin de la ligne à partir du curseur
:3,7 d : suppression des lignes 3 à 7
:3,7 t 10 : copie des lignes 3 à 7 après la ligne 10
:3,7 m 10 : transfert des lignes 3 à 7 après la ligne 10
yy : mémorisation de la ligne courante (copier)
3yy : mémorisation des 3 lignes suivantes (copier)
p : copie ce qui a été mémorisé après le curseur
P : copie ce qui a été mémorisé avant le curseur
:set nu : affichage des numéros de ligne
/mot : recherche le mot mot (on se déplace avec n ou N ou *)
```

### Étape n°1 : Les fichiers exécutables

Un fichier exécutable est un fichier contenant un programme (des instructions en langage machine) et identifié par le système d’exploitation en tant que tel. Le chargement d’un tel fichier entraîne la création d’un processus dans le système, et l’exécution du programme.

Nous utiliserons gcc sous Linux pour produire un fichier binaire exécutable. Par défaut,gcc fabrique un fichier nommé a.out. On peut aussi préciser le nom de l’exécutable avec l’option -o. Il n’y a pas d’extension spécifique pour les exécutables sous UNIX/Linux. Par défaut, gcc utilise une édition de liens dynamique.

Tout d’abord, il faut créer un fichier source avec l’éditeur vim:
```
$ vim hello.c
```
Il faut taper i pour passer en mode insertion, puis éditer le fichier avec le contenu ci-dessous. Pour finir, on enregistre et on quitte (touche Echap pour revenir en mode commande puis :xou :wq).
```
// Affiche à l’écran le message Hello world
#include <stdio.h>
#define TEXTE "Hello world"

int main ()
{
printf("%s\n", TEXTE);
return 0;
}

```
Le fichier source hello.c

Remarque : hello world (« bonjour le monde ») sont les mots traditionnellement écrits par un programme informatique simple dont le but est de faire la démonstration rapide d’un langage de programmation (par exemple à but pédagogique) ou le test d’un compilateur. La tradition d’utiliser « hello world » comme message de test a été initiée par le livre The C Programming Language de Brian Kernighan et Dennis Ritchie. Le premier exemple de ce livre affiche "hello, world". Au XXIe siècle, les programmes affichent plus souvent "Hello world !".

**1) Est-ce q’un fichier source est un fichier binaire? Non!**

```
$ file hello.c
hello.c: ASCII C program text
```

2) Est-ce q’un fichier en-tête ( header ) est un fichier binaire? Non!

```
$ file /usr/include/stdio.h
/usr/include/stdio.h: ASCII C program text
```

Pour fabriquer un fichier binaire (avec une édition de liens dynamique par défaut), il faut utiliser gcc:
```
$ gcc hello.c
 
$ ls -hl a.out
-rwxr-xr-x 1 tv tv 5,7K 2010-07-18 11:52 a.out
```


3) Est-ce qu’un fichier fabriqué par gcc est un fichier binaire? Oui!
// Exécuter le fichier binaire
```
$ ./a.out
Hello world
 
$ file a.out
a.out: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked (uses
shared libs), for GNU/Linux 2.6.9,not stripped
```

Remarque : ELF (Executable and Linking Format) est un format de fichier informatique binaire utilisé pour l’enregistrement de code compilé (objets, exécutables, bibliothèques de fonctions). La commande man elf donnera de plus amples informations. Aujourd’hui, ce format est utilisé dans la plupart des systèmes d’exploitation Unix (GNU/Linux, Solaris, Irix, System V, BSD), à l’exception de Mac OS X qui utilise le format Mach-O. Windows utilise le format PE (Portable Executable). Par son format, un fichier exécutable sera déjà exclusivement utilisable sur un OS donné.


4) Un fichier binaire peut-il contenir aussi du texte? Oui!

Il est plus sage d’utiliser la commande strings (mais vous pouvez essayer avec la commande cat ou vim !) :
```
$ strings a.out
/lib/ld-linux.so.
__gmon_start__
libc.so.
_IO_stdin_used
puts
__libc_start_main
GLIBC_2.
PTRh
[^_]
Hello world
```

On retrouve bien notre chaîne de caractères "Hello world". On peut aussi modifier le contenu d’un fichier
binaire, par exemple le ’w’ en ’W’ :
```
$ hexedit a.out

$ ./a.out
Hello World
```

5) Que contient un fichier binaire exécutable? Une structure (un format) et des instructions
machines

Les fichiers exécutable possèdent un format relativement complexe qui dépasse l’objet de ce TP.

Visualiser la structure d’un fichier exécutable
```
$ objdump -s a.out
```

Visualiser les instructions machines d’un fichier exécutable
```
$ objdump -d a.out
```

On trouve par exemple une métadonnée dans la section.comment:GCC : (GNU) 4.4.3. On peut vérifier en demandant la version de gcc :

```
$ gcc -v
```
Exercice:

Refaire l'exemple ci-dessus en séparant le code en 2 fichiers.

Le premier contenant le code principal: hello.c

Le second contenant la définition du TEXTE: hello.h

Utiliser la commande make et un fichier Makefile pour recompiler le programme si un des 2 fichiers est modifié
