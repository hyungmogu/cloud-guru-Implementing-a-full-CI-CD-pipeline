# Cloud Guru Implementing a Full CI/CD Pipeline

<img src="https://user-images.githubusercontent.com/6856382/226251463-29f39f34-405b-4653-a5fb-9fbb4eb36f70.png">

## Key Take Aways

1. Jenkins must be installed on third party server (e.g. AWS, Azure, GCP, Digital Ocean). Localhost doesn't work!

2. `Application Monitoring` in kubernetes is done by harvesting data from Kubernetes Service [See lesson 8.4 for more]

3. Setting Up Horizontal Pod Autoscaler [see lesson 10.2 for more]

```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

git clone git@github.com:<YOUR_PROJECT_REPO>.git
kubectl apply -f <YOUR_PROJECT_REPO>/<WHERE_YOUR_DEPLOY_CODE_IS_LOCATED>.yml
```

**`<YOUR_PROJECT_REPO>/<WHERE_YOUR_DEPLOY_CODE_IS_LOCATED>.yml`**
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
        image: linuxacademycontent/train-schedule:autoscaling
        ports:
        - containerPort: 8080
        livenessProbe:
          httpGet:
            path: /
            port: 8080
          initialDelaySeconds: 15
          timeoutSeconds: 1
          periodSeconds: 10
        resources: # <- MAKE SURE THIS IS INCLUDED
          requests:
            cpu: 200m

---

apiVersion: autoscaling/v2beta1 #<- MAKE SURE THIS IS INCLUDED
kind: HorizontalPodAutoscaler
metadata:
  name: train-schedule
  namespace: default
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: train-schedule-deployment
  minReplicas: 1
  maxReplicas: 4
  metrics:
  - type: Resource
    resource:
      name: cpu
      targetAverageUtilization: 50
```

4. Setting up liveness probe [see lesson 9.2 for more]
- liveness probe checks for unhealthy condition in containers without the main process exiting.
- liveness probe restarts a container if the probe determines that a container is unhealthy


**`<YOUR_PROJECT_REPO>/<WHERE_YOUR_DEPLOY_CODE_IS_LOCATED>.yml`**
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
        livenessProbe: # <-- MAKE SURE THIS IS INCLUDED
          httpGet:
            path: /
            port: 8080
          initialDelaySeconds: 15
          timeoutSeconds: 1
          periodSeconds: 1
```

