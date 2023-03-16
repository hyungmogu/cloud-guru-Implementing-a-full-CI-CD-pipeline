# Installing Docker on Jenkins

## Instructions

1. Login to jenkins server via ssh

2. Install Docker as instructed in docker installation [page](https://docs.docker.com/engine/install/ubuntu/)

3. Make sure docker is working

```
sudo systemctl status docker
```

4. Give Jenkins Permission to use docker

```
sudo groupadd docker
sudo usermod -aG docker jenkins
```

5. Restart jenkins and docker

```
sudo systemctl restart jenkins
sudo systemctl restart docker
```

