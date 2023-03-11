# Installing Gradle

## Instruction

1. Make sure jdk version 8 or higher is installed
- if not, the it can be installed from [here]()

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

## Note

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

## Gradle Wrapper

