# WordPress Stack - Défis 1, 2 et 3

## Description

Stack WordPress avec :
- **2 instances WordPress** (clustering)
- **Nginx** (reverse proxy + load balancer)
- **Prometheus + Grafana** (monitoring)
- **MariaDB**, **Redis**, **phpMyAdmin**

## Prérequis

- Docker >= 20.0
- Docker Compose >= 2.0
- Ports disponibles : 80, 3000, 8080, 8081, 9090, 9100

## Installation

### 1. Créer le fichier `.env`
```env
# Base de données
MYSQL_ROOT_PASSWORD=root_password
MYSQL_DATABASE=wordpress_db
MYSQL_USER=wp_user
MYSQL_PASSWORD=wp_password

# WordPress
WORDPRESS_DB_HOST=db:3306
WORDPRESS_DB_NAME=wordpress_db
WORDPRESS_DB_USER=wp_user
WORDPRESS_DB_PASSWORD=wp_password

# Ports
WORDPRESS_PORT=8080
PHPMYADMIN_PORT=8081 

```

### 2. Lancer
```bash
docker-compose up -d
```

## Accès aux services

| Service | URL | Identifiants | Description |
|---------|-----|--------------|-------------|
| **WordPress** | http://localhost | Configuration initiale | Site principal via Nginx |
| **phpMyAdmin** | http://localhost:8081 | root / `MYSQL_ROOT_PASSWORD` | Administration BDD |
| **Grafana** | http://localhost:3000 | admin / admin | Dashboards monitoring |
| **Prometheus** | http://localhost:9090 | - | Métriques et requêtes |
| **cAdvisor** | http://localhost:8083 | - | Monitoring conteneurs |
| **Node Exporter** | http://localhost:9100/metrics | - | Métriques système |


## Défis implémentés

### Défi 1 : Reverse Proxy
- Nginx devant WordPress sur le port 80
- Configuration dans `nginx/nginx.conf`

### Défi 2 : Monitoring
- **Prometheus** : collecte les métriques
- **Grafana** : visualisation
- **cAdvisor** : monitoring des conteneurs
- **Node Exporter** : métriques système

### Défi 3 : Clustering
- 2 instances WordPress (wordpress1 et wordpress2)
- Load balancing avec Nginx (round-robin)
- Volume partagé pour les fichiers WordPress

## Test du load balancing

```bash
# Voir les logs des 2 WordPress
docker-compose logs -f wordpress1 wordpress2

# Dans un autre terminal, envoyer des requêtes
for i in {1..10}; do curl http://localhost; done

# Les requêtes doivent être distribuées entre les 2 instances
```

## Configuration Grafana

1. Aller sur http://localhost:3000
2. Se connecter avec admin/admin
3. Ajouter Prometheus comme data source : `http://prometheus:9090`
4. Importer un dashboard (ex: ID 1860 pour Docker monitoring)

## Commandes utiles

```bash
# Voir tous les conteneurs
docker-compose ps

# Arrêter une instance pour tester la haute dispo
docker-compose stop wordpress1

# Tout arrêter
docker-compose down
```

## Sécurité 

⚠️ **Configuration développement - À adapter pour la production**

**Mesures à prendre pour la production :**

1. **HTTPS avec Nginx** :
```nginx
server {
    listen 443 ssl;
    ssl_certificate /path/to/certificate.crt;
    ssl_certificate_key /path/to/private.key;
    # ... configuration SSL
}
```

2. **Authentification Prometheus/Grafana** :
   - Reverse proxy avec authentification
   - Restriction par IP
   - LDAP/OAuth integration


## Groupe

- Ryan Korban
- Baptiste Delaborde
