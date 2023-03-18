# Application Monitoring

- `Application Monitoring` monitors data from applications
- `Application Monitoring` is done by making apps themselves to provide data
- `Application Monitoring` in prometheus is achieved by sending data through `/metrics` endpoint, which prometheus automatically detects
- `Application Monitorning` is done in Kubernetes by adding a service annotation, which makes Prometheus to scrape data from `/metrics` endpoint of all services represented by the service: `prometheus.io/scrape:'true'`


## Example

- Train Schedule App uses nodejs app
- Train Schedule App uses nodejs specific Prometheus client library called prom-client to instrument the app

