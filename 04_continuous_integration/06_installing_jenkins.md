# Installing Jenkins

## Instructions

1. Install Java, epel-release package, and jenkins

```
sudo yum install -y epel-release java-11-openjdk
sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
sudo yum -y install jenkins
```

2. Start Jenkins

```
sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
```

3. Create an Administrator account
- a. Navigate to Jenkins UI

```
<JENKINS_PUBLIC_IP>:8080
```

- b. Copy temporary password given by Jenkins

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

5. Click Install suggested plugins

6. Start using jenkins