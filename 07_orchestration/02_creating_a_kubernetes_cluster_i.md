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

3. Run the following commands on master node to allow the use of `kubectl`
- after running the below command, `kubectl` can be run

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

**confirming successful registration**
```
kubectl get nodes
```

4. Setup networking using `flannel` on master node
- as of March 16th, 2023, the below link works for kubernetes 1.17+
- if successful, the status of nodes under `kubectl get nodes` would change to `Ready`
```
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
```

**confirming successful installation**
```
kubectl get nodes
```

```
NAME                           STATUS   ROLES    AGE     VERSION
92c60ee3641c.mylabserver.com   Ready    master   7m24s   v1.18.5
```

5. Have worker nodes join kubernetes network using the command given at the end of `kubeadm init`
- if successful, there should be more than 1 node showing up

**Worker node**
```
sudo kubeadm join 172.31.108.203:6443 --token ymqx98.9nhwumvmhnb8oii1 \
    --discovery-token-ca-cert-hash sha256:<sha_code>
```

**confirming successful joining of worker node (master node)**
```
kubectl get nodes
```

```
NAME                           STATUS   ROLES    AGE   VERSION
92c60ee3641c.mylabserver.com   Ready    master   10m   v1.18.5
92c60ee3642c.mylabserver.com   Ready    <none>   66s   v1.18.5
```

