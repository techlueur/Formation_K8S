# Kubernetes Scaling Cheatsheet

## 📊 Types de Scaling

### Horizontal Scaling (Scale Out/In)

Scaling manuel d'un deployment :
```bash
kubectl scale deployment <nom-deployment> --replicas=<nombre>
```

Vérifier le nombre de replicas :
```bash
kubectl get deployment <nom-deployment>
```

Créer un HPA (Horizontal Pod Autoscaler) :
```bash
kubectl autoscale deployment <nom-deployment> \
  --cpu-percent=50 \
  --min=1 \
  --max=10
```

Voir status HPA :
```bash
kubectl get hpa
```

### Vertical Scaling (Scale Up/Down)

Configuration des ressources dans le manifest :
```yaml
resources:
  requests:
    memory: "64Mi"
    cpu: "250m"
  limits:
    memory: "128Mi"
    cpu: "500m"
```

## 🔍 Monitoring & Diagnostic

### Surveiller les Ressources

Voir l'utilisation CPU/Mémoire des pods :
```bash
kubectl top pods
```

Surveiller un HPA en temps réel :
```bash
kubectl get hpa -w
```

Détails d'un déploiement :
```bash
kubectl describe deployment <nom-deployment>
```

### Logs & Debugging

Voir les logs d'auto-scaling :
```bash
kubectl describe hpa <nom-hpa>
```

Vérifier les événements :
```bash
kubectl get events --sort-by='.lastTimestamp'
```

## 🛠 Configuration Best Practices

### HPA (Horizontal Pod Autoscaler)

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: mon-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: mon-app
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```

### VPA (Vertical Pod Autoscaler)

```yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: mon-app-vpa
spec:
  targetRef:
    apiVersion: "apps/v1"
    kind: Deployment
    name: mon-app
  updatePolicy:
    updateMode: "Auto"
```

## 📈 Métriques à Surveiller

* CPU Utilization (%)
* Memory Usage
* Request/Response Latency
* Error Rate
* Network I/O

## 🎯 Quick Tips

### Performance

* Définissez toujours des `requests` et `limits`
* Commencez avec un HPA modéré (50-70% CPU)
* Surveillez les métriques sur plusieurs jours

### Sécurité

* Limitez le `maxReplicas` selon vos ressources
* Configurez des `PodDisruptionBudgets`
* Utilisez des `readinessProbes`

### Coûts

* Définissez des limites de scaling appropriées
* Utilisez des node pools dédiés
* Monitorez la consommation de ressources

## 🚨 Troubleshooting Common Issues

| Problème | Commande de Diagnostic | Solution Possible |
|----------|----------------------|------------------|
| Pods ne scale pas | `kubectl describe hpa` | Vérifier les métriques CPU |
| OOM Kills | `kubectl describe pod` | Augmenter memory limits |
| Lenteur scaling | `kubectl get events` | Ajuster HPA parameters |

## 🔗 Commandes Utiles Bonus

Scaling multiple deployments :
```bash
kubectl scale deployment --all --replicas=0
```

Backup avant scaling :
```bash
kubectl get deployment <nom> -o yaml > backup.yaml
```

Rollback rapide :
```bash
kubectl rollout undo deployment/<nom>
```

---

> 💡 **Pro Tip**: Gardez cette cheatsheet à portée de main pendant vos opérations de scaling. 
> Pour plus d'informations, consultez la [documentation officielle Kubernetes](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/).
