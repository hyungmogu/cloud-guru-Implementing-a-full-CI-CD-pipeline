# Gradle Basics

- `gradle build` is defined in a groovy script called `build.gradle`
- `gradle build` file `build.gradle` is located at the root directory of your project

## Initializing a new project

- `gradle init` command initializes a new project.
- `gradle init` also sets up the gradle wrapper

## Running a Gradle build

- `gradle build` consists of a set of tasks that can be called from commandline:

**Example**
```
./gradlew Task1 Task2
```

- the above commandline will run tasks `Task1` and `Task2`

## Defining Tasks

- `build.gradle` controls what tasks are avilable for your project

**Example (/your/project/root/directory/build.gradle)**
```
task Task1 {
    println 'Hello World'
}
```

## Task Dependencies

- `task dependencies` are used by gradle to determine what tasks are needed to run for a particular build command
- `task dependencies` determine the order in which tasks are run
- `task dependencies` can be defined in `build.gradle` like below:

**Syntax**
```
taskA.dependsOn taskB
```


## Plugins
- `plugins` are available for public browsing [here](https://plugins.gradle.org/)
- `plugins` and a wide variety of them are offered in gradle
- `plugins` are offered mainly by the community
- `plugins` provide pre-built tasks that you can configure to do what you need

**Syntax**
```
plugins {
    id "<plugin id>" version "<plugin version>"
}
```

## Example
- `com.moowork.node` is a plugin that allows to interact with node.js
```
plugins {
    id 'com.moowork.node' version '1.2.0'
}

task hello {
    doLast {
        println "Hello, world!"
    }
}

task anotherTask {
    doLast {
        println "This is another task"
    }
}

hello.dependsOn anotherTask
```

**Commandline**
```
./gradlew hello 
```

**Commandline**
```
./gradlew hello anotherTask 
```

**Commandline (executing tasks from plugin)**
```
./gradlew nodeSetup
```

