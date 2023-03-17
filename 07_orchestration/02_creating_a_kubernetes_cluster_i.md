# Creating a Kubernetes Cluster

## Installation Instructions

- Installation instruction for kubernetes cluster is given [here](https://kubernetes.io/docs/tasks/tools/)
- Should have following installed
    - docker
    - kubelet
    - kubeadm
    - kubectl

## Kubeadm

- `Kubeadm` allows to setup kubernetes and run it quickly and easily

## Installation

### 1) Installing Kubernetes with Kubeadm

- `Kubeadm` requires at least two servers:
    1. master
    2. node (let's say worker node)

- `kubeadm` can be used to setup master with the following commands

```
sudo kubeadm init --config=<PATH_TO_KUBERNETES_CLUSTER_CONFIGURATION_FILE>.yml
```

- `kubeadm` gives instructions to configure worker nodes

### 2) Installing Flannel

- `Flannel` is required to communicate between pods
- `Flannel` is a network fabric for containers designed for kubernetes
- `Flannel` network configuration of pods can be applied from it's github

**Example**
```
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

### 3) Join nodes to the cluster


## Example

1. Create a configuration file for kubeadm

```
vim kube-config.yml
```

**kube-config.yml**
```
apiVersion: kubeadm.k8s.io/v1beta2
kind: ClusterConfiguration
networking:
  podSubnet: 10.244.0.0/16
apiServer:
  extraArgs:
    service-node-port-range: 8000-31274
```

2. run `kubeadm` with the config file

```
sudo kubeadm init --config=kube-config.yml
```

