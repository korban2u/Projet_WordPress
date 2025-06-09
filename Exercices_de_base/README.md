# WordPress Stack - Exercices de base

## Description

Ce projet déploie une stack WordPress en utilisant Docker Compose avec les services suivants :
- **WordPress** 
- **MariaDB** 
- **Redis**
- **phpMyAdmin** 


## Prérequis

- Docker >= 20.0
- Docker Compose >= 2.0
- Ports disponibles : 8080, 8081

## Installation et déploiement

### 1. Configurer les variables d'environnement

Créez un fichier `.env` avec les variables suivantes :

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

### 2. Lancer la stack

```bash
# Construire et démarrer tous les services
docker-compose up -d

# Vérifier le statut des conteneurs
docker-compose ps

# Consulter les logs
docker-compose logs
```

### 3. Arrêter la stack

```bash
# Arrêter les services
docker-compose down

# Arrêter et supprimer les volumes (perte de données)
docker-compose down -v
```

## Accès aux services

| Service | URL | Identifiants |
|---------|-----|--------------|
| **WordPress** | http://localhost:8080 | Configuration lors du premier accès |
| **phpMyAdmin** | http://localhost:8081 | root / `MYSQL_ROOT_PASSWORD` |


## Commandes utiles

### Gestion des conteneurs
```bash
# Redémarrer un service spécifique
docker-compose restart wordpress

# Accéder au shell d'un conteneur
docker-compose exec wordpress bash
docker-compose exec db bash

# Voir les logs d'un service
docker-compose logs -f wordpress
```

### Tests de connectivité
```bash
# Tester la connexion à MariaDB depuis WordPress
docker-compose exec wordpress mysql -h db -u wp_user -p

# Tester Redis depuis WordPress
docker-compose exec wordpress redis-cli -h redis ping
```

### Sauvegarde et restauration
```bash
# Sauvegarder la base de données
docker-compose exec db mysqldump -u root -p wordpress_db > backup.sql

# Restaurer la base de données
docker-compose exec -T db mysql -u root -proot_password wordpress_db < backup.sql
```

### Monitoring
```bash
# Surveiller l'utilisation des ressources
docker stats

# Inspecter le réseau
docker network ls
docker network inspect exercices_de_base_wordpress_network
```

## Sécurité

⚠️ **Cette configuration est adaptée pour le développement local uniquement**

**Mesures de sécurité implémentées :**
- Variables d'environnement dans fichier `.env`
- Réseau Docker isolé
- Limitation des ressources MariaDB

**Pour la production, ajoutez :**
- HTTPS avec certificats SSL
- Mots de passe complexes
- Utilisateurs non-root dans les conteneurs
- Chiffrement des communications inter-conteneurs


## Groupe

- Ryan Korban
- Baptiste Delaborde
