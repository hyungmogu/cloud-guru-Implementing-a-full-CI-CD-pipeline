# Dockering an App

## Steps

### Creating Dockerfile for the application

1. go to [github source code](https://github.com/linuxacademy/cicd-pipeline-train-schedule-docker) and fork the repo

2. Inside the forked repo, create new `Dockerfile`

3. Add the following lines of code to newly created `Dockerfile`

**/PROJECT_REPO/Dockerfile**
```
FROM node:carbon
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 8080
CMD [ "npm", "start" ]
```

4. Commit changes

### Building a Docker Image Using the Dockerfile and Run it

