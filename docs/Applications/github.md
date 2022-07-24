# Github

Use self hosted runners by specifying the runs-on like

``` yaml
jobs:
  build:
    runs-on: [self-hosted, arm64]
```

start runner on host with docker

``` bash
docker create --name armrunner \
  -e RUNNER_NAME=armrunner \
  -e GITHUB_ACCESS_TOKEN=XXXXXXXXXXXXXXXXXXXX \
  -e RUNNER_REPOSITORY_URL=https://github.com/XXXXXXX \
  -v /var/run/docker.sock:/var/run/docker.sock \
  ghcr.io/stkr22/github-self-hosted-runner:main
```

Sources:

- <https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#choosing-self-hosted-runners>
