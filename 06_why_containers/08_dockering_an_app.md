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

1. Add ssh-key
- if don't have it, create one thorough [here](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

```
eval $(ssh-agent -s)
ssh-add ~/.ssh/<ssh_private_key>
```

2. clone repository

```
git clone <forked_repo_git_address>
```

3. in project root folder, build docker image

```
cd cicd-pipeline-train-schedule-docker
sudo docker build -t <docker_hub_username>/train-schedule .
```

4. Run the current docker image

```
docker run -p 8080:8080 -d <docker_hub_username>/train-schedule
```