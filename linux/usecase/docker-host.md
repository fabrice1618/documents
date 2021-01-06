# Docker Host

## Configuration
- Poweredge 2850
- 2 * processeur 3 Ghz
- 4 cores
- 2 Go

```
$ cat /proc/cpuinfo | grep "model name"
model name      : Intel(R) Xeon(TM) CPU 3.00GHz
model name      : Intel(R) Xeon(TM) CPU 3.00GHz
model name      : Intel(R) Xeon(TM) CPU 3.00GHz
model name      : Intel(R) Xeon(TM) CPU 3.00GHz

$ egrep --color -i "svm|vmx" /proc/cpuinfo

```

## installation

Mise à jour système
```
$ sudo apt update
$ sudo apt upgrade
```
Firewall
```
$ sudo ufw enable
$ sudo ufw allow ssh
$ sudo ufw status
```
Installation docker
```
$ sudo apt-get update

$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common

$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
$ sudo apt-key fingerprint 0EBFCD88

$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

$ sudo apt-get update
$ sudo apt-get install docker-ce docker-ce-cli containerd.io
$ sudo docker run hello-world
```
Manage Docker as a non-root user
```
$ sudo groupadd docker
$ sudo usermod -aG docker fab
$ docker run hello-world
```
Installation de docker-compose
```
$ sudo apt install docker-compose
```
