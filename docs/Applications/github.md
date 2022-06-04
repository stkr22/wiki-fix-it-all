# Github

Use self hosted runners by specifying the runs-on like

``` yaml
jobs:
  build:
    runs-on: [self-hosted, arm64]
```

Sources:

- <https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#choosing-self-hosted-runners>
