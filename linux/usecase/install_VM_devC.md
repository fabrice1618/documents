**Obsolète**

# Installation d'une machine virtuelle Linux développement C

## HyperV

- Gestionnaire de commutateur virtuel: ajoute un commutateur "externe"
- créer une nouvelle machine virtuelle "génération 1" sur commutateur externe
- RAM 2048 Mb
- HDD 20 Go
- Installation à partir de ISO Ubuntu server 20.04

## Installation ubuntu server 20.04 et premier boot
- langue Francais / Clavier Francais
- Cette machine sera utilisée comme serveur, vous devrez utiliser une IP fixe
- Installer openSSH server

```bash
$ ip a
$ sudo ufw enable
$ sudo ufw allow ssh
```

## Connexion avec SSH

A partir de git bash sur le poste de travail:
```bash
$ ssh fab@192.168.1.20
```

```bash
$ sudo apt update && sudo apt upgrade -y
$ sudo halt
```

## Dans HyperV

- Exporter la machine virtuelle
- Importer un ordinateur virtuel 
    - Copier ordinateur virtuel
    - Choisir un autre emplacement de stockage
    - Dans les paramêtres, modifier le nom de la VM

## Nouvelle VM

- Démarrage de la VM
- Connexion SSH
- Modification de l'adresse IP

```bash
$ cd /etc/netplan/
$ ls
00-installer-config.yaml
$ sudo vi 00-installer-config.yaml
$ sudo netplan apply
```
- Nouvelle connexion SSH

```bash
$ sudo sh -c "echo cdev > /etc/hostname"
$ sudo hostname cdev
$ sudo vi /etc/hosts
```

## Environnement de développement C

```bash
$ sudo apt update
$ sudo apt install build-essential
$ sudo apt install gdb
$ gcc --version
gcc (Ubuntu 9.3.0-17ubuntu1~20.04) 9.3.0
$ gdb --version
GNU gdb (Ubuntu 9.2-0ubuntu1~20.04) 9.2
```

## Le classique hello world

Créer le fichier source suivant :
```bash
$ cd
$ vi hello.c
```

```C
// hello.c
#include <stdio.h>
 
int main() {
    printf("Hello, world!\n");
    return 0;
}
```

Ensuite, le compiler pour pouvoir l'éxecuter et examiner son code de retour.

```bash
$ gcc -o hello hello.c
$ ls -l
-rwxrwxr-x 1 fab fab 16696 Sep 10 15:48 hello
-rw-rw-r-- 1 fab fab    93 Sep 10 15:48 hello.c
$ ./hello
Hello, world!
$ echo $?
0
```

## Utilisation basique de make

Les fichiers Makefile permettent de très nombreuses fonctionnalités décrites dans la documentation et dans de nombreux projets open source.
Consultez la documentation : https://www.gnu.org/software/make/manual/make.html

Le programme précédent est découpé en 3 fichiers :
```bash
$ cd
$ vi hello.c
$ vi say_hello.c
$ vi say_hello.h
```

Le programme principal.

```C
// hello.c
#include "say_hello.h"
 
int main() {
    say_hello();
    return 0;
}
```
La fonction say_hello réalisant l'affichage souhaité

```C
// say_hello.c
#include <stdio.h>
 
void say_hello() {
    printf("Hello, world!\n");
}
```

Le fichier header permettant d'inclure le prototype de la fonction

```C
// say_hello.h
#ifndef  SAY_HELLO_H
#define  SAY_HELLO_H

void say_hello();

#endif
```

Compilation à la main et execution du programme

```bash
$ gcc -o hello hello.c say_hello.c
$ ./hello
Hello, world!
$ rm hello

$ vi Makefile
```

Création du fichier Makefile ci-dessous:

```
hello : hello.c say_hello.c say_hello.h
        gcc -o hello hello.c say_hello.c
clean :
        rm hello
```

La cible principale **hello** permet de compiler le programme.
La cible **clean** Efface le programme executable.
La commande make permet alors de compiler le programme.

```
$ ls
Makefile  hello.c  say_hello.c  say_hello.h
$ make
gcc -o hello hello.c say_hello.c
$ ls
Makefile  hello  hello.c  say_hello.c  say_hello.h
$ ./hello
Hello, world!

$ vi say_hello.c

```

Modification du programme source, traduction du message.

```C
// say_hello.c
#include <stdio.h>
 
void say_hello() {
    printf("Bonjour monde!\n");
}
```

la commande make permet de re-construire l'exécutable. Si les fichiers sources sont plus anciens que l'exécutable, la compilation n'est pas relancée.

```
$ make
gcc -o hello hello.c say_hello.c
$ ./hello
Bonjour, monde !
$ make
make: 'hello' is up to date.
$ make clean
rm hello
$ ls
Makefile  hello.c  say_hello.c  say_hello.h
```

## Connexion depuis vscode sur le poste de travail

- Dans vscode, installer l'extension remote SSH.
- Dans la palette, lancer "add new ssh host"
- Connection command ssh fab@192.168.1.21 -A
- Se connecter et saisir le mot de passe
- ouvrir le dossier /home/<username>
- Editez directement vos fichiers avec vscode
- Le terminal permet de compiler et exécuter les programmes

```bash
$ mkdir -p code/hello

$ mkdir -p code/hello
$ make clean
rm hello
$ ls *hello*
hello.c  say_hello.c  say_hello.h
$ mv *hello* ~/code/hello
$ mv Makefile ~/code/hello
```

## Utilisation du profiler gprof

Documentation gprof: http://sourceware.org/binutils/docs-2.18/gprof/index.html

