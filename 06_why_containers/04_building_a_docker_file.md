# Building a Dockerfile

- Important syntaxes to know
    1. `FROM <IMAGE_NAME>` - sets the parent image
    2. `WORKDIR <DIRECTORY_PATH>` - sets the current working directory inside the container image for another command
    3. `COPY <SOURCE> <DESTINATION>` - Copies files from the host to the container image
    4. `RUN <COMMAND>` - executes a command within the container image
    5. `EXPOSE <PORT>` - tells docker that the softwre in the container listens on a particular port
    6. `CMD <ARRAY_OF_CMD_ARGUMENTS>` - sets the command that is executed by the cotnainer when it is run

## Example

```
FROM node:carbon
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 8080
CMD ["npm", "start"]
```

```
docker built -t willbla/train-schedule .
docker run -p 8080:8080 -d willbla/train-schedule
```