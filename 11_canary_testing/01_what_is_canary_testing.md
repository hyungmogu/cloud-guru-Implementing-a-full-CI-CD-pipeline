# Canary Testing

- `Canary Testing` is a powerful tool when doing continuous integration and continuous delivery
- `Canary Testing` means pushing new code to a small group of real users, usually with those users being unaware that they are using new code
    - This allows to test code in real-world usage
    - This allows to limit impact to small number of users
- `Canary Testing` gives a lot of confidence in deployed environment

## Canary Testing - How it Works

- `Canary Testing` spins up new instances
- `Canary Testing` diverts a small portion of users
- `Canary Testing` runs new code and old code side by side

<img src="https://user-images.githubusercontent.com/6856382/226355870-1c2bad01-8b04-46b4-90ed-ad8678ad567b.png">

- `Canary Testing` applys new code to all instances once conditions are met in a real-world environment
- `Canary Testing` can be done automatically using Kubernetes Cluster