3 étapes : 
- Compiler le programme avec l'option profiling
- exécuter normalement le programme
- lancer l'utilitaire gprof pour consulter les données

```bash
$ mkdir -p code/gprof

```

```C
//test_gprof.c
#include<stdio.h>

void new_func1(void);

void func1(void)
{
    printf("\n Inside func1 \n");
    int i = 0;

    for(;i<0xffffffff;i++);
    new_func1();

    return;
}

static void func2(void)
{
    printf("\n Inside func2 \n");
    int i = 0;

    for(;i<0xffffffaa;i++);
    return;
}

int main(void)
{
    printf("\n Inside main()\n");
    int i = 0;

    for(;i<0xffffff;i++);
    func1();
    func2();

    return 0;
}
```
```C
//test_gprof_new.c
#include<stdio.h>

void new_func1(void)
{
    printf("\n Inside new_func1()\n");
    int i = 0;

    for(;i<0xffffffee;i++);

    return;
}
```

Compilation avec l'option -pg, exécution, les statistiques sont enregistrées dans gmon.out
```bash
$ gcc -Wall -pg -o test_gprof test_gprof.c test_gprof_new.c 
$ ./test_gprof 

 Inside main()

 Inside func1 

 Inside new_func1()

 Inside func2 
$ ls
gmon.out  test_gprof  test_gprof.c  test_gprof_new.c
```

Générer les fichier analysis.txt
- normal
- (-a) en supprimant les fonctions statiques
- (-b) en réduisant les commentaires
- (-p) imprimer uniquement flat profile
- (-pfonction) Seulement ce qui est relatif à une fonction
- (-P) supprimer flat profile
- (-Q) supprimer call graph
```bash
$  gprof test_gprof gmon.out > analysis.txt
$  gprof -a test_gprof gmon.out > analysis.txt
$  gprof -b test_gprof gmon.out > analysis.txt
$  gprof -p test_gprof gmon.out > analysis.txt
$ gprof -pfunc1 -b test_gprof gmon.out > analysis.txt
$ gprof -P -b test_gprof gmon.out > analysis.txt
$ gprof -Q -b test_gprof gmon.out > analysis.txt
```

## Extension vscode

https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools


```bash
$ mkdir ~/code/vscode
$ cd ~/code/vscode
$ touch helloworld.cpp
```

```C
#include <iostream>
#include <vector>
#include <string>

using namespace std;

int main()
{
    vector<string> msg {"Hello", "C++", "World", "from", "VS Code", "and the C++ extension!"};

    for (const string& word : msg)
    {
        cout << word << " ";
    }
    cout << endl;
}
```

```bash
$ g++ --version
g++ (Ubuntu 9.3.0-17ubuntu1~20.04) 9.3.0

$ which g++
/usr/bin/g++
```

Configurer la compilation 

- Terminal > Configure Default Build Task
- C/C++: g++ build active
- le fichier tasks.json est créé
- menu terminal / run build task
- A partir du terminal intégré:
 $ ./helloworld 
Hello C++ World from VS Code and the C++ extension! 

Configurer le debugger

- Ajout debugger: Run / Add configuration
- Choisir C++ (GDB/LLDB)
- le fichier launch.json est créé
- modifier le paramètre "stopAtEntry": true pour ajouter un breakpoint sur la fonction main
- "program": "${fileDirname}/${fileBasenameNoExtension}",
- menu Run / Start debugging ...
- Dans l'onglet Run and debug vous disposez des breakpoints et des watch de variables...

## Le debugger gdb

```bash
$ mkdir ~/code/gdb
$ cd ~/code/gdb
$ touch factoriel.c
```

Créer le fichier ci-dessous avec des bugs pour l'exercice !

```C
// factoriel.c
# include <stdio.h>

int main()
{
	int i, num, j;
	printf ("Entrez le nombre: ");
	scanf ("%d", &num );

	for (i=1; i<num; i++)
		j=j*i;    

	printf("La factorielle de %d est %d\n",num,j);
}
```

La factorielle de 3 ne donne pas le bon résultat

```bash
$ gcc -o factoriel factoriel.c
fab@cdev:~/code/gdb$ ./factoriel 
Entrez le nombre: 3
La factorielle de 3 est 65532
```

```bash
$ gcc -g -o factoriel factoriel.c
$ gdb factoriel
```

```gdb
(gdb) break 11
Breakpoint 1 at 0x11d6: file factoriel.c, line 11.
(gdb) run
Starting program: /home/fab/code/gdb/factoriel 
Entrez le nombre: 3

Breakpoint 1, main () at factoriel.c:11
11                      j=j*i;    
(gdb) print j
$1 = 32767
(gdb) p i
$2 = 1
(gdb) p j
$3 = 32767
(gdb) p num
$4 = 3
(gdb) list
6               int i, num, j;
7               printf ("Entrez le nombre: ");
8               scanf ("%d", &num );
9
10              for (i=1; i<num; i++)
11                      j=j*i;    
12
13              printf("La factorielle de %d est %d\n",num,j);
14      }
```
Autres fonctions disponibles :
- c or continue: Debugger will continue executing until the next break point.
- n or next: Debugger will execute the next line as single instruction.
- s or step: Same as next, but does not treats function as a single instruction, instead goes into the function and executes it line by line.
- quit

## Python3

Python est installé par défaut avec la distribution ubuntu

```python
$ python3
Python 3.8.10 (default, Jun  2 2021, 10:49:15)
[GCC 9.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> print( "Hello python" )
Hello python
>>> quit()
$
```


 