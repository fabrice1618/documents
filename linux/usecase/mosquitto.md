# Broker MQTT - Mosquitto

Configuration : Linux Ubuntu 22.04 server

## Installation 

```bash
$ apt install mosquitto
```

## Installation sur docker

> Voir repository https://github.com/fabrice1618/m5_mqtt

2 containers : 

- container mosquitto sur le port 1883
- container python avec client mqtt (paho-mqtt)

Création sous domaine : paramétrage DNS

mqtt IN A xxx.xxx.xxx.xxx

Configuration routeur : Redirections de ports

Nom | IP source | Port début-fin | Protocole | IP de destination | Port interne début-fin
-----|-----|-----|-----|-----|-----
web | | 80-80 | TCP | 192.168.1.4 | 80-80
ssh | | 1618-1618 | TCP | 192.168.1.4 | 22-22
mqtt | | 1883-1883 | TCP | 192.168.1.4 | 1883-1883

