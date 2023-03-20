# Autoscaling in Kubernetes

## Instruction - Installing Metrics API in the Cluster

1. Clone git repository

**Kubernetes Control Plane**

```
git clone https://github.com/kubernetes-incubator/metrics-server.git
```

2. Checkout to appropriate version of the repository
- if it's the most recent version use the following command line

```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

**kubernetes Control Plane**
```
git checkout ed0663b3b4ddbfab5afea166dfd68c677930d22e
```

3. Install necessary objects

**Kubernetes Control Plane**
```
kubectl create -f deploy/1.8+
```

4. Make sure correct pods are running
- check for the name `metrics-server-...`

**kubernetes Control Plane**
```
kubectl get pods -n kube-system
```

```
NAME                                    READY     STATUS    RESTARTS   AGE
etcd-ip-10-0-1-101                      1/1       Running   0          38m
kube-apiserver-ip-10-0-1-101            1/1       Running   0          38m
kube-controller-manager-ip-10-0-1-101   1/1       Running   0          39m
kube-dns-86f4d74b45-qfl4f               3/3       Running   0          40m
kube-flannel-ds-86f5g                   1/1       Running   0          39m
kube-flannel-ds-f68hf                   1/1       Running   0          40m
kube-proxy-p5pqg                        1/1       Running   0          40m
kube-proxy-pw6xq                        1/1       Running   0          39m
kube-scheduler-ip-10-0-1-101            1/1       Running   0          39m
metrics-server-6fbfb84cdd-zvns9         1/1       Running   0          59s
```

5. Make sure metrics API is running


**Kubernetes Control Plane**
```
kubectl get --raw /apis/metrics.k8s.io
```

```
{"kind":"APIGroup","apiVersion":"v1","name":"metrics.k8s.io","versions":[{"groupVersion":"metrics.k8s.io/v1beta1","version":"v1beta1"}],"preferredVersion":{"groupVersion":"metrics.k8s.io/v1beta1","version":"v1beta1"},"serverAddressByClientCIDRs":null}
```

## Configur Horizontal Pod Autoscaler

1. Download project repo
- the project link can be found [here](https://github.com/linuxacademy/cicd-pipeline-train-schedule-autoscaling)

```
git clone 
```

2. Edit train-schedule-kube.yml to account for `Horizontal Pod Autoscaler`

**Kubernetes Control Plane**
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
        resources:
          requests:
            cpu: 200m

---

apiVersion: autoscaling/v2beta1
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

3. Make sure that pods are starting

**kubernetes control plane**

```
kubectl get pods -w
```

4. Verify that the autoscaler is working

**kubernetes control plane**
```
kubectl get hpa
```

## Testing the system

1. Do a bunch of load testing

```
kubectl run -i --tty load-generator --image=busybox /bin/sh
while true; do wget -q -O- http://NODE_PUBLIC_IP:8080/generate-cpu-load; done
```

2. Check if additional pods are created to mitigate rising demand

**Local Terminal**
```
while true; do wget -q -O- http://NODE_PUBLIC_IP:8080/generate-cpu-load; done
```

**Kubernetes control pane**

```
kubectl get hpa -w
```

```
NAME             REFERENCE                              TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
train-schedule   Deployment/train-schedule-deployment   6%/50%    1         4         1          5m
train-schedule   Deployment/train-schedule-deployment   1%/50%    1         4         1         5m
train-schedule   Deployment/train-schedule-deployment   1%/50%    1         4         1         6m
train-schedule   Deployment/train-schedule-deployment   1%/50%    1         4         1         8m
train-schedule   Deployment/train-schedule-deployment   87%/50%   1         4         1         9m
train-schedule   Deployment/train-schedule-deployment   123%/50%   1         4         1         10m
train-schedule   Deployment/train-schedule-deployment   123%/50%   1         4         1         10m
train-schedule   Deployment/train-schedule-deployment   123%/50%   1         4         2         11m
```

#