# Running Docker in Production

## Steps

1. Install docker on the server
2. Start and enable docker service
3. Use docker run with a restart policy
    - Makes sure that container is always running
    - Recreates container if crashes occur

**Example**
```
docker run --restart always
```

