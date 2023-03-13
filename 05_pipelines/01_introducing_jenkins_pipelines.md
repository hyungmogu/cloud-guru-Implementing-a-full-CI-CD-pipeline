# Introducing Jenkins Pipeline

- `Pipeline` adhere to the best practice of infrastracture as code
- `Pipeline` is implemented in a file that is kept in source control along with the rest of the application code. This is called `Jenkinsfile`
- `Pipeline` project is created by selecting `pipeline` or `Multibranch Pipeline`

## What Goes in a Jenkinsfile?

- `Pipeline` has a **doman-specific-language (DSL)** that is used to define pipeline logic
- `Pipeline` syntaxes has two types
    1. Scripted - like procedural code
    2. Declarative