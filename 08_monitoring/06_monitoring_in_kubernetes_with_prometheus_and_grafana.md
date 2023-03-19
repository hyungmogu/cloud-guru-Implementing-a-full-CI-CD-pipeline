# Monitoring in Kubernetes with Prometheus and Grafana

- Helm chart is moved to [this](https://hub.helm.sh/) address
- Helm chart, if above link is not working, can be found [here](https://artifacthub.io/)
- Helm chart packages, after being deprecated by kubernetes, is being maintained individually

## Instructions on how to setup Prometheus and Grafana using Helm Charts

1. Login to Kubernetes control plane

2. Initialize helm
- More recent version is released. Please check helm site for more info
**Kubernetes Control Plane**
```
helm init --stable-repo-url=https://charts.helm.sh/stable --wait --tiller-image ghcr.io/helm/tiller:v2.8.2
```

3.

