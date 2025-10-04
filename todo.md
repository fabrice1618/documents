# Documents obsolètes

Cette liste répertorie les documents obsolètes du dépôt qui nécessitent une révision ou une suppression.

## Documents marqués comme obsolètes

### 1. `database/initiation_db - Copie.md`
**Raison** : Fichier dupliqué (copie de sauvegarde) - Le fichier original `initiation_db.md` existe déjà avec un contenu similaire mais une URL de serveur différente (mips.science vs fabricatic.fr). Cette copie devrait être supprimée.

### 2. `web/gulp.md`
**Raison** : Outil de build obsolète - Gulp (version 3.9.1 mentionnée) est largement remplacé par des outils modernes comme Vite, esbuild, ou Webpack 5. Les build tools modernes offrent de meilleures performances et une configuration plus simple. Ce document devrait être mis à jour avec des alternatives modernes ou archivé.

### 3. `linux/usecase/install_VM_devC.md`
**Raison** : Version Ubuntu obsolète - Le document utilise Ubuntu Server 20.04, qui bien que toujours supporté, est remplacé par des versions plus récentes (22.04 LTS, 24.04 LTS). Les instructions spécifiques à HyperV et les versions de compilateurs (gcc 9.3.0) sont datées.

### 4. `linux/usecase/install_VM_devPHP.md`
**Raison** : Configuration PHP/MySQL obsolète - Le document mentionne PHP 7.4 et MySQL 8.0.22 sur Ubuntu 20.04, qui sont des versions anciennes. PHP 7.4 n'est plus supporté depuis novembre 2022. Le document devrait être mis à jour pour PHP 8.x et Ubuntu 22.04/24.04.

### 5. `linux/win-docker-LAMP/install-dockerhost.md`
**Raison** : Docker Compose v1 obsolète - Le document utilise la syntaxe docker-compose v1 (version 1.26.2) qui est obsolète. Docker Compose v2 est maintenant intégré dans Docker CLI. Ubuntu Server 20.04 est également une version plus ancienne.

### 6. `linux/serveur_dev_web/serveur_developpement_web.md`
**Raison** : Architecture obsolète et versions dépassées - Utilise Ubuntu Server 21.04 (fin de support), PHP 7.4 (non supporté), MySQL 8.0.25, et une architecture de serveur rsyslog complexe qui pourrait être remplacée par des solutions modernes de logging (comme Docker avec stack ELK ou Loki). L'approche multi-VM pourrait être modernisée avec Docker Compose ou Kubernetes.

## Recommandations

- **Mettre à jour** : Les 5 autres documents avec les versions actuelles des logiciels et systèmes d'exploitation
