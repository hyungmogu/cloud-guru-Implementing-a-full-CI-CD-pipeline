# Implementing Automated Deployment Jenkins Pipeline

- This part of exercise is about `continuous deployment`
- This part of exercise already includes `continuous integration`
- This part of exercise tasks user to
    1. Perform CI build and produce a deployable zip file
    2. Deploy the application to staging server
    3. Wait for approval before deploying to production
    4. Once approved, deploy to production

- This part of exercises uses [this](https://github.com/linuxacademy/cicd-pipeline-train-schedule-cd) repo

## Instructions - Deploying the App to the Staging Server via the Jenkins Pipeline

### 1) Configure staging and production Servers for the Publish Over SSH Plugin

1. In the Jenkin's tab in your browser, click `Manage Jenkins` and click `Configure Systems`

<img src="https://user-images.githubusercontent.com/6856382/225211029-c9a7af26-7909-4fe3-a8db-a41cd02bad54.png">

2. Find the `Publish over SSH` section

<img src="https://user-images.githubusercontent.com/6856382/225211290-13deaff8-7417-434f-8915-1fdd32cad86f.png">

3. Click `Add` under `SSH Servers`

<img src="https://user-images.githubusercontent.com/6856382/225211504-12789c64-e230-448c-91c0-d98b3e54be38.png">

4. Add the SSH server values for staging server where
    1. Name: staging
    2. Hostname: Staging server public IP Address
    3. Remote Directory: /

5. Click `Add` again for Production` server
    1. Name: production
    2. Hostname: Production server public IP Address

6. Click `Save`

<img src="https://user-images.githubusercontent.com/6856382/225214091-36ed2de4-7332-4cda-8c19-1dcde60dab03.png">

### 2) Setup Jenkins Credentials
- This is to allow Jenkins to login to servers and perform deployment

1. From the sidebar, click `credentials`
    - on new version of Jenkins, click `Manage Jenkins` > `Manage Credentials`

<img src="https://user-images.githubusercontent.com/6856382/225215269-ff212a23-0fa3-4634-9c7d-7a24102fc12b.png">

2. Under the section `Stores Scoped to Jenkins`, click `global`

<img src="https://user-images.githubusercontent.com/6856382/225215637-a4ce9c5a-6edc-44a2-9161-3ac5be7d3352.png">

3. Click `Add Credentials`
    - These are the credentials that jenkins will use to login to the servers and perform the deployment
    - fill in the following values (these are pre-supplied credential values):
        - username: deploy
        - password: jenkins
        - ID: webserver_login
        - Description: Webserver Login

<img src="https://user-images.githubusercontent.com/6856382/225216817-03178438-ff45-4e3c-b9dd-0604ed23d684.png">

### 3) Setup Jenkins Project
- This part of tutorial requires github repo, which can be downloaded [here](https://github.com/linuxacademy/cicd-pipeline-train-schedule-cd)

1. Go back to Jenkins Home page

2. From the sidebar menu, click `New Item`

<img src="https://user-images.githubusercontent.com/6856382/225217986-0f04031a-8ca8-43a5-ab9c-4d58a1df2112.png">

3. Enter the item name `train schedule`

4. Select `Multibranch Pipeline`

<img src="https://user-images.githubusercontent.com/6856382/225220932-1e8450f7-cc10-46ad-b48a-aff08bb6f88a.png">

5. Navigate to [this](https://github.com/linuxacademy/cicd-pipeline-train-schedule-cd) git repo and fork the repo

#### Generating Webhook
- The purpose of webhook is to constantly observe github account and if changed, perform CI/CD

1. (on Github) Click `Settings` > `Developer Settings` > `Personal Access Tokens` > `Generate New Token` to create webhook for Jenkins
    - Enter the following values
        1. Token Description: "Webhook for CloudGuru Jenkins"
        2. Select `admin:repo_hook`

2. Copy generated API token

#### Completing Setup

8. On Jenkin's `Multibranch Pipeline` page, select `Add Source` > `Github` under `Branch Sources` tab

<img src="https://user-images.githubusercontent.com/6856382/225325383-ae6ddc1b-cc06-444e-8f28-f9a7a5d872fe.png">

<img src="https://user-images.githubusercontent.com/6856382/225327608-9e132c47-e5c2-4184-97ef-437fbe9c2ded.png">


9. Select `Add` > `Jenkins` under `credentials`

<img src="https://user-images.githubusercontent.com/6856382/225482282-e43be051-5d89-4ff6-ab5d-6df99c7f5240.png"/>

10. Set the username, password, id, and description as follows
    - username: `<YOUR_GITHUB_USERNAME>`
    - password: `<GENERATED_API_TOKEN_FROM_EARLIER>`
    - ID: `github_key`
    - Description: `Github Key`

<img src="https://user-images.githubusercontent.com/6856382/225484649-5217d2d9-0ec6-433b-8824-ac7388da8bcf.png"/>

11. Type in `<YOUR_GITHUB_USERNAME>` to `Owner` and select repository to run 

<img src="https://user-images.githubusercontent.com/6856382/225485807-554029f6-6558-4c8e-9072-4d6e1305833e.png"/>

12. Click `Save`

## Creating Deploy code in the Jenkinsfile and Running the Build

1. Open `Jenkinsfile` in the forked repo

2. Edit the `Jenkinsfile` and replace it with the following [contents](https://github.com/linuxacademy/cicd-pipeline-train-schedule-cd/blob/example-solution/Jenkinsfile):

```
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('DeployToStaging') {
            when {
                branch 'master'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'staging',
                                sshCredentials: [
                                    username: "$USERNAME",
                                    encryptedPassphrase: "$USERPASS"
                                ], 
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'dist/trainSchedule.zip',
                                        removePrefix: 'dist/',
                                        remoteDirectory: '/tmp',
                                        execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }
        stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {
                input 'Does the staging environment look OK?'
                milestone(1)
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'production',
                                sshCredentials: [
                                    username: "$USERNAME",
                                    encryptedPassphrase: "$USERPASS"
                                ], 
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'dist/trainSchedule.zip',
                                        removePrefix: 'dist/',
                                        remoteDirectory: '/tmp',
                                        execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }
    }
}
```

3. Returning to Jenkins UI, click `Build Now` inside the multipipeline branch that is created.

4. Test the deployment by putting public url in browser

5. if `<PUBLIC_URL_STARING_SERVER>:3000` returns response, it should be successful

## Deploying the App to the Production Server

1. Add Build stage `DeployToProduction` to Jenkinsfile

2. On Jenkins UI page where the multipipline project for this is created, select `build now`

3. Test connection once deployment is successful.

## Note

1. Installing firewall on ubuntu

```
sudo apt-get update
sudo apt-get install -y ufw
```

**Denying all inbound and permit all outbound**
```
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

**Adding UFW to permit only HTTP, SSH, and 3000 traffic**
```
sudo ufw allow 80
sudo ufw allow 3000
sudo ufw allow 5900:5901/tcp
```