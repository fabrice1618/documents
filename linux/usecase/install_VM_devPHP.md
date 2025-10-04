**Obsolète**

# Installation d'une VM pour le développement PHP

## Configuration de base

## Installation VM avec Ubuntu desktop 22.04

- 2 cores
- RAM 4 Go
- HDD 15 Go (25 Go recommandés)
- Configuration réseau bridge
- installation minimale 
  - pendant l'installation bien configurer le clavier
  - penser à cocher openssh server pour installation
- Après redemarrage, mettre à jour le système

Remise à jour de tous les logiciels installés
```
$ sudo apt update && sudo apt upgrade -y
```

- Vérification de l'install de openssh server
```
$ sudo systemctl status sshd
$ sudo apt install net-tools
$ netstat -tulp
```

- Install openssh server et vérification
```
$ sudo apt update
$ sudo apt install openssh-server
$ sudo systemctl status sshd
$ netstat -tulp
```

- première connexion SSH à partir de votre machine. L'utilisation est plus facile.

- Configuration du firewall pour l'accès SSH
```bash
$ ip a
$ sudo ufw enable
$ sudo ufw allow ssh
```

Configuration firewall et vérification que les ports SSH et MySql sont ouverts.
```
$ sudo ufw status
$ sudo ufw allow 3306
$ sudo ufw allow http
$ sudo ufw allow https
$ sudo ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere
3306                       ALLOW IN    Anywhere
80/tcp                     ALLOW IN    Anywhere
22/tcp (v6)                ALLOW IN    Anywhere (v6)
3306 (v6)                  ALLOW IN    Anywhere (v6)
80/tcp (v6)                ALLOW IN    Anywhere (v6)

```

## Installation MySQL

### Installation MySql et test
```
$ sudo apt install mysql-server
$ systemctl status mysql.service
● mysql.service - MySQL Community Server
   Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset: enabled)
   Active: active (running) since Sat 2020-06-06 22:52:35 UTC; 10h ago
  Process: 1157 ExecStart=/usr/sbin/mysqld --daemonize --pid-file=/run/mysqld/mysqld.pid (code=exited, status=0/SUCCESS)
  Process: 866 ExecStartPre=/usr/share/mysql/mysql-systemd-start pre (code=exited, status=0/SUCCESS)
 Main PID: 1159 (mysqld)
    Tasks: 28 (limit: 2317)

$ mysql --version
mysql  Ver 8.0.22-0ubuntu0.20.04.3 for Linux on x86_64 ((Ubuntu))

$ sudo mysqladmin version
mysqladmin  Ver 8.0.22-0ubuntu0.20.04.3 for Linux on x86_64 ((Ubuntu))
Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Server version          8.0.22-0ubuntu0.20.04.3
Protocol version        10
Connection              Localhost via UNIX socket
UNIX socket             /var/run/mysqld/mysqld.sock
Uptime:                 3 min 40 sec

Threads: 2  Questions: 2  Slow queries: 0  Opens: 115  Flush tables: 3  Open tables: 36  Queries per second avg: 0.009
```

### Première connexion au serveur MySQL
```
$ sudo mysql
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.05 sec)

mysql> show grants;
Grants for root@localhost

GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, SHUTDOWN, PROCESS, FILE, REFERENCES, INDEX, ALTER, SHOW DATABASES, SUPER, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER, CREATE TABLESPACE, CREATE ROLE, DROP ROLE ON *.* TO `root`@`localhost` WITH GRANT OPTION 

GRANT APPLICATION_PASSWORD_ADMIN,AUDIT_ADMIN,BACKUP_ADMIN,BINLOG_ADMIN,BINLOG_ENCRYPTION_ADMIN,CLONE_ADMIN,CONNECTION_ADMIN,ENCRYPTION_KEY_ADMIN,GROUP_REPLICATION_ADMIN,INNODB_REDO_LOG_ARCHIVE,INNODB_REDO_LOG_ENABLE,PERSIST_RO_VARIABLES_ADMIN,REPLICATION_APPLIER,REPLICATION_SLAVE_ADMIN,RESOURCE_GROUP_ADMIN,RESOURCE_GROUP_USER,ROLE_ADMIN,SERVICE_CONNECTION_ADMIN,SESSION_VARIABLES_ADMIN,SET_USER_ID,SHOW_ROUTINE,SYSTEM_USER,SYSTEM_VARIABLES_ADMIN,TABLE_ENCRYPTION_ADMIN,XA_RECOVER_ADMIN ON *.* TO `root`@`localhost` WITH GRANT OPTION

GRANT PROXY ON ''@'' TO 'root'@'localhost' WITH GRANT OPTION

3 rows in set (0.00 sec)

mysql> exit
```

### Activation des logs de MySQL et permettre l'accès au serveur MySQL depuis l'extérieur

Activer tous les logs sur notre base de données. Cela nous permettra d'avoir un suivi de ce qu'il se passe sur le serveur.
Activer le log principal dans /var/log/mysql/mysql.log
Activer le slow query log pour indiquer toutes les requetes prennant plus de 2 secondes et les requetes n'utilisant pas d'indexes. (Ces options sont utiles sur des machines de développement).

