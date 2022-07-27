# Kubernetes General

## Translate with Komposer

curl -L <<https://github.com/kubernetes/kompose/releases/download/v1.22.0/kompose-linux-amd64> -o kompose>

chmod +x kompose
sudo mv ./kompose /usr/local/bin/kompose

Sources:

- <https://github.com/kubernetes/kompose>
- <https://kompose.io/getting-started/>

## When to use '"' in annotations and options

Use when necessary (boolean and numbers). In most cases it is unnecessary and might cause problems.

Sources:

- <https://blog.jbrosi.dev/2019-3-nginx-ingress-rancher-basic-auth/>

## What are labels and how do they work?

No idea, however, setting the wrong labels will result in resources not working together. It is important to set the same labels on collaborative resources like pods/services

``` yaml
selector:
      matchLabels:
          app: {{ APP_NAME }}
    template:
      metadata:
          labels:
              app: {{ APP_NAME }}
```

Labels are not used for core functionality and server mainly the purpose of helping the user to identify objects. Like this:

``` bash
kubectl get pods -l environment=production,tier=frontend
```

In some cases like for services labels are used to identify the pods they belong to and thus can serve. If a service and a pod do not match in terms of their labels the service might not be able to find the pod.

Kubernetes also has a set of recommended labels:

``` yaml
app.kubernetes.io/name: mysql
app.kubernetes.io/instance: mysql-abcxzy
app.kubernetes.io/version: "5.7.21"
app.kubernetes.io/component: database
app.kubernetes.io/part-of: wordpress
app.kubernetes.io/managed-by: helm
app.kubernetes.io/created-by: controller-manager
```

Sources:

- <https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/>
- <https://kubernetes.io/docs/concepts/overview/working-with-objects/common-labels/>

## Delete multiple resouce types from namespace

``` bash
kubectl delete --namespace middleware --all deployments,pv,pvc,svc,ing
```

Use pattern matching for multiple resources and save delete:

``` bash
kubectl delete pods -l app={{ LABEL_VALUE }} -n default
```

Sources:

- <https://github.com/kubernetes/kubernetes/issues/39084>
- <https://stackoverflow.com/questions/59473707/kubenetes-pod-delete-with-pattern-match-or-wilcard>

## Label a node and node affinity

To make sure a Pod is always scheduled on a specific node one can use label:

``` bash
kubectl label node k8s-node04 {{ LABEL_KEY }}={{ LABEL_VALUE }}
```

In our config yaml we use the affinity setting:

``` yaml
nodeAffinity:
requiredDuringSchedulingIgnoredDuringExecution:
    nodeSelectorTerms:
    - matchExpressions:
    - key: {{ LABEL_KEY }}
        operator: In
        values:
        - {{ LABEL_VALUE }}
```

Sources:

- <https://nikdoof.com/posts/2021/zigbee2mqtt-on-kubernetes/>

## How to control CronJob retries

backoffLimit means the number of times it will retry before it is considered failed. The default is 6.

concurrencyPolicy set to Forbid means it will run 0 or 1 times, but not more.

restartPolicy set to Never means it won't restart on failure.

Sources:

- <https://stackoverflow.com/questions/51657105/how-to-ensure-kubernetes-cronjob-does-not-restart-on-failure>
