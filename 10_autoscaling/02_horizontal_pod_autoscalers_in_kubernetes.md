# Horizontal Pod Autoscalers in Kubernetes

- `Autoscaling` can be implemented in Kubernetes using a `Horizontal Pod Autoscaler` (HPA)
- `Autoscaling` for Kubernetes (HPA) can be found [here](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)
- `Horizontal Pod Autoscalers` work with the Metrics API to periodically check metrics and perform autoscaling according to the HPA configuration

## Steps to implementing Autoscaling Based on CPU usage with an HPA

1. Install Metrics API
    - Metrics server repository can be found [here](https://github.com/kubernetes-sigs/metrics-server)
    - `kubernetes create` creates Kubernetes objects directly from commandline
    - `kubernetes apply` creates Kubernetes objects from .yml file

    **Kubernetes Control Plane**
    ```
    kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
    ```

    **Verifying installation is complete**
    ```
    kubectl get pods -n kube-system
    ```

    ```
    NAME                                                   READY   STATUS    RESTARTS   AGE
    coredns-66bff467f8-bcg7z                               1/1     Running   1         
    2d15h
    coredns-66bff467f8-fpgj8                               1/1     Running   1         
    2d15h
    etcd-92c60ee3641c.mylabserver.com                      1/1     Running   1         
    2d15h
    kube-apiserver-92c60ee3641c.mylabserver.com            1/1     Running   1         
    2d15h
    kube-controller-manager-92c60ee3641c.mylabserver.com   1/1     Running   1         
    2d15h
    kube-proxy-9fdtw                                       1/1     Running   1         
    2d15h
    kube-proxy-pv955                                       1/1     Running   1         
    2d15h
    kube-scheduler-92c60ee3641c.mylabserver.com            1/1     Running   1         
    2d15h
    metrics-server-5c67c69c6c-c6n6l                        0/1     Running   0         
    58s #<- THIS GUY HERE
    ```

    **Verifying installation is complete #2**

    ```
    kubectl get --raw /apis/metrics.k8s.io
    ```

    ```
    kubectl get --raw /apis/metrics.k8s.io
    {"kind":"APIGroup","apiVersion":"v1","name":"metrics.k8s.io","versions":[{"groupVersion":"metrics.k8s.io/v1beta1","version":"v1beta1"}],"preferredVersion":{"groupVersion":"metrics.k8s.io/v1beta1","version":"v1beta1"}}
    ```

2. Clone repository to the cluster
    - Project can be found [here](https://github.com/linuxacademy/cicd-pipeline-train-schedule-autoscaling)

    **Kubernetes Control Plane**
    ```
    git clone git@github.com:linuxacademy/cicd-pipeline-train-schedule-autoscaling.git
    cd cicd-pipeline-train-schedule-autoscaling
    ```

3. Set resource request for the pods that will be autoscaled
    - `rescoures: requests: cpu: 200m` compares resources against cpu 
    - `rescoures: requests: cpu: 200m` means 200 millicpus
    - `rescoures: requests: cpu: 200m` means this is the average consumption of CPU by the container. It is the benchmark number for comparison
    - `rescoures: requests: cpu: 200m` means that at some point in time if CPU usage of the container goes to 400m, it means 200% CPU usage

    **Example (/project/root/folder/train-schedule-kube.yml)**

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
                cpu: 200m # <- THIS GUY HERE
    ```

3. Create an HPA
    - Documentation for Horizontal Pod Autoscaler can be found [here](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/)
    - `targetAverageUtilization` is calculated based on `resources: requests: cpu: 200m`
    - `targetAverageUtilization: 50` means kubernetes autoscaler will try to keep the average CPU utilization ACROSS all of the pods in the vincinity of 50%
    - `targetAverageUtilization: 50` works by calculating how many additional pods must be created / deleted to bring the cpu usage down / up to 50% 

    **Example (/project/root/folder/train-schedule-kube.yml)**
    ```
    ...

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

4. Apply HPA to Kubernetes Cluster

**Kubernetes Control Plane**
```
cd /project/root/directory
kubectl apply -f train-schedule-kube.yml
```

**Verifying that the installation is complete**
```
kubectl get pods 
```

```
NAME                                         READY   STATUS              RESTARTS   AGE
train-schedule-deployment-6b99c64749-9x7fh   0/1     ContainerCreating   0         
 4s
train-schedule-deployment-6b99c64749-tmskg   0/1     ContainerCreating   0         
 4s
```

**Verifying that the installation is complete #2**
```
kubectl get hpa 
```

```
NAME             REFERENCE                              TARGETS         MINPODS   MAXPODS   REPLICAS   AGE
train-schedule   Deployment/train-schedule-deployment   <unknown>/50%   1         4         2          41s
```

#