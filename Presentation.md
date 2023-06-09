---
title: Brief10 - Presentation
tags: presentation
slideOptions:
  theme: moon
  transition: 'fade'
  spotlight:
    enabled: true
---

# Brief 10 : Superviser des containers

---

## Objectifs et ressources

----

### Objectifs

- Collecter les metrics et les logs des containers faisant tourner l’application de vote
- Collecter les metrics et les logs des containers faisant tourner redis

----

### Ressources

**Prometheus** : Outil de monitoring des métriques de fonctionnement des serveurs et générateur d'alertes en fonction de seuils considérés critiques
**Grafana** : Outil de visualisation de données qui permet de réaliser des tableaux de bord et des graphiques à partir des données de Prometheus
**Loki** : Outil d'aggrégation des logs de Grafana

----

### Ressources réutilisées

- Kubernetes-déploiement de l'application : Brief 6
![](https://i.imgur.com/M7Piqz6.png =850x)

----

### Modification des ressources

```consol
spec:
  ...
    spec:
      containers:
      - name: vote
        ...
        resources:
          requests:
            cpu: "100m"
            memory: "500Mi"
          limits:
            cpu: "200m"
            memory: "1000Mi"
```

----

### Modification des ressources

```consol
spec:
  ...
    spec:
      containers:
      - name: redis
        ...
        resources:
          requests:
            cpu: "100m"
            memory: "50Mi"
          limits:
            cpu: "200m"
            memory: "100Mi"
```

---

## Prometheus, Grafana et Loki

----

### Installation

- Utilisation de Helm
- Prometheus et Grafana : https://github.com/prometheus-community/helm-charts
- Loki : https://grafana.github.io/helm-charts

----

### Alertes

Pour Redis et Vote : 

- Warning si l’utilisation de la mémoire alloué est supérieur à 70% 
- Alerte critique si l’utilisation de la mémoire alloué est supérieur à 85%
- Warning si l’utilisation du CPU est supérieur à 75%
- Alerte critique si l’utilisation du CPU est supérieur à 85%

----

### Mémoire

Pourcentage de mémoire utilisé : 
**sum(container_memory_working_set_bytes) / sum(kube_pod_container_resource_limits) x 100**

![](https://hackmd.io/_uploads/rJoOhIeDh.png)

----

![](https://hackmd.io/_uploads/Byn53IxP2.png)

----

![](https://hackmd.io/_uploads/BkT3h8xP3.png)

----

![](https://hackmd.io/_uploads/r14rTUeD2.png)

----

### CPU

Pourcentage de CPU utilisé : 
**sum(rate(container_cpu_usage_seconds_total)) / 
sum(kube_pod_container_resource_limits) x 100**

![](https://hackmd.io/_uploads/SJ0YsUgv2.png)

----

![](https://hackmd.io/_uploads/HkKniLgv3.png)

----

![](https://hackmd.io/_uploads/B1Yk38xD3.png)

----

![](https://hackmd.io/_uploads/H15ZhIxwh.png)

----

### Dashboard de l'application de vote

![](https://hackmd.io/_uploads/rJCApUew3.png)

----

### Dashboard de Redis

![](https://hackmd.io/_uploads/HJuNRIlw3.png)

----

### Redis : Dashboard supplémentaire

![](https://hackmd.io/_uploads/rJJu0LlD2.png)

---

## Envoi d'alertes

----

### Discord

![](https://hackmd.io/_uploads/SkYiyPevh.png)

Test : Horizontal autoscaling des pods 
Nombre de pods > 1

----

### Mail

Manque de temps

![](https://hackmd.io/_uploads/SyGC4Pew2.png)

----

![](https://hackmd.io/_uploads/SyKJrvgDh.png)

---

![](https://hackmd.io/_uploads/SymFnP9I2.png)



