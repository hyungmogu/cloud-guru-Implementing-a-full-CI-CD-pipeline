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

2. Click on `Status` > `Master build` and wait for it to process

3. Once the `master build` gets to DeployToProduction stage, hover over the DeployToProduction, and select proceed

4. Click on our Kubernetes server, and enter the following to review deployment  

```
kubectl get pods -w
```

5. Review application by copying the kubernete nodes (not master's) public ip, and paste it to browser with port 8080

## Add canary stage to the pipeline

1. On githubs browser page, go to the fork that was just made

2. Create file named `train-schedule-kube-canary.yml` in the project root directory

3. copy paste in canary code from [here](https://raw.githubusercontent.com/linuxacademy/cicd-pipeline-train-schedule-canary/example-solution/train-schedule-kube-canary.yml)

```
kind: Service
apiVersion: v1
metadata:
  name: train-schedule-service-canary
spec:
  type: NodePort
  selector:
    app: train-schedule
    track: canary
  ports:
  - protocol: TCP
    port: 8080
    nodePort: 8081

---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: train-schedule-deployment-canary
  labels:
    app: train-schedule
spec:
  replicas: $CANARY_REPLICAS
  selector:
    matchLabels:
      app: train-schedule
      track: canary
  template:
    metadata:
      labels:
        app: train-schedule
        track: canary
    spec:
      containers:
      - name: train-schedule
        image: $DOCKER_IMAGE_NAME:$BUILD_NUMBER
        ports:
        - containerPort: 8080
        livenessProbe:
          httpGet:
            path: /
            port: 8080
          initialDelaySeconds: 15
          timeoutSeconds: 1
          periodSeconds: 10
        resources:
          requests:
            cpu: 200m
```

4. In Jenkinsfile, add the following stage
    1. after the `Push Docker Image` stage 
    2. before the `DeployToProduction` stage

**Jenkinsfile**
```
stage('CanaryDeploy') {
  when {
    branch 'master'
  }
  environment {
    CANARY_REPLICAS = 1
  }
  steps {
    kubernetesDeploy(
      kubeconfigId: 'kubeconfig',
      configs: 'train-schdeule-kube-canary.yml',
      enableConfigSubstitution: true
      )
  }
}
```

5. when the review is complete, and is ready to dismantle canary pods, replace `environment` with the following:

**Jenkinsfile**
```
    ...
    environment {
        CANARY_REPLICAS = 0
    }
```

6. Run commit changes

## Run a Successful Deployment

1. Go to kubernetes server and review pods

**Kubernetes control plane**

```
kubectl get pods -w 
```

2. on jenkins browser page, click `build now` on `master branch` to apply updated information

3. on kubernetes terminal, check canary section pods are appearing

4. Check website by typing `<KUBERNETES_WORKER_PUBLIC_IP>:8081`

5. Once the site is good to go, click `DeployToProduction` and click speead 

