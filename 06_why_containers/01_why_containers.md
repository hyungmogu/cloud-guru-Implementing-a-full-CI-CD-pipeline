# Why Containers

## Problems of Current Approach

1. Manual initial configuration of servers
2. Software updates or any everys that break production server
    - There is no way to quickly and easily replace it
    - Resulting in downtime
3. No degree-of-confidence about producting environment working the same as test environment
    - The current setup is vulnerable to `Configuration Drift`

## Solution - Docker / Container

1. `Container` is a light-weight virtualization tool
2. `Container` distributes the whole app in a ready-to-run state
3. `Container` allows easier automation and orchestration