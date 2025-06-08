# WordPress Stack - Défis supplémentaires (Monitoring & Reverse Proxy)

## Description

Cette version intègre un reverse proxy Nginx et une solution de monitoring avec Prometheus et Grafana. Elle comprend tous les services de base plus :

**Services de base :**
- **WordPress**
- **MariaDB**
- **Redis**
- **phpMyAdmin**

**Services avancés :**
- **Nginx** 
- **Prometheus** 
- **Grafana** 
- **cAdvisor**
- **Node Exporter** 


## Prérequis

- Docker >= 20.0
- Docker Compose >= 2.0
- Ports disponibles : 80, 3000, 8080, 8081, 9090, 9100

## Structure du projet

```
Defis_Sup_1_ET_2/
├── docker-compose.yml          # Configuration complète des services
├── wordpress/
│   ├── Dockerfile             # Image WordPress personnalisée
│   └── php.ini               # Configuration PHP
├── nginx/
│   └── nginx.conf            # Configuration reverse proxy
├── prometheus/
│   └── prometheus.yml        # Configuration Prometheus
├── volumes/                  # Données persistantes
│   ├── wordpress/
│   ├── mariadb/
│   ├── redis/
│   ├── prometheus/
│   └── grafana/
└── .env                     # Variables d'environnement
```

## Installation et déploiement

### 1. Préparer l'environnement

```bash
cd Defis_Sup_1_ET_2/
```

### 2. Configurer les variables d'environnement

Créez un fichier `.env` :

```env
# Base de données
MYSQL_ROOT_PASSWORD=votre_mot_de_passe_root_securise
MYSQL_DATABASE=wordpress_db
MYSQL_USER=wp_user
MYSQL_PASSWORD=votre_mot_de_passe_wp_securise

# WordPress
WORDPRESS_DB_HOST=db:3306
WORDPRESS_DB_NAME=wordpress_db
WORDPRESS_DB_USER=wp_user
WORDPRESS_DB_PASSWORD=votre_mot_de_passe_wp_securise
```

### 3. Lancer la stack complète

```bash
# Construire et démarrer tous les services
docker-compose up -d

# Vérifier le statut de tous les conteneurs
docker-compose ps

# Suivre les logs de démarrage
docker-compose logs -f
```

### 4. Vérification du déploiement

```bash
# Vérifier que tous les services répondent
curl -I http://localhost          # Nginx → WordPress
curl -I http://localhost:3000     # Grafana
curl -I http://localhost:9090     # Prometheus
curl -I http://localhost:8080     # cAdvisor
curl -I http://localhost:9100/metrics  # Node Exporter
```

## Accès aux services

| Service | URL | Identifiants | Description |
|---------|-----|--------------|-------------|
| **WordPress** | http://localhost | Configuration initiale | Site principal via Nginx |
| **phpMyAdmin** | http://localhost:8081 | root / `MYSQL_ROOT_PASSWORD` | Administration BDD |
| **Grafana** | http://localhost:3000 | admin / admin | Dashboards monitoring |
| **Prometheus** | http://localhost:9090 | - | Métriques et requêtes |
| **cAdvisor** | http://localhost:8080 | - | Monitoring conteneurs |
| **Node Exporter** | http://localhost:9100/metrics | - | Métriques système |

## Configuration des services avancés

### Nginx (Reverse Proxy)
- **Rôle** : Point d'entrée unique pour WordPress
- **Configuration** : `nginx/nginx.conf`

### Prometheus (Collecte de métriques)
- **Cibles configurées** :
  - Prometheus lui-même (localhost:9090)
  - Node Exporter (node-exporter:9100)
  - cAdvisor (cadvisor:8080)
- **Intervalle de collecte** : 15 secondes
- **Stockage** : Volume persistant dans `volumes/prometheus/`

### Grafana (Visualisation)
- **Mot de passe admin par défaut** : `admin`
- **Configuration recommandée** :
  1. Ajouter Prometheus comme source de données : `http://prometheus:9090`
  2. Importer des dashboards prédéfinis pour Docker

### cAdvisor (Monitoring conteneurs)
- **Métriques fournies** :
  - Utilisation CPU/RAM par conteneur
  - I/O réseau et disque
  - Statistiques de performance

### Node Exporter (Métriques système)
- **Métriques fournies** :
  - CPU, mémoire, disque de l'hôte
  - Statistiques réseau
  - Informations système

## Commandes de gestion

### Gestion globale
```bash
# Redémarrer toute la stack
docker-compose restart

# Arrêter proprement tous les services
docker-compose down

# Mise à jour des images
docker-compose pull
docker-compose up -d
```

### Monitoring spécifique
```bash
# Vérifier les métriques Prometheus
curl http://localhost:9090/api/v1/targets

# Tester les endpoints de métriques
curl http://localhost:9100/metrics | head -20
curl http://localhost:8080/metrics | head -20

# Logs des services de monitoring
docker-compose logs prometheus
docker-compose logs grafana
```

### Gestion Nginx
```bash
# Recharger la configuration Nginx
docker-compose exec nginx nginx -s reload

# Tester la configuration
docker-compose exec nginx nginx -t

# Voir les logs d'accès
docker-compose logs nginx
```

## Configuration Grafana

### 1. Premier accès
1. Accédez à http://localhost:3000
2. Connectez-vous avec `admin/admin`
3. Changez le mot de passe

### 2. Ajouter Prometheus comme source de données
1. Configuration → Data Sources → Add data source
2. Sélectionnez "Prometheus"
3. URL : `http://prometheus:9090`
4. Cliquez "Save & Test"

### 3. Dashboards recommandés
```bash
# IDs de dashboards Grafana.com à importer :
# - Docker Container & Host Metrics : 10619
# - Docker and system monitoring : 893
# - Node Exporter Full : 1860
```

## Monitoring et alertes

### Métriques clés à surveiller

**Conteneurs (cAdvisor) :**
- `container_memory_usage_bytes` : Utilisation mémoire
- `container_cpu_usage_seconds_total` : Utilisation CPU
- `container_network_receive_bytes_total` : Trafic réseau

**Système (Node Exporter) :**
- `node_memory_MemAvailable_bytes` : Mémoire disponible
- `node_cpu_seconds_total` : Utilisation CPU système
- `node_filesystem_free_bytes` : Espace disque libre

**WordPress spécifique :**
- Temps de réponse via Nginx
- Statut des conteneurs
- Utilisation des volumes

### Requêtes Prometheus utiles
```promql
# Top 5 conteneurs par utilisation mémoire
topk(5, container_memory_usage_bytes{name!=""})

# Utilisation CPU par conteneur
rate(container_cpu_usage_seconds_total{name!=""}[5m]) * 100

# Espace disque libre
(node_filesystem_avail_bytes * 100) / node_filesystem_size_bytes
```

## Sécurité

⚠️ **Configuration développement - À adapter pour la production**

**Mesures supplémentaires pour la production :**

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
