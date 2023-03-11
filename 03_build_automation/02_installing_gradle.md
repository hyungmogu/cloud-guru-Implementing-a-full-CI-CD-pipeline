# Installing Gradle

## Instruction

1. Make sure jdk version 8 or higher is installed
- if not, the it can be installed from [here](https://gradle.org/install/)

### Installer-based installation

2. Install gradle
- More instruction can be found [here](https://gradle.org/install/)

**Mac OS**
```
brew install gradle
```

**Unix Distributions**
```
sdk install gradle 8.0.2
```

### Manual Installation

1. install java-jre and java-jdk [here](https://www.digitalocean.com/community/tutorials/how-to-install-java-with-apt-on-ubuntu-22-04)

2. install unzip

```
sudo apt install unzip
```

3. download Gradle following [this](https://gradle.org/install/)

```
wget -O ~/gradle-8.0.2-bin.zip https://services.gradle.org/distributions/gradle-8.0.2-bin.zip
sudo mkdir /opt/gradle
sudo unzip -d /opt/gradle ~/gradle-8.0.2-bin.zip
ls /opt/gradle/gradle-8.0.2
```

4. add PATH

**Setting up /etc/profile.d/gradle.sh**
```
sudo vim /etc/profile.d/gradle.sh
sudo chmod 755 /etc/profile.d/gradle.sh 
```

*/etc/profile.d/gradle.sh*
```
export PATH=$PATH:/opt/gradle/gradle-4.7/bin
```

## Instruction - Notes

- `/opt/` directory means "reserved for the installation of add-on application software packages"

**Example**
```
/opt/gradle
```

- `/etc/profile.d/<app_name>.sh` adds `<app_name>` to PATH permanently.
- `/etc/proifle.d/<app_name>.sh` starts `<app_name>.sh` after load

**Example**
```
/etc/profile.d/gradle.sh
```

**Setting up /etc/profile.d/gradle.sh**
```
sudo vim /etc/profile.d/gradle.sh
sudo chmod 755 /etc/profile.d/gradle.sh 
```

*/etc/profile.d/gradle.sh*
```
export PATH=$PATH:/opt/gradle/gradle-4.7/bin
```

## Gradle Wrapper / Gradle Init

- `Gradle wrapper` is a script that invokes a declared version of Gradle, downloading it beforehand if necessary
- `Gradle wrapper` allows gradle to install itself using just the files from your project's source control
- `Gradle wrapper` is useful because:
    1. It removes the need to have Gradle installed beforehand in order to run the build
    2. Ensures the project is always built with a specific version of Gradle
    3. Lets you build multiple projects with different Gradle version on one system
    4. Anyone (or any automated process) can run the build quickly and easily - they only need Java


## Instruction - installing Gradle Wrapper

### With Gradle Installed

1. go to project root directory of choice

```
cd /project/root/directory/of/choice
```

2. start gradle wrapper

```
gradle wrapper
```

- If installed following instruction from gradle site, the command changed and is now `gradle init`

```
gradle init
```

3. add the following line to `.gitignore`

**/project/root/directory/of/choice/.gitignore**
```
.gradle
```

4. Run gradle builds

**/project/root/directory/of/choice/**
```
./gradlew build
```

#