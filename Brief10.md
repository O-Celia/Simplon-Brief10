# Brief 10 : Superviser des containers

## Chapitre 1 : Déployer un cluster AKS
    
### Créer un cluster AKS avec 1 node

Connexion azure CLI
``az login``

Création d'un groupe de ressource
``az group create --name B110Celia --location westeurope``

Création du cluster AKS avec 1 nodes et la clé SSH de l'utilisateur dans le groupe de ressource
``az aks create -g B10Celia -n AKSCluster --node-count 1 --ssh-key-value ./.ssh/id_rsa.pub --enable-managed-identity -a ingress-appgw --appgw-name B10Gateway --appgw-subnet-cidr "10.225.0.0/16"``

Téléchargement des informations d’identification et configuration de l’interface de ligne de commande Kubernetes
``az aks get-credentials --resource-group B10Celia --name AKSCluster``

## Chapitre 2 : Déploiement de l'application de vote

Utilisation des fichiers yaml du Brief 7, avec image docker celiaoued/vote-app:1.0.113

## Chapitre 3 : Création de DNS pour l'application de vote

Création d’un enregistrement DNS pointant vers l’adresse IP de l’application de vote (cf MC_B10Celia_AKSCluster_westeurope) sur Gandi : http://vote.simplon-celia.space

## Chapitre 4 : Installation de Prometheus et Grafana via Helm

Installation de Helm
```consol
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

Ajout du repository pour Prometheus et Grafana, et mise à jour
```consol
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

Installation d'un Helm Chart dans un namespace monitoring
```consol
helm install prometheus \
  prometheus-community/kube-prometheus-stack \
  --namespace monitoring \
  --create-namespace
```

Vérification
``kubectl get all -n monitoring``

## Chapitre 5 : Connexion à Prometheus et Grafana

Exposer Grafana : 
``kubectl port-forward svc/prometheus-grafana -n monitoring 4000:80``
On obtient ``Forwarding from 127.0.0.1:4000 -> 3000``
Connexion à **127.0.0.1:4000** et entrée de l'identifiant et le mot de passe : **admin/prom-operator**

Exposer Prometheus : 
``kubectl port-forward svc/prometheus-kube-prometheus-prometheus -n monitoring 4001:9090``
On obtient : ``Forwarding from 127.0.0.1:4001 -> 9090``
Connexion à **127.0.0.1:4001**

## Chapitre 6 : Alertes

### Mémoire

Utilisé : container_memory_working_set_bytes
Total : kube_pod_container_resource_limits

Pourcentage de mémoire utilisé : 
**sum(container_memory_working_set_bytes) / sum(kube_pod_container_resource_limits) x 100**

![](https://hackmd.io/_uploads/rJoOhIeDh.png)
![](https://hackmd.io/_uploads/Byn53IxP2.png)
![](https://hackmd.io/_uploads/BkT3h8xP3.png)
![](https://hackmd.io/_uploads/r14rTUeD2.png)

### CPU

Utilisé : container_cpu_usage_seconds_total
Total : kube_pod_container_resource_limits

Pourcentage de CPU utilisé : 
**sum(rate(container_cpu_usage_seconds_total)) / 
sum(kube_pod_container_resource_limits) x 100**

![](https://hackmd.io/_uploads/SJ0YsUgv2.png)
![](https://hackmd.io/_uploads/HkKniLgv3.png)
![](https://hackmd.io/_uploads/B1Yk38xD3.png)
![](https://hackmd.io/_uploads/H15ZhIxwh.png)

## Chapitre 7 : Loki

Installation de Loki : 

```consol
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm upgrade --install promtail --namespace monitoring grafana/promtail
helm upgrade --install loki --namespace monitoring grafana/loki-stack
```

Home > Connection > Data Source > Add new data source > Loki
**URL** : http://loki.monitoring.svc.cluster.local:3100
(loki : nom du cluster, monitoring : namespace de grafana et prometheus ; 3100 : port de loki)

## Chapitre 8 : Dashboard Redis

Home > Connection > Data Source > Add new data source > Redis
**URL** : rediscluster.default.svc.cluster.local:6379
(rediscluster : nom du cluster, default : namespace de redis ; 6379 : port de redis)
Mot de passe : Configuration > Secrets

## Pour terminer, voir Artifact Hub
