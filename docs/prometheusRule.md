# Create:
## nginx-prometheusrule.yaml
```
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: nginx-prometheus-rule
  namespace: monitoring
  labels:
    prometheus: k8s
    role: alert-rules
    app.kubernetes.io/name: kube-prometheus
    app.kubernetes.io/part-of: kube-prometheus
spec:
  groups:
  - name:
    rules:
    - alert: NginxDown
      expr: up{job="nginx"} == 0
      for: 1m
      labels:
        severity: critical
      annotations:
        summary: "Nginx is down"
        description: "Nginx is down for more than 1 minute. Pod name: {{ $labels.pod }}"
```
# Commands:
