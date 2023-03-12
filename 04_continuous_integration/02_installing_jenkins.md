# Installing Jenkins

- Jenkins installation instruction is found [here](https://www.jenkins.io/doc/book/installing/)
- `Jenkins` have several approaches of installation
    1. Docker
    2. War file
    3. Kubernetes
    4. Others


## installation instruction - CentOS 

1. download java jdk

```
sudo yum install -y java-11-openjdk epel-release
```

2. install jenkins

**Example**
```
sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
sudo yum upgrade 
sudo yum install -y jenkins
sudo systemctl daemon-reload
```

3. start jenkins

```
sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
```

4. proceed to post-installation instruction on browser

```
http://<YOUR_PUBLIC_IP>:8080
```

