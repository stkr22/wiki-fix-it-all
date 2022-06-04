# Kubernetes Pods

## Run a command inside a pod

``` bash
kubectl exec -stdin --namespace {{ NAMESPACE }} --tty {{ POD_NAME }} -- /bin/bash -c "echo 1;"
```

as another user:

``` bash
kubectl exec --stdin --namespace {{ NAMESPACE }} --tty {{ POD_NAME }} -- su -s /bin/bash www-data -c "php occ --version"
```

Sources:

- <https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/>
- <https://unix.stackexchange.com/a/372851>
- <https://github.com/kubernetes/kubernetes/issues/30656#issuecomment-738687664>

## Restart a pods/statefulset/deployment

Use:

``` bash
kubectl rollout restart statefulset
```

Sources:

- <https://stackoverflow.com/questions/59168406/kubectl-rollout-restart-for-statefulset>

## Update deployments

You simply update the image tag in the pods description

``` bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.16.1 --record
```

Sources:

- <https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment>

## Copy to container inside pods from local

``` bash
kubectl cp --namespace {{ NAMESPACE_NAME }} {{ PATH_TO_LOCAL_FILE }} {{ POD_NAME }}:{{ POD_PATH }} -c {{ CONTAINER_NAME }}
```

Sources:

- <https://kubernetes.io/docs/reference/kubectl/cheatsheet/?ref=hackernoon.com#copy-files-and-directories-to-and-from-containers>

## Get Logs

``` bash
kubectl logs --namespace {{ NAMESPACE }} {{ POD_NAME }}
```

``` bash
kubectl logs -f deployment/{{ name-of-deployment }}
```

Sources:

- <https://stackoverflow.com/questions/60518658/how-to-get-logs-of-deployment-from-kubernetes#62831466>

## Get logs of failed containers

``` bash
sudo more /var/log/containers/
```

Sources:

- <https://stackoverflow.com/a/64626323>

## Run a preStartHook

Always keep in mind that the very first argument in the command call is the binary and the binary only!! That works:

``` yaml
postStartCommand: ["/bin/sh", "-c", "echo 1"]
```

That too:

``` yaml
postStart:
    exec:
        command: ["/bin/sh", "-c", "echo 1"]
```

This does not

``` yaml
postStartCommand: ["/bin/sh -c", "echo 1"]
```

## Get a pod with bash for debugging

``` bash
kubectl run -i --tty alpine --image=alpine --restart=Never -- sh
```

Sources:

- <https://kubernetes.io/docs/reference/kubectl/cheatsheet/#interacting-with-running-pods>
- <https://kubernetes.io/blog/2015/10/some-things-you-didnt-know-about-kubectl_28/#run-interactive-commands>

## Analyze Pod Pending

Can potentially be found to be struggling with *insufficient cpu*. Check the cpu usage with:

``` bash
kubectl get nodes
kubectl describe node {{ NODE_NAME }}
```

Sources:

- <https://stackoverflow.com/questions/38869673/pod-in-pending-state-due-to-insufficient-cpu>

## How to use and set resource limitations in kubernetes

Use this command to evaluate the current usage of container. This can potentially be used to evaluate the real usage.

``` bash
kubectl top pod {{ POD_NAME }} --namespace {{ NAMESPACE  }}
```

Sources:

- <https://cloud.google.com/blog/products/containers-kubernetes/kubernetes-best-practices-resource-requests-and-limits>
- <https://kubernetes.io/docs/tasks/configure-pod-container/assign-memory-resource/#if-you-do-not-specify-a-memory-limit>
