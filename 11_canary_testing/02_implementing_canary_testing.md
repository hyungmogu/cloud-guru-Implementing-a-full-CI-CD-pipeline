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

2. replace `$DOCKER_IMAGE_NAME:$BUILD_NUMBER` with the image to run in deployed pod
    - in this case, it's `linuxacademycontent/train-schedule:1`

3. Apply the deployment .yml file

**Kubernetes Control Plane**
```
kubectl apply -f train-schedule-kube.yml
```

```
NAME                                        READY   STATUS    RESTARTS   AGE
train-schedule-deployment-b85b697f8-lw7d7   1/1     Running   0          54s
train-schedule-deployment-b85b697f8-x9qvs   1/1     Running   0          54s
```

4. Check if the track label has been applied successfully

**Kubernetes Control Plane**
```
kubectl describe pod train-schedule-deployment-b85b697f8-lw7d7
```

```
Name:         train-schedule-deployment-b85b697f8-lw7d7
Namespace:    default
Priority:     0
Node:         92c60ee3642c.mylabserver.com/172.31.102.174
Start Time:   Tue, 21 Mar 2023 01:34:26 +0000
Labels:       app=train-schedule
              pod-template-hash=b85b697f8
              track=stable # <- THIS LABEL HERE
Annotations:  <none>
Status:       Running
IP:           **.***.*.*
IPs:
  IP:           **.***.*.*
Controlled By:  ReplicaSet/train-schedule-deployment-b85b697f8
Containers:
  train-schedule:
    Container ID:   docker://d624d46d8d2963084c3148a244af0371356118ee908a20ef9cd36898b029acb5
    Image:          linuxacademycontent/train-schedule:1
    Image ID:       docker-pullable://linuxacademycontent/train-schedule@sha256:0b42eed24cff995a6c72220690f7d228b0ab5070959c152644504fcadc7bdf90
    Port:           8080/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Tue, 21 Mar 2023 01:34:39 +0000
    Ready:          True
    Restart Count:  0
    Requests:
      cpu:        200m
    Liveness:     http-get http://:8080/ delay=15s timeout=1s period=10s #success=1 #failure=3
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-l4jb6 (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  default-token-l4jb6:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-l4jb6
    Optional:    false
QoS Class:       Burstable
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason     Age    From                                   Message
  ----    ------     ----   ----                                   -------
  Normal  Scheduled  2m19s  default-scheduler                      Successfully assigned default/train-schedule-deployment-b85b697f8-lw7d7 to 92c60ee3642c.mylabserver.com
  Normal  Pulling    2m18s  kubelet, 92c60ee3642c.mylabserver.com  Pulling image "linuxacademycontent/train-schedule:1"
  Normal  Pulled     2m13s  kubelet, 92c60ee3642c.mylabserver.com  Successfully pulled image "linuxacademycontent/train-schedule:1"
  Normal  Created    2m6s   kubelet, 92c60ee3642c.mylabserver.com  Created container train-schedule
  Normal  Started    2m6s   kubelet, 92c60ee3642c.mylabserver.com  Started container train-schedule
```

5. In the same Kubernetes control plane, create another file named `train-schedule-kube-canary.yml`
- notice in the file below that service has label `track: canary`
- notice in the file below that deployment select has label `track: canary`
- notice in the file below that deploement template has label `track: canary`
- notice that unlike the original deployment file, it's metadata name has `-canary` attached to it
- notice in the file below that nodePort is 8081 instead of 8080
    - this is a way to test canary pods by re-directing some traffic

**Kubernetes Control Plane (/project/root/folder/train-scheudle-kube-canary.yml)**
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

6. replace `$DOCKER_IMAGE_NAME:$BUILD_NUMBER` with `linuxacademycontent/train-schedule:latest`

```
    ...

    spec:
      containers:
      - name: train-schedule
        image: linuxacademycontent/train-schedule:latest
        ports:
        - containerPort: 8080
        livenessProbe:
          httpGet:
    ...
```

7. replace `replicas: $CANARY_REPLICAS` with number of replicas
- here in this case, it's `1`

```
...
apiVersion: apps/v1
kind: Deployment
metadata:
  name: train-schedule-deployment-canary
  labels:
    app: train-schedule
spec:
  replicas: 1
  selector:
    matchLabels:
      app: train-schedule
      track: canary

      ... 
```

8. Apply canary deployment file to Kubernetes

**Kubernetes Control Plane**
```
kubectl apply -f train-schedule-kube-canary.yml
```

9. Look at logs, and metric systems to see whether we are getting any 500 responses

10. Once all is okay, apply canary pods to normal deployment by replacing old versions in `image: $DOCKER_IMAGE_NAME:$BUILD_NUMBER` and going through normal deployment

11. Remove canary services and deployments

12. If it's not okay, just delete canary services and deployments