```
$ sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
#
# * Logging and Replication
#
# Both location gets rotated by the cronjob.
# Be aware that this log type is a performance killer.
general_log_file        = /var/log/mysql/mysql.log
general_log             = 1
#
# Error log - should be very few entries.
#
log_error = /var/log/mysql/error.log
#
# Here you can see queries with especially long duration
slow_query_log          = 1
slow_query_log_file     = /var/log/mysql/mysql-slow.log
long_query_time = 2
log-queries-not-using-indexes

bind-address            = 0.0.0.0
mysqlx-bind-address     = 0.0.0.0


$ sudo systemctl restart mysql.service

$ cd /var/log/mysql
$ ls -ltr
-rw-r----- 1 mysql mysql  178 Dec 14 00:55 query.log
-rw-r----- 1 mysql mysql  178 Dec 14 00:55 mysql-slow.log
-rw-r----- 1 mysql adm   5929 Dec 14 00:55 error.log
```

### Configuration des utilisateurs de MySQL

Habituellement, nous n'utilisons par le user root pour se connecter à la base de données. C'est un choix pas très sûr de conserver les accès root sur le serveur MySQL. Pour cet exercice, nous conserverons les accès root dans un premier temps. Puis à la fin de la configuration, nous sécuriserons l'installation de MySQL et nous désactiverons l'accès root.

Sécurisation de la base de données : https://mariadb.com/kb/en/mysql_secure_installation/

Pour réaliser l'administration des bases de données, nous utilisons un user spécifique pour cette tâche généralement appelé dba (DataBase Administrator). Cet utilisateur doit avoir tous les accès sur toutes les bases de données et tous les privillèges. En général, les modifications de structure de la base de données sont faites avec cet utilisateur, en plus des sauvegardes et des restaurations.

Un autre utilisateur est créé pour se connecter à la base de données pour le compte de l'application. Pour améliorer la sécurité, on limite les privilèges de cet utilisateur. Cet utilisateur ne peut pas utiliser les autres bases de données présentes sur le serveur.

Création de l'utilisateur dba dans mysql pour un accès local. Il est possible de créer aussi un accès pour une administration à distance (par exemple en utilisant MySQL workbench).
```
$ sudo mysql

mysql> CREATE USER 'dba'@'localhost' IDENTIFIED BY 'ghjk';
mysql> GRANT ALL PRIVILEGES ON *.* TO 'dba'@'localhost' WITH GRANT OPTION;
Query OK, 0 rows affected (0.00 sec)

mysql> CREATE USER 'dba'@'%' IDENTIFIED BY 'ghjk';
mysql> GRANT ALL PRIVILEGES ON *.* TO 'dba'@'%' WITH GRANT OPTION;
Query OK, 0 rows affected (0.00 sec)

mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW GRANTS FOR 'dba'@'localhost';
+--------------------------------------------------------------------+
| Grants for dba@localhost                                           |
+--------------------------------------------------------------------+
| GRANT ALL PRIVILEGES ON *.* TO 'dba'@'localhost' WITH GRANT OPTION |
+--------------------------------------------------------------------+
1 row in set (0.00 sec)
mysql> exit;
```

### Création de la base de données flopsecurity et de l'utilisateur associé

On se connecte à MySQL comme utilisateur dba pour la création de la base de données.

```
$ mysql -u dba -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 9
Server version: 8.0.22-0ubuntu0.20.04.3 (Ubuntu)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> create database flopsecurity;
Query OK, 1 row affected (0.11 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| flopsecurity       |
+--------------------+
5 rows in set (0.00 sec)


mysql> CREATE USER 'flopsecurity'@'localhost' IDENTIFIED BY 'abcd';
mysql> GRANT ALL PRIVILEGES ON flopsecurity.* TO 'flopsecurity'@'localhost' WITH GRANT OPTION;
Query OK, 0 rows affected (0.00 sec)

mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW GRANTS FOR 'flopsecurity'@'localhost';
+------------------------------------------------------------------------------------+
| Grants for flopsecurity@localhost                                                     |
+------------------------------------------------------------------------------------+
| GRANT USAGE ON *.* TO `flopsecurity`@`localhost`                                      |
| GRANT ALL PRIVILEGES ON `flopsecurity`.* TO `flopsecurity`@`localhost` WITH GRANT OPTION |
+------------------------------------------------------------------------------------+
2 rows in set (0.00 sec)

mysql> exit;
```
Test de la connexion de l'utilisateur flopsecurity à la base de données flopsecurity
```
$ mysql -u flopsecurity -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 8.0.22-0ubuntu0.20.04.3 (Ubuntu)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use flopsecurity;
Database changed
mysql> show tables;
Empty set (0.01 sec)
mysql> exit;
```

### Installation MySQL Workbench

- Installation custom:
  - MySQL Workbench
  - MySQL shell
  - connector/ODBC
  - connector/C++
  - MySQL Documentation
  - samples and examples
  
