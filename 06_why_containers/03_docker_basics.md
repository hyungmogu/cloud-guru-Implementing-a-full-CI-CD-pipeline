# Docker Basics

- `Image` is a package of the software and everything that it needs in order to run
- `Image` exists in a hierarchy. This means most image have a parent image
- `Image` can be used to cover most of what's needed for the running of application
- `Container` is an instance of an image

## Building and Running Docker

- `Image` is built using the following Syntax

**Syntax**
```
docker build -t <docker_username>/<image_name> <Docker_file_path>
```

**Example**
```
docker built -t test-user/test-img .
```

- `Container` is run using the following command

**Syntax**
```
docker run -d <docker_username>/<image_name>
```

## Useful docker commands

1. `docker ps` - shows all running containers
2. `docker stop <container_id>` - stops a container

## Pushing to Docker Registry

- Docker image can be uploaded to either
    1. Private repository (AWS)
    2. Official cloud registry (Docker Hub)

- To upload it to official docker registry,

**Syntax**
```
docker login --username=<hub_username> --email=<hub_email>
```

