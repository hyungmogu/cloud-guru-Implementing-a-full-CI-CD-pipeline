# Cloud Guru Implementing a Full CI/CD Pipeline

<img src="https://user-images.githubusercontent.com/6856382/226251463-29f39f34-405b-4653-a5fb-9fbb4eb36f70.png">

## Key Take Aways

### Setting up

1. Jenkins must be installed on third party server (e.g. AWS, Azure, GCP, Digital Ocean). Localhost doesn't work!

### Application / Infrastructure Monitoring

1. `Application Monitoring` in kubernetes is done by harvesting data from Kubernetes Service [See lesson 8.4 for more]

### Horizontal Pod Scaler

1. Setting Up Horizontal Pod Autoscaler [see lesson 10.2 for more]

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

apiVersion: autoscaling/v2beta1 #<- MAKE SURE ALL OF THIS AND BELOW ARE INCLUDED
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

### Liveness Probe

1. Setting up liveness probe [see lesson 9.2 for more]
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

### Canary Testing (Important)

1. `Canary Testing` is very similar to Blue / Green Deployment
2. `Canary Testing` is used for debugging
2. `Canary Testing` works by
  1. Routing certain percentage of users to the newly applied solution contained inside canary pod
  2. If all is well, then merge solution by deploying it to main pods
3. `Canary Testing` is applied by
  1. Deploying the normal pods with label `stable`
  2. Deploying the canary pods with label `canary`
  3. Setting service port for canary pods to be different from normal, stable pods
  4. Replace images in normal pods if canary testing is successful, delete canary pods if unsucessful

- NOTE: Website can be accessed by entering `<PUBLIC_IP` of worker nodes, not control plane

**Kubernetes Control Plane (Normal deployment)**
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
      track: stable
  template:
    metadata:
      labels:
        app: train-schedule
        track: stable
    spec:
      containers:
      - name: train-schedule
        image: $DOCKER_IMAGE_NAME:$BUILD_NUMBER
        ports:
        - containerPort: 8080
        livenessProbe:
          httpGet:
            path: /
            port: 8080
          initialDelaySeconds: 15
          timeoutSeconds: 1
          periodSeconds: 10
        resources:
          requests:
            cpu: 200m

```

**Kubernetes Control Plane (Canary deployment)**

```
kind: Service
apiVersion: v1
metadata:
  name: train-schedule-service-canary
spec:
  type: NodePort
  selector:
    app: train-schedule
    track: canary
  ports:
  - protocol: TCP
    port: 8080
    nodePort: 8081

---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: train-schedule-deployment-canary
  labels:
    app: train-schedule
spec:
  replicas: $CANARY_REPLICAS
  selector:
    matchLabels:
      app: train-schedule
      track: canary
  template:
    metadata:
      labels:
        app: train-schedule
        track: canary
    spec:
      containers:
      - name: train-schedule
        image: $DOCKER_IMAGE_NAME:$BUILD_NUMBER
        ports:
        - containerPort: 8080
        livenessProbe:
          httpGet:
            path: /
            port: 8080
          initialDelaySeconds: 15
          timeoutSeconds: 1
          periodSeconds: 10
        resources:
          requests:
            cpu: 200m
```

