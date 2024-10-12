## Create the pod
You need to create a YAML file to define the Pod. This file will describe the Pod and the container that will run on Nginx.
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
```

Explanation of fields:
```
    apiVersion: The API version we are using, in this case v1, because Pod is a basic feature in Kubernetes.
    kind: The type of resource we are creating, which in this case is Pod.
    metadata: Contains information about the Pod, such as the name (nginx-pod) and labels (app:nginx) that can be used to identify the Pod.
    spec: The Pod specification, which includes the definition of containers.
        containers: List of containers that will run in the Pod.
            name: The name of the container.
            image: The container image to use. In this case, we are using the official Nginx image available on Docker Hub (nginx:latest).
            ports: Defines the port that will be exposed inside the container (port 80).
```

After creating the nginx-pod.yaml file, you can apply this manifest to your Kubernetes cluster using the kubectl command:
```
kubectl apply -f nginx-pod.yaml
```

List pods
```
kubectl get pods
```

Run a shell inside the nginx container created with Pod:
```
kubectl exec -ti nginx-pod -c nginx -- bash
```

See more details about the created Pod, you can use:
```
kubectl describe pod nginx-pod
```

Redirect port 8080 on your local machine to port 80 on the Pod to access Nginx at http://localhost:8080: 
```
kubectl port-forward pod/nginx-pod 8080:80
```

## Create the podMonitor
PodMonitor uses label selectors to find the Pods you want to monitor, and then defines details about how the metrics should be collected. This includes the port and path on which Prometheus should fetch metrics. Here is a basic example of how a PodMonitor can be configured:

```
apiVersion: monitoring.coreos.com/v1
kind: PodMonitor
metadata:
  name: pod-monitor-example
  labels:
    team: devops
spec:
  selector:
    matchLabels:
      app: minha-aplicacao
  podMetricsEndpoints:
  - port: metrics
    path: /metrics
    interval: 30s
```

Explanation of Fields:
```
    apiVersion: Defines the version of the API used. For PodMonitors, the resource comes from the monitoring.coreos.com API.
    kind: The type of resource, which in this case is PodMonitor.
    metadata: Contains the name and labels for the PodMonitor.
    spec.selector: Specifies the labels that Prometheus will use to select Pods to monitor. In this example, you are selecting Pods with the label app: my-application.
    podMetricsEndpoints: Defines the endpoints within the Pods from which Prometheus should collect metrics. In this example, the metrics are exposed in the metrics port and the /metrics path, and Prometheus will collect them every 30 seconds (interval: 30s).
```
Main Components:
```
    spec.selector: Used to select Pods based on labels. Only Pods that match the specified labels will be monitored.

    podMetricsEndpoints: Defines where Prometheus should look for metrics within the selected Pods. This includes:
        port: Port where the metrics are exposed.
        path: Path of the endpoint to collect metrics (by default, /metrics).
        interval: Time interval between metric collections.
```
Advantages of PodMonitor

    Direct Pod Monitoring: PodMonitor eliminates the need to create a Service for Pods you want to monitor, making monitoring more direct and efficient.
    More Flexibility: It allows you to have finer control over which Pods should be monitored, without depending on a Service to group the Pods.
    Facilitates Monitoring of Ephemeral Pods: For more dynamic workloads or Pods that do not expose a stable service, PodMonitor offers a more practical solution.

When to use PodMonitor?

    Applications without a defined Service: In some cases, Pods may not be associated with a Service, but you may still want to monitor them directly. PodMonitor allows this.
    Job or CronJob Monitoring: For Pods that are temporarily created by jobs or cronjobs and that do not have an associated Service, PodMonitor can be useful.
    Pod-level monitoring: If you need granular control to monitor specific Pods, PodMonitor offers a more direct approach compared to ServiceMonitor.

Conclusion

PodMonitor is a feature of Prometheus Operator that allows you to directly monitor Pods in Kubernetes, without the need to expose them through a Service. It is useful for scenarios where you want to monitor individual Pods, with greater control over the discovery and metrics collection process.
