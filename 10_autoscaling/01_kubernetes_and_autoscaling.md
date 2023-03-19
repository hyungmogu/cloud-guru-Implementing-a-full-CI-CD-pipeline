# Kubernetes and Autoscaling

## Autoscaling

- `Autoscaling` automatically deallocates / allocates resources based on
    1. resource
    2. time
- `Autoscaling` in Kubernetes means
    1. Increasing the number of pod replicas when resource usage is high
    2. Removing uneeded replicas when resource usage is low

- `Autoscaling` is driven by metrics such as:
    1. CPU usage
    2. Memory usage
    3. Requests per second

#