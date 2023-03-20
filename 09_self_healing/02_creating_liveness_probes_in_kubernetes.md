# Creating Liveness Probes in Kubernetes

- `Liveness probe` is used in a case where an application becomes unhealthy without the main process exiting
- `Liveness probe` is more sophisticated way of self-healing
- `Liveness probe` is a part of Kubernetes
- `Liveness probe` is a custom checks that run periodically against containers to detect whether or not they are healthy
- `Liveness probe` restarts a container if the probe determines that a container is unhealthy
- `Liveness probe` is activated by defining `livenessProbe` under container under Kubernetes deployment

## Instruction - Applying Liveness Probe to Kubernetes

1. Reference the following file. Notice how the type `livenessProbe` is added to the file

**Example (/project/root/directory/train-schedule-kube.yml)**
```
kind: Service
apiVersion: v1
metadata:
  name: train-schedule-service
spec:
  type: NodePort
  selector:
    app: train-schedule
  ports:
  - protocol: TCP
    port: 8080
    nodePort: 8080

---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: train-schedule-deployment
  labels:
    app: train-schedule
spec:
  replicas: 2
  selector:
    matchLabels:
      app: train-schedule
  template:
    metadata:
      labels:
        app: train-schedule
    spec:
      containers:
      - name: train-schedule
        image: guhyungm7/train-schedule:selfhealing
        ports:
        - containerPort: 8080
        livenessProbe: # <-- HERE
          httpGet:
            path: /
            port: 8080
          initialDelaySeconds: 15
          timeoutSeconds: 1
          periodSeconds: 1
```

- type `httpGet` is the address of where to make an HTTP request to the application
- type `port` is the internal port (not external port like nodePort)

2. Apply file to kubernetes

**Kubernetes Control Plane**
```
kubectl apply -f train-schedule-kube.yml
```

**Checking if changes are successful**
```
kubectl get pods -w
```

