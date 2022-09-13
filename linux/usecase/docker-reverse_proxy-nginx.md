# Reverse Proxy - nginx

## Configuration
- poweredge

## Documentation

- https://www.atlantic.net/dedicated-server-hosting/how-to-deploy-nginx-reverse-proxy-in-docker-on-ubuntu-20-04/
- https://www.freecodecamp.org/news/docker-nginx-letsencrypt-easy-secure-reverse-proxy-40165ba3aee2/
- https://phoenixnap.com/kb/docker-nginx-reverse-proxy
- https://www.theserverside.com/blog/Coffee-Talk-Java-News-Stories-and-Opinions/Docker-Nginx-reverse-proxy-setup-example
- https://www.grottedubarbu.fr/docker-nginx-reverse-proxy/
- https://www.it-connect.fr/configurer-nginx-en-tant-que-reverse-proxy/
- https://www.bogotobogo.com/DevOps/Docker/Docker_Nginx_WebServer.php
- https://openclassrooms.com/fr/courses/1733551-gerez-votre-serveur-linux-et-ses-services/5236056-securisez-votre-serveur-web
- https://openclassrooms.com/fr/courses/1733551-gerez-votre-serveur-linux-et-ses-services/5236081-mettez-en-place-un-reverse-proxy-avec-nginx


## Installation serveur web netsim
```
$ cd /data/docker/
$ mkdir netsim
$ cd netsim
```
Configuration
```
$ git clone https://github.com/fabrice1618/netsim.git netsim

```

## Installation serveur web dbintro


## installation reverse proxy

```
$ mkdir reverseproxy
$ cd reverseproxy

$ docker network create reverseproxy



/data/docker/reverseproxy/conf.d$ cp /data/docker/netsim/netsim-reverseproxy.conf.old .
```

/etc/nginx/conf.d
