# Déploiement d'une Application de Vote sur Kubernetes avec Monitoring

Ce document explique les étapes nécessaires pour déployer une application de vote sur un cluster Kubernetes (AKS), avec des services comme Redis et un système de monitoring basé sur Prometheus, Grafana, et Loki.

## 1. Création et Configuration d'un Cluster AKS

Créer un cluster AKS :
- Utiliser Azure CLI pour créer un groupe de ressources et un cluster AKS.
- Configurer un nœud avec une clé SSH et activez l'ingress via Azure Application Gateway.
Configurer Kubernetes CLI :
- Télécharger les informations d'identification avec az aks get-credentials.

## 2. Déploiement de l'Application de Vote

Fichiers YAML nécessaires :
- Déploiement des pods : Configurer les pods pour Redis et l'application de vote.
- Services : Configurer les services pour exposer Redis et l'application.
- Ingress : Configurer un ingress pointant vers le service de vote.
- Autoscaling : Implémenter un autoscaler horizontal pour le déploiement de vote.
- Volumes persistants : Utiliser une StorageClass Azure pour Redis.

Image Docker :
- Déployer l'image Docker celiaoued/vote-app:TAG.

Variables d'environnement :
- Configurer des secrets pour sécuriser les mots de passe.

## 3. Configuration DNS

Création d’un enregistrement DNS :
- Configurer un domaine via Gandi pour rendre l’application accessible publiquement, par exemple : http://vote.simplon-celia.space.

## 4. Monitoring avec Prometheus et Grafana

Installation de Helm :
- Installer Helm pour gérer les chartes.

Déploiement Prometheus et Grafana :
- Installer le Helm Chart Prometheus/Grafana dans un namespace nommé monitoring.
- Vérifier les ressources déployées dans le namespace.

Connexion à Grafana et Prometheus :
- Configurer des redirections de ports pour accéder aux interfaces via votre navigateur.

## 5. Configuration des Alertes

Alertes pour le CPU et la mémoire :
- Configurer des métriques pour surveiller l’utilisation des ressources comme :
    - Mémoire utilisée : container_memory_working_set_bytes.
    - CPU utilisé : container_cpu_usage_seconds_total.
- Configurer des pourcentages pour définir les seuils d'alertes.

## 6. Ajout de Loki pour le Logging

Installation :
- Déployez Loki et Promtail via Helm pour la collecte et la gestion des logs.
Intégration dans Grafana :
- Ajoutez Loki comme source de données dans Grafana.

## 7. Configuration d’un Dashboard Redis

Ajout de Redis comme source de données dans Grafana :
- Configurer un dashboard pour monitorer Redis en utilisant les informations du cluster Redis (nom, namespace, port).
- Ajouter le mot de passe depuis les secrets.

## 8. Résumé des Points Clés

Infrastructure :
- Déploiement sur AKS avec un nœud.
- Services exposés via ingress et DNS public.
Application :
- Vote app avec Redis comme backend.
- Autoscaling pour gérer la charge.
Monitoring :
- Prometheus pour les métriques.
- Grafana pour les dashboards.
- Loki pour les logs.
- Alertes configurées pour CPU/mémoire.

## Outils et Technologies Utilisés

- Kubernetes (AKS).
- Azure CLI pour la gestion des ressources.
- Docker pour l’image de l’application.
- Helm pour l’installation des outils de monitoring.
- Prometheus, Grafana et Loki pour la surveillance et les logs.
- Gandi pour la configuration DNS.

## Note
Toutes les configurations sensibles comme les mots de passe et les clés doivent être gérées via des secrets Kubernetes.
