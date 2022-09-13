# Installation VM de développement C / C++

## Installation VM avec Ubuntu desktop 22.04

- 2 cores
- RAM 4 Go
- HDD 15 Go (25 Go recommandés)
- installation minimale
- Après redemarrage, mettre à jour le système
- Configuration réseau bridge

## Installation des applications et environement de développement

- Install VS Code (applet logiciels ubuntu)

- Install net-tools
```
$ sudo apt update
$ sudo apt install net-tools
```

- Install openssh server
```
$ sudo apt update
$ sudo apt install openssh-server
$ sudo systemctl status sshd
$ netstat -tulp
```

- Installation compilateur gcc et debugger gdb
```
$ sudo apt install build-essential
$ gcc --version
$ g++ --version
$ gdb --version
```

- Install client et serveur MySQL - configuration dev et admin dba
```
$ sudo apt install mysql-server
$ mysql --version
$ cd /etc/mysql/mysql.conf.d
$ sudo vi mysqld.cnf
$ sudo systemctl restart mysql.service 
$ ls /var/log/mysql/
error.log  mysql-slow.log  query.log
```

mysqld.conf

```
general_log_file        = /var/log/mysql/query.log
general_log             = 1
log_error = /var/log/mysql/error.log
slow_query_log          = 1
slow_query_log_file     = /var/log/mysql/mysql-slow.log
long_query_time = 2
log-queries-not-using-indexes
```

Utilisateur dba

```
$ sudo mysql

mysql> CREATE USER 'dba'@'localhost' IDENTIFIED BY 'ghjk';
mysql> GRANT ALL PRIVILEGES ON *.* TO 'dba'@'localhost' WITH GRANT OPTION;
mysql> FLUSH PRIVILEGES;
mysql> SHOW GRANTS FOR 'dba'@'localhost';
mysql> exit;

$ sudo mysql -u dba -p
mysql> SHOW DATABASES;

```
