# Kubernetes Canary Testing with Jenkins Pipeline

## Canary Testing in Kubernetes with Jenkins Pipelines

- To implement canary test in a Jenkins Pipeline, we need to do the following:
    1. Add a stage to execute the canary deployment
    2. Add steps to the production deployment stage that will clean up canary pods when they are no longer needed

## Instruction - Implementing Canary Testing with Jenkins Pipeline

1. Make sure three credentials are added to Jenkins
    1. Github Key
    2. Dockerhub Key
    3. Kubeconfig Key

<img src="https://user-images.githubusercontent.com/6856382/226525930-d8c815bf-bda1-41c3-9534-84a66b8d1ccc.png">

2. In `train-schedule-kube-canary.yml`, unlike Previous steps, leave variables like `$DOCKER_IMAGE_NAME:$BUILD_NUMBER` as it is

**/project/root/train-schedule-kube-canary.yml**
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
  replicas: $CANARY_REPLICAS # <- LEAVE AS IS
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
        image: $DOCKER_IMAGE_NAME:$BUILD_NUMBER # <- LEAVE AS IS
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

3. In `Jenkins` file, add canary step

**/project/root/folder/Jenkinsfile**
```
pipeline {
    agent any
    environment {
        //be sure to replace "willbla" with your own Docker Hub username
        DOCKER_IMAGE_NAME = "guhyungm7/train-schedule"
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                    app.inside {
                        sh 'echo Hello, World!'
                    }
                }
            }
        }
        stage('Push Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        // ============= THIS GUY HERE =================
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
                    configs: 'train-schedule-kube-canary.yml',
                    enableConfigSubstitution: true
                )
            }
        }
        // ===============================================
        stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {
                input 'Deploy to Production?'
                milestone(1)
                kubernetesDeploy(
                    kubeconfigId: 'kubeconfig',
                    configs: 'train-schedule-kube.yml',
                    enableConfigSubstitution: true
                )
            }
        }
    }
}
```

4. In Jenkinsfile, define cleanup step
- Setting environmental variable `CANARY_REPLICAS` to 0 removes the pod from space 


**/project/root/folder/Jenkinsfile**
```
pipeline {
    agent any
    environment {
        //be sure to replace "willbla" with your own Docker Hub username
        DOCKER_IMAGE_NAME = "guhyungm7/train-schedule"
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                    app.inside {
                        sh 'echo Hello, World!'
                    }
                }
            }
        }
        stage('Push Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
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
                    configs: 'train-schedule-kube-canary.yml',
                    enableConfigSubstitution: true
                )
            }
        }
        // ============= THIS GUY HERE =================
        stage('DeployToProduction') {
            when {
                branch 'master'
            }
            environment {
                CANARY_REPLICAS = 0 // <- MUST DO THIS
            }
            steps {
                input 'Deploy to Production?'
                milestone(1)
                kubernetesDeploy(
                    kubeconfigId: 'kubeconfig',
                    configs: 'train-schedule-kube-canary.yml',
                    enableConfigSubstitution: true
                )
                kubernetesDeploy(
                    kubeconfigId: 'kubeconfig',
                    configs: 'train-schedule-kube.yml',
                    enableConfigSubstitution: true
                )
            }
        }
        // ============================================
    }
}
```

5. Run pipeline as `multibranch pipeline project` in jenkins

<img src="https://user-images.githubusercontent.com/6856382/226527887-303b69cc-2d83-4cb4-a2c2-c5c3f4fc8f7b.png">

#