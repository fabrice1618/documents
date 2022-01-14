# TP : Installation de wordpress sur un serveur de test

## Vue d'ensemble

Nous allons installer wordpress sur un serveur Linux Ubuntu 20.04 LTS. Nous allons faire une installation directement sur le serveur, contrairement à une installation dans des containers docker. 

Pour notre manipulation, nous utiliserons une VM qui sera très proche d'une configuration de serveur privé ou VPS hébergé sur internet. 

Attention: nous ne mettrons pas en place une configuration de sécurité complète. Cette configuration sera adaptée pour un réseau local, par exemple comme serveur de pre-prod ou de test.


## Installation VM de test

Installation de la VM Linux ubuntu server 20.04 dans virtualbox

    - Wordpress ubuntu 20.04 TP
    - penser à ajouter openssh pendant installation
    - configurer le clavier suivant le matériel disponible.
    - RAM: 2GB
    - Disque: 20 GB
    - Réseau: accès par pont
    - hostname: wordpress01
    - penser à modifier la configuration du réseau pour une IP fixe si possible.

Post-installation - Mise à jour du système:
```
$ sudo apt update
$ sudo apt upgrade
```

Vérification du réseau:
```
$ ip a ( adresse IP 192.168.1.23)
$ ping 192.168.1.1 (Connexion avec le réseau local OK )
```

