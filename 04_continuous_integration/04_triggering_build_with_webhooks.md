# Triggering Build with Webhooks

- `Webhooks` are event notifications made from one application to another over http
- `Webhooks` allow jenkins to run build whenever there is a change in github
- `Webhooks` in jenkins are easy to setup

## Instructions

1. Create an access token in Github that has permission to read and create webhooks
- a. click `Developer Settings` under `Settings`

<img src="https://user-images.githubusercontent.com/6856382/224565006-403b15a1-f38f-4cfe-a16f-075d39d5fb18.png">

- b. click `personal access tokens`

<img src="https://user-images.githubusercontent.com/6856382/224565294-4310f3f5-09c4-4bc4-af50-f237d4e9049d.png">

- c. click `generate new token` under `Tokens (classic)`

<img src="https://user-images.githubusercontent.com/6856382/224565409-eecd2884-cbe6-4178-a8b1-d7d702d19a7f.png">

- d. name the hook, and select `admin:repo_hook`

<img src="https://user-images.githubusercontent.com/6856382/224565500-439a4499-fd3a-4d59-9bf7-9aed540a80ab.png">

- e. Copy Key

2. Add Github server in Jenkins for Github.com

- a in jenkins, select `manage jenkins`

<img src="https://user-images.githubusercontent.com/6856382/224568174-1c0d5a23-d22b-46fe-9db1-6a14acfc3d15.png">

- b. Select `Configure System`

<img src="https://user-images.githubusercontent.com/6856382/224568337-29850b15-0182-4998-aae2-2f35d7935942.png">

- c. Select `Github Server` underneath `Github` Section

<img src="https://user-images.githubusercontent.com/6856382/224568492-80774c9b-7aae-49fa-a3c7-91f5a0ef1490.png">

- d. Add `Github` to `Name` and Select `Jenkins` under `Credentials`

<img src="https://user-images.githubusercontent.com/6856382/224568609-cb1745b8-fbd1-436a-b726-c9fb91661fe4.png">

- e. Perform following: 
    1. Select `Secrete Text` under `Kind`
    2. Enter Webhook api key gained under step 1 to `Secret`
    3. Enter `github_key` to `ID`
    4. Enter `GitHubKey` to `Description`

<img src="https://user-images.githubusercontent.com/6856382/224569180-348b005b-37e6-44a4-9c66-a072eb797bca.png">

3. Create a Jenkins credentials with the token and configure the Github Server configuration to use it
4. Check "Manage Hooks" for the Github server configuration
5. In the project configuration, under "Build Trigger", select "GitHub hook trigger for GITScm Polling"

