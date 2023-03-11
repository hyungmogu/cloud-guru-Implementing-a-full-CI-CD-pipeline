# Automated Testing

- `automated testing` is the automated execution of tests that verify the quality and stability of codes

- `automated testing` has multiple types
    1. unit tests
    2. integration tests
        - making sure that larger portions of applications are integrated with each other
    3. smoke tests / sanity tests
        - high-level integration tests that verify basic, large scale things such as
            1. whether application is running
            2. whether application is returning https protocol, and so on

- `automated testing` can be done using gradle

**Syntax**
```
task build {
    doLast {
        ...
    }
}

task test {
    doLast {
        ...
    }
}

build.dependsOn test
```

```
./gradlew build
```

#