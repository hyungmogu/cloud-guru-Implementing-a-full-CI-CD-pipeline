# Application Monitoring

- `Application Monitoring` monitors data from applications
- `Application Monitoring` is done by making apps themselves to provide data
- `Application Monitoring` in prometheus is achieved by sending data through `/metrics` endpoint, which prometheus automatically detects
- `Application Monitorning` is done in Kubernetes by adding a service annotation, which makes Prometheus to scrape data from `/metrics` endpoint of all services represented by the service: `prometheus.io/scrape:'true'`

**Example (~/prometheus-values.yml)**
```
kind: Service
apiVersion: v1
metadata:
  name: train-schedule-service
  annotations:
    prometheus.io/scrape: 'true' 
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
        image: linuxacademycontent/train-schedule:1
        ports:
        - containerPort: 8080

```

## Example

- Train Schedule App uses nodejs app
- Train Schedule App uses nodejs specific Prometheus client library called prom-client to instrument the app

