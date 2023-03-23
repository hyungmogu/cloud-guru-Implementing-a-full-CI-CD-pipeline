# Fully Automated Deployment

- `Fully Automated Deployment` needs confidence in 
    1. Automation
    2. Testing
    3. Monitoring

- `Fully Automated Deployment` is not desirable in all cases, but in some cases, it can be great

## Instructions - Setting up Fully Automated Deployment

1. On Jenkins web browser page, click `Manage Jenkins` > `Configure System`

2. Scroll down to `Global Properties` and select `Environmental Variables`

<img src="https://user-images.githubusercontent.com/6856382/226923545-c5ab6fbc-0445-44a7-88a3-bcb8df7a820d.png">

3. Add environment variable `KUBE_MASTER` and it's ip address

<img src="https://user-images.githubusercontent.com/6856382/227123499-a2ae64fc-54ea-435a-adad-dcab39104844.png">

4. Scroll down to `Github` section and turn on Github Webhook trigger

<img src="">