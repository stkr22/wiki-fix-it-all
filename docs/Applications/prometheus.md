# Prometheus

## install Grafana and Prometheus

Sources:

- <https://kubernetes.github.io/ingress-nginx/user-guide/monitoring/>
- <https://www.fosstechnix.com/install-prometheus-and-grafana-on-kubernetes-using-helm/>
- <https://github.com/prometheus-operator/kube-prometheus>

## Prometheus Metrics

Sources:

- <https://cert-manager.io/docs/usage/prometheus-metrics/>

## Kubernetes

### Prometheus watching all namespaces

After consulting stakeoverflow as well as the github repo it turned out that the documentation is completely outdated. If one wants prometheus to watch all namespaces for services and pods etc. then we need to alter the null value treatment:

``` yaml
prometheusSpec:
  ruleSelectorNilUsesHelmValues: false
  serviceMonitorSelectorNilUsesHelmValues: false
  podMonitorSelectorNilUsesHelmValues: false
  probeSelectorNilUsesHelmValues: false
```

Sources:

- <https://github.com/helm/charts/issues/20581#issuecomment-610370687>
- <https://github.com/prometheus-operator/prometheus-operator/blob/master/Documentation/api.md#namespaceselector>
- <https://stackoverflow.com/a/65648944>
- <https://github.com/prometheus-operator/prometheus-operator/issues/2890#issuecomment-583006452>
- <https://stackoverflow.com/a/60710751>
