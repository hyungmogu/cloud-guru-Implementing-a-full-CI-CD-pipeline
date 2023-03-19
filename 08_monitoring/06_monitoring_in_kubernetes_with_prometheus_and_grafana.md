# Monitoring in Kubernetes with Prometheus and Grafana

- Helm chart is moved to [this](https://hub.helm.sh/) address
- Helm chart, if above link is not working, can be found [here](https://artifacthub.io/)
- Helm chart packages, after being deprecated by kubernetes, is being maintained individually

## Instructions on Initializing Helm

1. Login to Kubernetes control plane

2. Initialize helm
- More recent version is released. Please check helm site for more info

**Kubernetes Control Plane**
```
helm init --stable-repo-url=https://charts.helm.sh/stable --wait --tiller-image ghcr.io/helm/tiller:v2.8.2
```

3. Clone Kubernetes Chart Repo

**Kubernetes Control Plane**
```
 cd ~/
 git clone https://github.com/kubernetes/charts
 cd charts
 git checkout efdcffe0b6973111ec6e5e83136ea74cdbe6527d
 cd ../
```

## Instruction - Installing Prometheus using Helm

4. Create a prometheus-values.yml file

**Kubernetes Control Plane** 
```
 vim prometheus-values.yml
```

**/project/root/folder/prometheus-values.yml**
```
alertmanager:
  persistentVolume:
    enabled: false
server:
  persistentVolume:
    enabled: false
```

5. Use Helm to install Prometheus

**Kubernetes Control Plane**
```
helm install -f ~/prometheus-values.yml ~/charts/stable/prometheus --name prometheus --namespace prometheus
```

6. We can see which pods are running in this new namespace with the command

**Kubernetes Control Plane**
```
kubectl get pods -n prometheus
```

## Instruction - Installing Grafana using Helm

1. Create a grafana-values.yml

**Kubernetes Control Plane**
```
vim grafana-values.yml
```

**/project/root/folder/grafana-values.yml**
```
adminPassword: <YOUR_PASSWORD_FOR_GRAFANA>
```

2. Use helm to install Grafana

**Kubernetes Control Plane**
```
helm install -f ~/grafana-values.yml ~/charts/stable/grafana --name grafana --namespace grafana
```

3. See Which Pods are running in the new namespace with the command

**Kubernetes control plane**
```
kubectl get pods -n grafana
```

## Instruction - Deploying NodePort Service to Provide External Access to Grafana

1. Create file grafana-ext.yml

**Kubernetes Control Plane**
```
vim grafana-ext.yml
```

**/project/root/folder/grafana-ext.yml**
```
kind: Service
apiVersion: v1
metadata:
  namespace: grafana
  name: grafana-ext
spec:
  type: NodePort
  selector:
    app: grafana
  ports:
  - protocol: TCP
    port: 3000
    nodePort: 8081
```

2. Deploy the service to Kubernetes

**Kubernetes Control Plane**
```
kubectl apply -f ~/grafana-ext.yml
```

## Instruction - Adding a Datasource for Prometheus

1. Click on `Add data source`
    - Name : Kubernetes
    - Type: Prometheus
    - URL: http://prometheus-server.prometheus.svc.cluster.local


    