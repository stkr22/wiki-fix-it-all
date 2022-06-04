# Kubernetes Troubleshooting

## Nameserver limit exceeded

Fix by removing namespace entries from /etc/resolv.conf

Sources:

- <https://www.reddit.com/r/kubernetes/comments/7ruq0f/kubelet_nameserver_limits_were_exceeded/>

1. Error: unable to build kubernetes objects from release manifest: unable to recognize "": no matches for kind "ServiceMonitor" in version "monitoring.coreos.com/v1" - Prometheus not yet installed thus resource ServiceMonitor unknown.

## Mounts

### How to troubleshoot?

First check the pvc and pv available:

``` bash
kubectl get pv, pvc
```

Get description of relevant pv and check what their status is

``` bash
kubectl describe pvc {{ my-pvc }}
```

If there are no erros check the longhorn GUI that should show you the claims status. If status is "detached" you need to check the node *status* in **Node**. This might be *unschedulable* if that is the case get the node description.

``` bash
kubectl -n longhorn-system get nodes.longhorn.io
kubectl -n longhorn-system describe nodes.longhorn.io {{ my-node }}
```

Check the disk status in the description. Especially the *Disk Status > {{ DISK_NAME }} > Conditions* most likely the *Schedulable* entry should give you some indication. Possible status could be disk pressure or something similar indicating that there is not enough space on disk.

Sources:

- <https://github.com/longhorn/longhorn/issues/971>
- <https://stackoverflow.com/questions/62215795/pod-has-unbound-immediate-persistentvolumeclaims-repeated-3-times>
- <https://longhorn.io/docs/1.1.1/volumes-and-nodes/multidisk/#configuration>

### Mount Attach failed: volume mount fail with exit status 32

Turned out to be an nfs issues and was solved by installing

``` bash
sudo apt-get install -y nfs-common # nfs-kernel-server
```

Sources:

- <https://stackoverflow.com/questions/34113569/kubernetes-nfs-volume-mount-fail-with-exit-status-32>
- <https://stackoverflow.com/questions/47423453/mountvolume-setup-failed-for-volume-nfs-mount-failed-exit-status-32>

### Mount point busy or already mounted

Seems to be an issues with multipath and can be resolved by altering multipath config.

``` bash
vi /etc/multipath.conf
```

add lines

```conf
blacklist {
    devnode "^sd[a-z0-9]+"
}
```

restart

``` bash
systemctl restart multipathd
```

Sources:

- <https://longhorn.io/kb/troubleshooting-volume-with-multipath/>
