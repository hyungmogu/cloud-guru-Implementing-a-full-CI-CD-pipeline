# Installing Prometheus and Grafana

- NOTE: HELM IS DEPRECATED AND THIS INSTRUCTIONS IS NOW INVALID

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

3. Clone Kubernetes charts repo from Github (THIS STEP AND BELOW ARE DEPRECATED DUE TO CNKF NOT HAVING ENOUGH RESOURCES TO FUND FOR SINGLE REPOSITORY)

**Kubernetes Control Plane**
```
git clone https://github.com/kubernetes/charts
```

4. Create prometheus-values.yml file for any special settings we want to use (HELM DEPRECATED DON'T USE)

**Kubernetes Control Plane**
```
vi prometheus-values.yml
```

```
alertmanager:
  persistentVolume:
    enabled: false
server:
  persistentVolume:
    enabled: false
```


5. Install Prometheus with `helm install` (DEPRECATED)

```
helm install -f prometheus-values.yml charts/stable/prometheus --name prometheus --namespace prometheus
```

**Checking for Successful Installation**
```
kubectl get pods -n prometheus
```

6. Install grafana

```
vi grafana-values.yml
```

```
adminPassword: password
```

7. Install Grafana Using Helm (DEPRECATED)

```
helm install -f grafana-values.yml charts/stable/grafana/ --name grafana --namespace grafana
```

**Checking for Successful Installation**
```
kubectl get pods -n grafana
```

8. Expose node port service that will allow users to access Grafana

```
vi grafana-ext.yml
```

**grafana-ext.yml**
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
    nodePort:8080
```

9. Apply services .yml file to kubernetes

```
kubectl apply -f grafana-ext.yml
```

10. Check if the changes have been made successfully

```
kubecetl get services -n grafana
```

11. Check in browser if it is running

```
<PUBLIC_URL>:8080
```

