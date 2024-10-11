## What is a Service Monitor?
A Service Monitor is a feature in Prometheus Operator that defines how services in a Kubernetes cluster should be monitored. It is used to specify which endpoints Prometheus should track to collect metrics from services and applications running on the cluster.

In the context of Kubernetes, the Prometheus Operator is responsible for automating the configuration of Prometheus in a Kubernetes environment, facilitating the process of discovering and monitoring applications running in the cluster. Service Monitor is part of this automation and provides a way for Prometheus to automatically discover the services it should monitor.

### How does Service Monitor work?
- Service Monitor acts as an intermediary between Prometheus and services that expose metrics in Kubernetes. It uses selectors to identify services that should be monitored and defines parameters such as the ports and paths from which Prometheus should collect metrics.

### Examples of servicemonitor.yaml
- servicemonitor.yaml:
```
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: meu-servico-monitor
  labels:
    release: prometheus
spec:
  selector:
    matchLabels:
      app: minha-aplicacao
  endpoints:
  - port: metrics
    interval: 30s
    path: /metrics
```
- nginx-servicemonitor.yaml: 
```
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: nginx-servicemonitor
  labels:
    app: nginx
spec:
  selector:
    matchLabels:
      app: nginx
  endpoints:
  - interval: 30s
    path: /metrics
    targetPort: 9113
```

### Main components:
```
    metadata.name: Name of the Service Monitor.
    spec.selector.matchLabels: Specifies the labels that will be used to identify the services that Prometheus should monitor. In this example, Service Monitor searches for services that have the label app: my-application.
    spec.endpoints: Defines details about the endpoints from which metrics will be collected, such as:
        port: Service port where the metrics are exposed.
        path: Path where Prometheus will access the metrics (normally /metrics).
        interval: Frequency at which metrics will be collected.
```
### Useful commands with service monitor:

- Applying in the cluster:
```
kubectl apply -f servicemonitor.yaml
```

- To list all service monitors in the cluster:
```
kubectl get servicemonitors -A
```

- To list service monitors in a specific name space:
```
kubectl get servicemonitors -n namespace
```

- To describe a specific service monitors:
```
kubectl describe servicemonitor-name -n namespace
```

- To describe a specific service monitors:
```
kubectl describe servicemonitor-name -n namespace
```

### Checking
- We need to use the port-forward to check:
```
kubectl port-forward -n monitoring svc/prometheus-k8s 39090:9090
```
- After port-forward we can check the created servicemonitor in the "Targets" of Prometheus (localhost:39090 in this case).

### Usage example
- Service Discovery: Prometheus uses Service Monitor to automatically discover services with exposed metrics. This eliminates the need to manually configure each service in Prometheus.
- Centralized Monitoring: You can have multiple Service Monitors to monitor different types of services, which centralizes monitoring without having to directly edit Prometheus settings.

### Why use Service Monitors?
- Automation: Service Monitor simplifies configuration, as new services that follow the defined labeling standard can be automatically discovered by Prometheus.
- Scalability: In dynamic environments, such as Kubernetes, where new services may be created frequently, Service Monitor avoids the need to manually maintain monitoring configurations.
- Native integration with Kubernetes: It uses the Kubernetes labels and selectors system, integrating perfectly into the ecosystem.

### Conclusion
- Service Monitor is an efficient way to define which services and applications Prometheus should monitor in a Kubernetes cluster, providing flexibility and scalability, especially in environments with many dynamic applications.
