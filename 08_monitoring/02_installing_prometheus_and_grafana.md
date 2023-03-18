# Installing Prometheus and Grafana

## Helm
- `Helm` is a tool for installing software using Kubernetes charts
- `Charts` makes it easy to install software on kubernetes cluster in a standardized configuration
    - e.g. installing Grafana and Prometheus
- `Charts` repo includes great charts for both Prometheus and Grafana

## Helm Installation

1. Install and initialize Helm
2. Clone Kubernetes charts repo from Github
3. Create values.yml file for any special settings we want to use 
4. Install Prometheus and Grafana with `helm install`
5. Setup a Prometheus datasource in Grafana and verify that it can connect

#