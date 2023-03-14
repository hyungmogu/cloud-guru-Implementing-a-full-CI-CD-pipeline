# Building Jenkins Profile


## Notes
- `Jenkinsfile` is run in either `Pipeline` or `Multibranch Pipeline` item 
- `Jenkinsfile` is required to create a pipeline
- `Jenkinsfile` should be created at the project root folder
- `Jenkinsfile` example pipeline code include

```
pipeline {
  agent any
  stages {
    stage ('Build') {
      steps {
        echo 'Running build automation'
        sh './gradlew build --no-daemon'
        archiveArtifacts artifacts: 'dist/trainSchedule.zip'
      }
    }
  }
}
```

## Notes - Getting API Key from Github

- API key from Github is required to download and run project containing jenkinsfile
- API key from Github is acquired by
    1. going to settings:

    <img src="https://user-images.githubusercontent.com/6856382/224904182-170dd378-3e99-422b-82da-982e3f7b7e57.png">

    2.  click `Developer Settings`

    <img src="https://user-images.githubusercontent.com/6856382/224904438-c8353a4e-6fa0-4cad-9d55-70a2cd1e0887.png">

    3. click `Personal access tokens.`

    4. click `Generate new token`

    <img src="https://user-images.githubusercontent.com/6856382/224904667-bf3645db-de28-46e2-a983-1832e456acab.png">

    5. name the hook, and select `admin:repo_hook`

    <img src="https://user-images.githubusercontent.com/6856382/224904900-85e82fd4-33ce-4e53-aad4-2e5945d516da.png">

    6. Copy generated token

    7. back at jenkins `multibranch pipeline new item` page, select `git` under `branch sources`

    <img src="https://user-images.githubusercontent.com/6856382/224906036-434ecdd3-6daf-4d13-881a-32889e63f73b.png">

    8. add credentials under `jenkin`

    <img src="https://user-images.githubusercontent.com/6856382/224908427-f5abfa40-bd09-438a-b090-4ad5a9afe03f.png">

    9. under `Username with password` enter the following info:
        - Username: Enter your GitHub username.
        - Password: Paste in the API key you copied before.
        - ID: github_key (or any unique id to distinguish this webhook from others)
        - Description: GitHub key

    10. Place all the information we've gathered

    <img src="https://user-images.githubusercontent.com/6856382/224909197-24940552-eea4-4995-948f-abdbe3e55993.png">

#