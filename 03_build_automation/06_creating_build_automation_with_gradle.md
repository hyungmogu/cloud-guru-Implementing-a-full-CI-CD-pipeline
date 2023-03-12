# Creating Build Automation with Gradle


- This automation needs to do:
    1. have a build task that can be called to execute the entire build process
    2. execute some automated tests (the build should fail if any of te tests fail)
    3. Pacakge the code and it's dependencies into a zip archive called dist/trainSchedule.zip

## Pretask

1. install java

```
sudo yum check-update
sudo yum install java-11-openjdk-devel
sudo yum install java-11-openjdk
```

2. install unzip

```
sudo yum check-update
sudo yum install unzip
```

3. install gradle

```
wget -O ~/gradle-8.0.2-bin.zip https://services.gradle.org/distributions/gradle-8.0.2-bin.zip
sudo mkdir /opt/gradle
sudo unzip -d /opt/gradle ~/gradle-8.0.2-bin.zip
ls /opt/gradle/gradle-8.0.2
```

4. add permanent pah

```
sudo vim /etc/profile.d/gradle.sh
sudo chmod 755 /etc/profile.d/gradle.sh
```

**/etc/profile.d/gradle.sh**
```
export PATH=$PATH:/opt/gradle/gradle-8.0.2/bin
```

5. reconnect to the terminal

6. check if gradle is running properly

```
gradle --version
```

## Instructions

1. configure git for ssh authentication with github.com

```
ssh-keygen -t ed25519 -C "<your_email>@example.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
cat ~/.ssh/id_ed25519.pub
```

- after above process, copy its result to github's ssh-key page


2. Create a personal fork of the sample repository [here](https://github.com/linuxacademy/cicd-pipeline-train-schedule-gradle)

3. clone your personal fork from github

```
git clone git@github.com:hyungmogu/cicd-pipeline-train-schedule-gradle.git
```

4. Initialize the project with github

```
cd cicd-pipeline-train-schedule-gradle
./gradlew init
```

5. Include the `com.moowork.node` plugin in the gradle project


```
plugins {
 id "com.moowork.node" version "1.2.0" 
}

node {
 download = true
}

task build

task zip(type: Zip) {
    from ('.') {
        include "*"
        include "bin/**"
        include "data/**"
        include "node_modules/**"
        include "public/**"
        include "routes/**"
        include "views/**"
    }
    destinationDir(file("dist"))
    baseName "trainSchedule"
}

build.dependsOn zip
zip.dependsOn npm_build
build.dependsOn npm_build
npm_build.dependsOn npmInstall
npm_build.dependsOn npm_test
npm_test.dependsOn npmInstall
```

```
./gradlew build
```

#