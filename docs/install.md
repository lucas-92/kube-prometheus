# Installing Kube Prometheus
Kube Prometheus is distributed by the Prometheus Operator community. You can install it using the manifests available in the Prometheus Operator repository.

Clone the repository:
```
git clone https://github.com/prometheus-operator/kube-prometheus.git
cd kube-prometheus
```

Apply the namespace creation manifests and CRDs:
```
kubectl create -f manifests/setup
```

Wait until the CRDs are ready by checking their status:
```
kubectl get crds
```

Then, apply the rest of the manifests to install Prometheus, Grafana and Alertmanager:
```
kubectl create -f manifests/
```

Check the exposed services:
```
kubectl get svc -n monitoring
```


## Exposing
### Port-Forward:
```
k port-forward -n monitoring svc/grafana 33000:3000
```

Expose Prometheus with Port-Forward:
```
k port-forward -n monitoring svc/prometheus-k8s 39090:9090
```

Expose Alertmanager with Port-Forward:
```
k port-forward -n monitoring svc/alertmanager-main 39393:9393
```
### Ingress:
1. Create the monitoring-ingress.yaml file:
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: monitoring-ingress
  namespace: monitoring
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: prometheus.env.local
    http:
      paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: prometheus-k8s
              port:
                number: 9090
  - host: prometheus.env.local
    http:
      paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: grafana
              port:
                number: 3000
```

2. Add the `grafana.env.local` and `prometheus.env.local` in the host file as the host name for: 127.0.0.1:
```
sudo vim /etc/hosts 
```

3. Aplique o monitoring-ingress.yaml para criar o ingress:
```
kubectl apply -f monitoring-ingress.yaml 
```

## Commands
To see all created ServiceMonitor, simply run the following command:
```
k get servicemonitor -n monitoring
```

To see the contents of a ServiceMonitor, simply run the following command:
```
kubectl get servicemonitor prometheus-k8s -n monitoring -o yaml
```

To see custom resource definitions:
```
k get customresourcedefinitions
```
