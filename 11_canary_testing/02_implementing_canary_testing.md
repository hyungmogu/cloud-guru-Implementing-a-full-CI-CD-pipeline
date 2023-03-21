# Implementing Canary Testing

- `Canary Test` in Kubernetes can be implemented by:
    1. Adding labels to pod that will differentiate between canary and normal pods
    2. Creating a new deployment for the canary pods
    3. Rolling out the new code to the normal pods by updating the regular deployment after canary testing is complete

## Instructions on Setting up Canary Testing

- core code containing examples of canary testing is provided [here](https://github.com/linuxacademy/cicd-pipeline-train-schedule-canary)

1. Add `spec: selector: track: stable` to train-schedule-kube.yml (the deployment file)
    - `spec: selector: track: stable` ensures that deployment only matches pods with the label
    - `spec: selector: track: stable` ensures that canary deployment doesn't overwrite these pods
    - `spec: template: labels: track: stable` applies that label to the pods from the deployment

**Kubernetes Control Plane (/project/root/folder/train-schedule-kube.yml)**
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
      track: stable # <- THIS GUY HERE
  template:
    metadata:
      labels:
        app: train-schedule
        track: stable # <- LABEL IS APPLIED HERE
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

#