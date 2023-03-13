# Jenkin Pipelines and Steps

1. `Pipeline Stages` are large pieces of the CD process
2. `Pipeline Stages` include
    - Build the code
    - Test the code
    - Deploy to Staging
    - Deploy to Production


## Pipeline Steps

- `Pipeline Steps` are the individual tasks that make up each stage
- `Pipeline Steps` include
    1. Execute a command
    2. Copy files to a server
    3. Restart a service
    4. Wait for input from human

- `Pipeline Steps` are implemented via declarative keyword in Jenkinsfile DSL

**Example**
```
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
    }
}

```