# Deploying to Kubernetes with Jenkins

## Instructions

1. Make sure docker hub key is included in Jenkin's Credentials
    - Click `Manage Jenkins` > `Manage Credentials` > `global`
    - Under Username and Password, add docker hub username and token as instructed [here](https://docs.docker.com/docker-hub/access-tokens/)

<img src="https://user-images.githubusercontent.com/6856382/226075111-32c8d39d-55d3-4ff1-850c-e92a85bdeaaf.png">

2. Rest of the steps are omitted due to it's plugin `Kubernetes Continuous Deploy Plugin` is outdated

    - maybe I can use ansible / Helm charts instead?

#