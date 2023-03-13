# Deployment With Jenkins Pipelines

## Continuous Delivery

- `Continuous Delivery` means you are always able to deploy any version of your code
- `Continuous Delivery` is done by automating the deployment process using Jenkins pipeline

## Automating Deployments in a Pipeline


1. Define stages of the CD process that invole deploying
    - Example
        1. if there is two servers - staging server and production server - implement stages called
            1. Deploy to Production
            2. Deploy to Staging

2. Define steps that performs the tasks necessary to carry out the deployment
    - Example
        1. Copy files to servers
        2. Restart a service

3. (optional) ask user for approval before performing the actual production deployment

#