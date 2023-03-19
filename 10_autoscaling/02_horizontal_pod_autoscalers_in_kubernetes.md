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
    git clone git@github.com:kubernetes-sigs/metrics-server.git
    cd metrics-server
    kubernetes create -f 
    ```

2. Set resource request for the pods that will be autoscaled 

3. Create an HPA

#