Par exemple...

### Sauvegarde des bases de données

Sauvegarde de toutes les bases de données
```
$ mysqldump -u dba -p --all-databases | gzip -9 > all-databases.tar.gz
Enter password:

$ ls -ltr
-rw-rw-r-- 1 fab fab 241440 Dec 14 01:56 all-databases.tar.gz

$ zcat all-databases.tar.gz
```

Sauvegarde de la base de données flopsecurity
```
$ mysqldump -u dba -p flopsecurity | gzip -9 > flopsecurity.tar.gz
Enter password:
$ ls -ltr
-rw-rw-r-- 1 fab fab 241440 Dec 14 01:56 all-databases.tar.gz
-rw-rw-r-- 1 fab fab    458 Dec 14 02:00 flopsecurity.tar.gz

$ zcat flopsecurity.tar.gz
```

### Restauration de la base de données

Quelle est la ou les commandes permettant de restaurer la base de données ?


## Installation apache 2 et PHP 8

Vous devriez avoir PHP8 installé.


```
$ sudo apt install apache2 php libapache2-mod-php php-mysql

$ php --version
PHP 7.4.3 (cli) (built: Oct  6 2020 15:47:56) ( NTS )
Copyright (c) The PHP Group
Zend Engine v3.4.0, Copyright (c) Zend Technologies
    with Zend OPcache v7.4.3, Copyright (c), by Zend Technologies

$ sudo systemctl status apache2
● apache2.service - The Apache HTTP Server
     Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor prese>
     Active: active (running) since Mon 2020-12-14 02:22:20 UTC; 2min 34s ago
       Docs: https://httpd.apache.org/docs/2.4/
   Main PID: 31392 (apache2)
      Tasks: 6 (limit: 2282)
     Memory: 11.2M
     CGroup: /system.slice/apache2.service
             ├─31392 /usr/sbin/apache2 -k start
             ├─31395 /usr/sbin/apache2 -k start
             ├─31396 /usr/sbin/apache2 -k start
             ├─31397 /usr/sbin/apache2 -k start
             ├─31398 /usr/sbin/apache2 -k start
             └─31399 /usr/sbin/apache2 -k start

Dec 14 02:22:20 devphp systemd[1]: Starting The Apache HTTP Server...
Dec 14 02:22:20 devphp apachectl[31378]: AH00558: apache2: Could not relia>
Dec 14 02:22:20 devphp systemd[1]: Started The Apache HTTP Server.

$ cd /var/www/html
$ ls
index.html
```

Dans un navigateur, aller à l'adresse 192.168.1.23 (adapter à votre adresse IP) et vérifier que vous avez accès à la page par défaut de Apache 2.

### Configuration PHP developpement

```
$ find / -name 'php.ini-development' -print 2> /dev/null
$ cd /etc/php/8.1/cli
$ ls
conf.d  php.ini
$ sudo cp /usr/lib/php/7.4/php.ini-development php.ini
$ vi php.ini
```

## Copie de fichiers entre poste de travail et VM

Sur poste de travail :
```bash
$ cd /c/code
$ git clone https://github.com/fabrice1618/flop-security.git
$ cd flop-security/webserver
$ ls
README.md  cheat.md  database.php  favicon.ico  home.php  index.php  log.php

$ scp *.* fab@192.168.1.22:
fab@192.168.1.22's password:
README.md                                     100%  514    51.9KB/s   00:00
cheat.md                                      100%  284   102.6KB/s   00:00
database.php                                  100% 3393     1.1MB/s   00:00
favicon.ico                                   100% 5430   749.4KB/s   00:00
home.php                                      100% 2852   664.5KB/s   00:00
index.php                                     100% 2744   631.4KB/s   00:00
log.php                                       100% 4441     1.1MB/s   00:00

```

Pour ma part, j'utilise l'editeur vi. Vous pouvez remplacer vi par nano qui est probablement plus simple dans un premier temps.

```bash
$ cd /var/www/html
$ ls -l
-rw-r--r-- 1 root root 10918 Sep 15 08:32 index.html
$ sudo rm index.html
$ sudo mv ~/* .

$ sudo vi phpinfo.php
[sudo] password for fab:
$ cat phpinfo.php
<?php

phpinfo();

```

### Connexion à partir de VS Code

- Installation de l'extension remote-SSH
- Ajout d'une connexion vers le serveur (^ shift P)ajouter un nouvel hôte SSH
- se connecter à l'hôte créé

### Mise en route de l'application flopsecurity

- Modifier la connexion BDD dans database.php en fonction des paramètres utilisés pendant l'installation
- 

### Premières requètes SQL

- Affichage du contenu de la table contenant les utilisateurs
- Déterminer la requète permettant d'afrficher les informations pour un utilisateur

### Test de la sécurité de l'application

- réaliser une injection SQL (SQLI)
- réaliser une attaque XSS

En option:
- réaliser un programme (par exemple en python) pour réaliser un brute-force sur le mot de passe d'un des utilisateurs

### Réalisation d'un audit de sécurité

Utilisation de Burp suite