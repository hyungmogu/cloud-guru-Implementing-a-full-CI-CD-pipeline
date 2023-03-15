# Implementing Automated Deployment Jenkins Pipeline

- This part of exercise is about `continuous deployment`
- This part of exercise already includes `continuous integration`
- This part of exercise tasks user to
    1. Perform CI build and produce a deployable zip file
    2. Deploy the application to staging server
    3. Wait for approval before deploying to production
    4. Once approved, deploy to production

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

<img src="">