Configuration firewall et vérification que les ports SSH et MySql sont ouverts.
```
$ sudo ufw status
$ sudo ufw enable
$ sudo ufw allow ssh
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

Vous pouvez vous connecter à partir de votre machine en utilisant un terminal type putty ou directement dans git bash, comme ci-dessous:
```
$ ssh fab@192.168.1.23
The authenticity of host '192.168.1.23 (192.168.1.23)' can't be established.
ECDSA key fingerprint is SHA256:mAeWaYu84BnLjIsyX5TypJAGwXUrnsv8WhJpOur/Wh8.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.1.23' (ECDSA) to the list of known hosts.
fab@192.168.1.23's password:
Welcome to Ubuntu 20.04.1 LTS (GNU/Linux 5.4.0-58-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Mon Dec 14 00:22:54 UTC 2020

  System load:  0.0                Processes:               117
  Usage of /:   21.9% of 18.57GB   Users logged in:         1
  Memory usage: 13%                IPv4 address for enp0s3: 192.168.1.23
  Swap usage:   0%
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

### Activation des logs

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

$ systemctl restart mysql.service

$ cd /var/log/mysql
$ ls -ltr
-rw-r----- 1 mysql mysql  178 Dec 14 00:55 query.log
-rw-r----- 1 mysql mysql  178 Dec 14 00:55 mysql-slow.log
-rw-r----- 1 mysql adm   5929 Dec 14 00:55 error.log
```

### Configuration des utilisateurs de MySQL

Habituellement, nous n'utilisons par le user root pour se connecter à la base de données. C'est un choix pas très sûr de conserver les accès root sur le serveur MySQL. Pour cet exercice, nous conserverons les accès root dans un premier temps. Puis à la fin de la configuration, nous sécuriserons l'installation de MySQL et nous désactiverons l'accès root.

Pour réaliser l'administration des bases de données, nous utilisons un user spécifique pour cette tâche généralement appelé dba (DataBase Administrator). Cet utilisateur doit avoir tous les accès sur toutes les bases de données et tous les privillèges. En général, les modifications de structure de la base de données sont faites avec cet utilisateur, en plus des sauvegardes et des restaurations.

Un autre utilisateur est créé pour se connecter à la base de données pour le compte de l'application. Pour améliorer la sécurité, on limite les privilèges de cet utilisateur. Cet utilisateur ne peut pas utiliser les autres bases de données présentes sur le serveur.

Création de l'utilisateur dba dans mysql:
```
$ sudo mysql

mysql> CREATE USER 'dba'@'localhost' IDENTIFIED BY 'ghjk';
mysql> GRANT ALL PRIVILEGES ON *.* TO 'dba'@'localhost' WITH GRANT OPTION;
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

### Création de la base de données wordpress et de l'utilisateur associé

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

mysql> create database wordpress;
Query OK, 1 row affected (0.11 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| wordpress          |
+--------------------+
5 rows in set (0.00 sec)


mysql> CREATE USER 'wordpress'@'localhost' IDENTIFIED BY 'abcd';
mysql> GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpress'@'localhost' WITH GRANT OPTION;
Query OK, 0 rows affected (0.00 sec)

mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW GRANTS FOR 'wordpress'@'localhost';
+------------------------------------------------------------------------------------+
| Grants for wordpress@localhost                                                     |
+------------------------------------------------------------------------------------+
| GRANT USAGE ON *.* TO `wordpress`@`localhost`                                      |
| GRANT ALL PRIVILEGES ON `wordpress`.* TO `wordpress`@`localhost` WITH GRANT OPTION |
+------------------------------------------------------------------------------------+
2 rows in set (0.00 sec)

mysql> exit;
```
Test de la connexion de l'utilisateur wordpress à la base de données wordpress
```
$ mysql -u wordpress -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 8.0.22-0ubuntu0.20.04.3 (Ubuntu)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use wordpress;
Database changed
mysql> show tables;
Empty set (0.01 sec)
mysql> exit;
```

### Sauvegarde des bases de données

Sauvegarde de toutes les bases de données
```
$ mysqldump -u dba -p --all-databases | gzip -9 > all-databases.tar.gz
Enter password:

$ ls -ltr
-rw-rw-r-- 1 fab fab 241440 Dec 14 01:56 all-databases.tar.gz

$ zcat all-databases.tar.gz
```

Sauvegarde de la base de données wordpress
```
$ mysqldump -u dba -p wordpress | gzip -9 > wordpress.tar.gz
Enter password:
$ ls -ltr
-rw-rw-r-- 1 fab fab 241440 Dec 14 01:56 all-databases.tar.gz
-rw-rw-r-- 1 fab fab    458 Dec 14 02:00 wordpress.tar.gz

$ zcat wordpress.tar.gz
```

## Installation apache 2 et PHP 7

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

Dec 14 02:22:20 wordpress01 systemd[1]: Starting The Apache HTTP Server...
Dec 14 02:22:20 wordpress01 apachectl[31378]: AH00558: apache2: Could not relia>
Dec 14 02:22:20 wordpress01 systemd[1]: Started The Apache HTTP Server.

$ cd /var/www/html
$ ls
index.html
```

Dans un navigateur, aller à l'adresse 192.168.1.23 et vérifier que vous avez accès à la page par défaut de Apache 2.

## Installation de wordpress

```
$ cd ~
$ wget http://wordpress.org/latest.zip
--2020-12-14 02:42:02--  http://wordpress.org/latest.zip
Resolving wordpress.org (wordpress.org)... 198.143.164.252
Connecting to wordpress.org (wordpress.org)|198.143.164.252|:80... connected.
HTTP request sent, awaiting response... 301 Moved Permanently
Location: https://wordpress.org/latest.zip [following]
--2020-12-14 02:42:02--  https://wordpress.org/latest.zip
Connecting to wordpress.org (wordpress.org)|198.143.164.252|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 16528923 (16M) [application/zip]
Saving to: ‘latest.zip’

latest.zip          100%[===================>]  15.76M  10.5MB/s    in 1.5s

2020-12-14 02:42:04 (10.5 MB/s) - ‘latest.zip’ saved [16528923/16528923]

$ sudo apt-get install zip
$ unzip latest.zip

$ ls -ltr
drwxr-xr-x 5 fab fab     4096 Dec  8 22:13 wordpress
-rw-rw-r-- 1 fab fab 16528923 Dec  8 22:14 latest.zip
-rw-rw-r-- 1 fab fab   241440 Dec 14 01:56 all-databases.tar.gz
-rw-rw-r-- 1 fab fab      458 Dec 14 02:00 wordpress.tar.gz

$ cd wordpress/
$ ls
index.php        wp-blog-header.php    wp-includes        wp-settings.php
license.txt      wp-comments-post.php  wp-links-opml.php  wp-signup.php
readme.html      wp-config-sample.php  wp-load.php        wp-trackback.php
wp-activate.php  wp-content            wp-login.php       xmlrpc.php
wp-admin         wp-cron.php           wp-mail.php
$ pwd
/home/fab/wordpress

$ cd /var/www/html
$ sudo rm index.html

$ sudo cp -R /home/fab/wordpress/* ./
$ ls -ltr
-rw-r--r--  1 root root  7278 Dec 14 02:52 readme.html
-rw-r--r--  1 root root 19915 Dec 14 02:52 license.txt
-rw-r--r--  1 root root   405 Dec 14 02:52 index.php
-rw-r--r--  1 root root  7101 Dec 14 02:52 wp-activate.php
-rw-r--r--  1 root root  2913 Dec 14 02:52 wp-config-sample.php
-rw-r--r--  1 root root  2328 Dec 14 02:52 wp-comments-post.php
-rw-r--r--  1 root root   351 Dec 14 02:52 wp-blog-header.php
drwxr-xr-x  9 root root  4096 Dec 14 02:52 wp-admin
-rw-r--r--  1 root root  3939 Dec 14 02:52 wp-cron.php
drwxr-xr-x  4 root root  4096 Dec 14 02:52 wp-content
-rw-r--r--  1 root root  8509 Dec 14 02:52 wp-mail.php
-rw-r--r--  1 root root 49831 Dec 14 02:52 wp-login.php
-rw-r--r--  1 root root  3300 Dec 14 02:52 wp-load.php
-rw-r--r--  1 root root  2496 Dec 14 02:52 wp-links-opml.php
drwxr-xr-x 25 root root 12288 Dec 14 02:52 wp-includes
-rw-r--r--  1 root root  3236 Dec 14 02:52 xmlrpc.php
-rw-r--r--  1 root root  4747 Dec 14 02:52 wp-trackback.php
-rw-r--r--  1 root root 31337 Dec 14 02:52 wp-signup.php
-rw-r--r--  1 root root 20975 Dec 14 02:52 wp-settings.php

$ cd ..
$ sudo chown -R www-data:www-data html

```
## Configuration de wordpress

Retourner sur la page web à l'adresse 192.168.1.23. Cette fois c'est l'installation de wordpress qui démarre.

Configuration de la base de données dans wordpress:

- Nom de la base de données: wordpress
- Identifiant: wordpress
- Mot de passe: abcd
- Adresse de la base de données:  localhost
- Préfixe des tables: wp_


- Titre du site: TP wordpress
- Identifiant: fab
- Mot de passe: fab
- Votre adresse de messagerie
- Visibilité par les moteurs de recherche: non


Accès à la page d'admin du site wordpress: http://192.168.1.23/wp-admin/

## Accès à votre site wordpress

http://192.168.1.23/

## Sauvegarde site wordpress

Sauvegarde de la base de données wordpress
```
$ mysqldump -u dba -p wordpress | gzip -9 > wordpress2.tar.gz
Enter password:
$ ls -ltr
-rw-rw-r-- 1 fab fab 241440 Dec 14 01:56 all-databases.tar.gz
-rw-rw-r-- 1 fab fab    458 Dec 14 02:00 wordpress.tar.gz
-rw-rw-r-- 1 fab fab  11701 Dec 14 03:18 wordpress2.tar.gz

$ zcat wordpress2.tar.gz
```

Sauvegarde des fichiers du site web
```
$ cd ~
$ tar cvzf wordpress_site.tar.gz /var/www/html
```

## Sécurisation

Consulter les documentations fournies par l'ANSSI
