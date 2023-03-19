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

2. Set resource request for the pods that will be autoscaled 

3. Create an HPA

