# Kubernetes Installation

## How to set up k3s

First set up a master. The master installation already includes the worker node on the master.

Check if certificate is correct otherwise run k3s serverwith tls-san

Then install kubectl on your local computer. Check with kubectl cluster-info

Install helm at local computer

## Installation with K3Sup

``` bash
k3sup install --ip {{ HOST_IP }} \
  --user {{ SSH_USER }} \
  --merge \
  --local-path ~/.kube/config --context {{ CONTEXT NAME }} \
  --k3s-version v1.25.5+k3s2 \
  --k3s-extra-args '--disable local-storage --disable servicelb --disable traefik --flannel-backend wireguard-native'
```

Join new node:

``` bash
k3sup join --ip {{ HOST_IP }} \
  --user {{ SSH_USER }} \
  --server-ip {{ SERVER_IP }} \
  --server-user {{ SERVER_SSH_USER }} \
  --k3s-version v1.25.5+k3s2 
```

Einzelnachweise:

- <https://rancher.com/docs/k3s/latest/en/installation/install-options/server-config/#data>
- <https://github.com/alexellis/k3sup>

## Helm

### Use Helm3

Customize charts with yaml

A few words on tillar

The most important change in Helm 3 is the removal of Tiller. In Helm, prior to version 3, Tiller was the service that actually communicated with the Kubernetes API server to manage the Helm packages, and was running as an in-cluster service. The removal of Tiller, basically reduced the cluster risk surface.

Sources:

- <https://helm.sh/docs/intro/using_helm/#customizing-the-chart-before-installing>
- <https://www.alcide.io/helm-3-released-bye-bye-tiller-and-why-secops-should-care/>



### How to get values for a helm template?

To get existing user values:

``` bash
helm get values {{ RELEASE_NAME }} > values.yaml
```

to get default values

``` bash
helm show values {{ CHART_REPOSITORY }} > values.yaml
```

Sources:

- <https://stackoverflow.com/questions/64343656/grafana-dashboard-not-working-with-ingress>

### How to prerender a helm template?

``` bash
helm template {{ RELEASE_NAME }} -f values.yaml {{ CHART_REPOSITORY }} --create-namespace --namespace {{ NAMESPACE }}
```

Sources:

- <https://helm.sh/docs/helm/helm_template/>

### How to get all versions of chart?

``` bash
helm search repo {{ CHART_REPOSITORY }} --versions
```

Sources:

- <https://stackoverflow.com/questions/51031294/for-a-helm-chart-what-versions-are-available>

## Deployment order

1. Longhorn
2. Prometheus + Grafana
3. nginx-ingress
4. cert-manager
5. cert-issuer
6. keycloak
7. minio
8. ...regular applications

## Set auto upgrade

Sources:

- <https://rancher.com/docs/k3s/latest/en/upgrades/automated/>
