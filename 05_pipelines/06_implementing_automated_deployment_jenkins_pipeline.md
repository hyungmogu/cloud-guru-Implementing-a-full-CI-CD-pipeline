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

1. Go back to Jenkins Home page

2. From the sidebar menu, click `New Item`

<img src="https://user-images.githubusercontent.com/6856382/225217986-0f04031a-8ca8-43a5-ab9c-4d58a1df2112.png">
