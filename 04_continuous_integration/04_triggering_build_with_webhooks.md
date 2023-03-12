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

2. Add Github server in Jenkins for Github.com
3. Create a Jenkins credentials with the token and configure the Github Server configuration to use it
4. Check "Manage Hooks" for the Github server configuration
5. In the project configuration, under "Build Trigger", select "GitHub hook trigger for GITScm Polling"

#