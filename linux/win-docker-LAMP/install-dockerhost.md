# Installation dockerhost Ubuntu server 20.04
## Virtualbox: Création de la machine virtuelle
RAM 2 Go
Disque 10 Go
Réseau bridge
Installation ubuntu server 20.04
langue français
Update to the new installer
Clavier french
Config réseau IP v4: Manuel
(!dockerhost Ubuntu server 20.04_config_reseau.png)
Administrateur fab/ghjk
hostname: dockerhost
Install openssh server

## Premier démarrage du serveur

```
sudo apt update && sudo apt upgrade
$ sudo ufw enable
$ sudo ufw allow ssh
$ sudo ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip
To Action From
-- ------ ----
22/tcp ALLOW IN Anywhere
22/tcp (v6) ALLOW IN Anywhere (v6)
```

C:\Windows\System32\drivers\etc
ajouter dans hosts
192.168.0.222 dockerhost

Connexion au serveur dans git bash
```
ssh fab@192.168.0.222
$ ssh fab@dockerhost
```

## Installation de Docker
### Desinstalaltion anciennes versions
```
sudo apt-get remove docker docker-engine docker.io containerd runc
```
### Configuration du repository
```
sudo apt-get update
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo apt-key fingerprint 0EBFCD88
pub   rsa4096 2017-02-22 [SCEA]
      9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
sub   rsa4096 2017-02-22 [S]

sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

```

### Installation de docker et docker-compose
```
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
sudo docker run hello-world
sudo curl -L "https://github.com/docker/compose/releases/download/1.26.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

```

## Container MySQL Server

```
sudo mkdir /docker
sudo chown fab:fab /docker
cd /docker
mkdir mysqlserver
cd mysqlserver/
vi docker-compose.yml

# Use root/example as user/password credentials
version: '3.1'

services:

  db:
    image: mysql
    command: --default-authentication-plugin=mysql_native_password
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: example

  adminer:
    image: adminer
    restart: always
    ports:
      - 8080:8080

sudo ufw allow 3306
Rule added
Rule added (v6)

sudo ufw status
Status: active
To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere
3306                       ALLOW       Anywhere
22/tcp (v6)                ALLOW       Anywhere (v6)
3306 (v6)                  ALLOW       Anywhere (v6)


cat docker.cnf
[mysqld]
skip-host-cache
skip-name-resolve

cat config-server.cnf
[mysqld]
bind-address           = 0.0.0.0

mysql -uroot -p

CREATE USER 'dba'@'localhost' IDENTIFIED BY 'dba_password';
GRANT ALL PRIVILEGES ON *.* TO 'dba'@'localhost' WITH GRANT OPTION;
flush privileges;

CREATE USER 'dba'@'%' IDENTIFIED BY 'dba_password';
GRANT ALL PRIVILEGES ON *.* TO 'dba'@'%' WITH GRANT OPTION;
flush privileges;

select host,user from mysql.user;
show grants for dba@localhost;

vi config-server.cnf

[mysqld]
bind-address           = 0.0.0.0

# Logging
log_output             = TABLE

# Le log des erreurs est activé
#log_error              = /var/log/mysql-error.log

# Le log general est defini mais non activé (=0)
# Il est activé uniquement si necessaire
#general_log_file       = /var/log/mysql.log
general_log            = 0

# Le log des requetes lentes est activé
slow_query_log         = 1
#slow_query_log_file    = /var/log/mysql/mysql-slow.log
long_query_time = 2
log-queries-not-using-indexes
log_slow_admin_statements

```
## Création du container Apache + PHP 8 (version rc3)

```
cd /docker
git clone https://github.com/fabrice1618/parcours.git

```

/usr/local/etc/php/conf.d



### Copie du site dans le container

```
scp -r -p * fab@dockerhost:/docker/parcours/html
scp -r -p .htaccess fab@dockerhost:/docker/parcours/html
scp -r -p .gitignore fab@dockerhost:/docker/parcours/html

```