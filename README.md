# Projet WordPress avec Docker

## Description

Déploiement d'une application WordPress multi-conteneurs avec Docker Compose


## Structure du projet

Le projet est organisé en deux dossiers principaux :

### 📁 Exercices_de_base/
Contient l'implémentation de base de la stack WordPress avec :
- WordPress
- MariaDB
- Redis
- phpMyAdmin

👉 **Voir le [README des exercices de base](./Exercices_de_base/README.md)** pour les instructions de déploiement et d'utilisation.

### 📁 Defis_Sup_1_2_3/
Contient l'implémentation avancée avec les défis supplémentaires :
- Défi 1 : Reverse Proxy avec Nginx
- Défi 2 : Monitoring avec Prometheus et Grafana
- Défi 3 : Clustering avec plusieurs instances WordPress

👉 **Voir le [README des défis](./Defis_Sup_1_2_3/README.md)** pour les instructions détaillées.

## Documents

- 📄 **Rapport_TDWordPress_RyanKorban_BaptisteDelaborde.pdf** : Rapport avec les réponses du TP

## Prérequis généraux

- Docker >= 20.0
- Docker Compose >= 2.0
- Git


```bash
# Pour les exercices de base
cd Exercices_de_base
# Suivre le README local

# Pour les défis avancés
cd Defis_Sup_1_2_3
# Suivre le README local
```

## Auteurs

- Ryan Korban
- Baptiste Delaborde
