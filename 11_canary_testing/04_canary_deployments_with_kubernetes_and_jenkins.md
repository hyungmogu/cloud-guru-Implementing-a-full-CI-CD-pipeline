# Canary Deployments with Kubernetes and Jenkins

- This exercise works with [this](https://github.com/linuxacademy/cicd-pipeline-train-schedule-canary) repository

## Instructions

1. Setup Jenkins Credentials
    - Requires:
        1. github key
        2. dockerhub key
        3. kubernetes credentials

## Instructions - Setting up Jenkins Credentials

1. Select `Manage Jenkins` > `Manage Credentials` from the menu
2. From the single credential present, click on `global` > `Add Credentials`

### Adding Github key

1. Add person github username in `Username` section
2. Go to GitHub browser page, select `Settings` > `Developer Settings` > `Personal access tokens`
3. Select `Generate new tokens`, and make sure to
    1. Set the `token description` to `Jenkins`
    2. Select the checkbox for `admin:repo_hook`
    3. Click `Okay`
4. Copy secret
5. Go back to Jenkins browser page and paste the secret to `Password` field

### Adding Docker Hub key

1. On web browser's Jenkins page, click `Add Credentials`
2. Provide Docker Username and Password 
3. Set the id to `docker_hub_login` and description to `docker_hub`
4. Click `Okay`


### Add Kubernetes Credentials

1. On web browser's Jenkins page, click`Add Credentials`
2. Set the kind to `Kubernetes configuration (kubeconfig)`