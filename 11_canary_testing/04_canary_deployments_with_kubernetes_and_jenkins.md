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

- Kubernetes Configuration plugin is deprecated.

1. On web browser's Jenkins page, click`Add Credentials`
2. Set the kind to `Kubernetes configuration (kubeconfig)`
3. Set the ID to kubeconfig and the description to Kubeconfig
4. Login into Kubernetes master
5. Copy the result of the following command:

```
cat ~/.kube/config
```

6. Back in Jenkins browser, paste the content to Entry directly > Content section
7. click `Okay`

## Setting up the project

1. On web browser's Jenkins page, go back to home page
2. Click `new items`
3. Give the item the name `train-schedule` (or any unique name suitable for this jenkins pipeline)
4. Select `Multibranch pipeline` > `Click`
5. Under `Branch Sources`, chance add source to `Github`
6. Set up the section as follows
    - `Credentials`: click dropdown and select `Github Key`
     - `Repository HTTPS URL`: https://github.com/your_account_id/cicd-pipeline-train-schedule-canary.git
        - ex: https://github.com/hyungmogu/cicd-pipeline-train-schedule-canary.git
7. Click `Save`

## Review the Builds

1. Should an item called `Build Queue` appears above `Build Executor Status`, selectd cancel button

2. Click on `Master build` and wait for it to process

