# Installing Prometheus and Grafana

## Helm
- `Helm` is a tool for installing software using Kubernetes charts
- `Charts` makes it easy to install software on kubernetes cluster in a standardized configuration
    - e.g. installing Grafana and Prometheus
- `Charts` repo includes great charts for both Prometheus and Grafana

## Helm Installation

1. Install and initialize Helm
- `DESIRED_VERSION` should be newer now

**Kubernetes Control Plane**
```
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get > /tmp/get_helm.sh
chmod 700 /tmp/get_helm.sh
DESIRED_VERSION=v2.8.2 
helm init --wait
```

2. Set permission so Helm has the permission it needs to run properly

**Kubernetes Control Plane**
```
kubectl --namespace=kube-system create clusterrolebinding add-on-cluster-admin --clusterrole=cluster-admin --serviceaccount=kube-system:default
```

**Checking for Successful Installation**
```
helm ls
```

```
SHOULD RETURN NOTHING
```

3. Clone Kubernetes charts repo from Github

**Kubernetes Control Plane**
```
git clone 
```

4. Create values.yml file for any special settings we want to use 
5. Install Prometheus and Grafana with `helm install`
6. Setup a Prometheus datasource in Grafana and verify that it can connect

#s