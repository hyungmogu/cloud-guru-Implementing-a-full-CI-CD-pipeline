# Kubernetes Basics

## Kubernetes Key Objects

- `Cluster`: A master and one or more nodes
- `Master`: Runs control processes and Kubernetes API
    - `Master` controls what runs on all the other nodes
- `Node`: A worker machine
- `Pod`: A running process on a node. 
    - A pod can cointain multiple containers
- `Service`: An abstraction defining a set of pods and a means to access them


<img src="https://user-images.githubusercontent.com/6856382/225928944-64b8386e-b1ec-4441-bcaf-255ff0bf23f3.png">

## Kubernetes Deployments

- `Kubernetes deployment` is a declarative definition for Pods and ReplicaSets

**Example**
```
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
    kubectl.kubernetes.io/last-applied-configuration: |
  creationTimestamp: "2022-12-30T00:52:56Z"
  generation: 1
  name: web-frontend
  namespace: web
  resourceVersion: "744"
  uid: ecfc0b80-f9b6-4b1a-b002-cc1c46572e5d
spec:
  progressDeadlineSeconds: 600
```

- `kubernetes deployment` changes are applied via the following command:

```
sudo kubectl apply -f <the_path_of_yml_file>.yml
```

## Kubernentes Deployments Usecases

1. "I want X replicas of Y Docker image"
    - `Kubernetes` orchastrates the process of standing up the desired number of pods across multiple nodes in a cluster
2. "I want to change the number of replicas"
    - `Kubernetes` creates or destroys pods to met the desired new number if the number of replicas in deployment changes
3. "I want to deploy a new Docker image with code changes"
    - `Kubernetes` orchastrates the rollout of the new version with zero downtime