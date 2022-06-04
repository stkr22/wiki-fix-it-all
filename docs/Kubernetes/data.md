# Kubernetes Data

## How to store sensitive information?

### How to store secrets in Kubernetes?

``` bash
kubectl create secret generic dev-db-secret \
  --from-literal=username={{ USERNAME }} \
  --from-literal=password='{{ PASSWORD }}'
```

generate random secret:

``` bash
kubectl create secret generic minio-creds-secret \
  --from-literal=accesskey=$(head -c 512 /dev/urandom | LC_CTYPE=C tr -cd 'a-zA-Z0-9' | head -c 20)
```

or from file:

``` bash
kubectl create secret generic db-user-pass \
  --from-file=username=./username.txt \
  --from-file=password=./password.txt
```

Sources:

- <https://kubernetes.io/docs/concepts/configuration/secret/>
- <https://kubernetes.io/docs/tasks/configmap-secret/managing-secret-using-kubectl/>
- <https://docs.gitlab.com/charts/installation/secrets#minio-secret>

### How to access secrets?

``` bash
kubectl describe secret {{ SECRET }}
kubectl get secret {{ SECRET }} -o jsonpath='{.data}'
```

Sources:

- <https://phoenixnap.com/kb/kubernetes-secrets>

### Encrypt private repository secrets

Use Docker Login first and then use the resulting dockerconfig json file as reference.

``` bash
kubectl create secret generic regcred \
  --from-file=.dockerconfigjson={{ path/to/.docker/config.json }} \
  --type=kubernetes.io/dockerconfigjson
```

Alternatively create the secret directly

``` bash
kubectl create secret docker-registry regcred --docker-server={{ your-registry-server }} --docker-username={{ your-name }} --docker-password={{ your-pword }} --docker-email={{ your-email }}
```

Sources:
<https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/>

### How to encrypt secrets at rest?

Sources:

- <https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/>

### How to create a basic auth secret?

``` bash
USER=$(head -c 512 /dev/urandom | LC_CTYPE=C tr -cd 'a-zA-Z0-9' | head -c 10); PASSWORD=$(head -c 512 /dev/urandom | LC_CTYPE=C tr -cd 'a-zA-Z0-9' | head -c 64); MYAUTH="${USER}:$(openssl passwd -stdin -apr1 <<< ${PASSWORD})"
kubectl create secret generic --namespace {{ NAMESPACE  }} {{ SECRET_NAME }} \
    --from-literal=users=$MYAUTH
```

``` bash
USER={{ USERNAME }}; read -s -p "password: " PASSWORD; MYAUTH="${USER}:$(openssl passwd -stdin -apr1 <<< ${PASSWORD})"
```

Sources:

- <https://longhorn.io/docs/1.1.2/deploy/accessing-the-ui/longhorn-ingress/>

## Configmaps

### Use configmaps in an read and write setting

SOmetimes you might want to use a configmap as config file for an application. Unfortunately the application needs to be able to write to that file.

One possible solution I found is using an init container:

``` yaml
initContainers:
  - name: config-creator
    image: busybox:latest
    imagePullPolicy: IfNotPresent
    args:
    - 'echo ''Setting Configmaps'' && mkdir -p {{ PATH_TO_CONFIG }} && cp {{ PATH_TO_CONFIGMAP }} {{ PATH_TO_CONFIG_FILE }}'
    command:
      - sh
      - -c
    volumeMounts:
    - name: data
      mountPath: {{ PATH_TO_CONFIG }}
    - mountPath: {{ PATH_TO_CONFIGMAP }}
      name: config
      subPath: config.conf
```

First we use a busybox image which simply copies the content of our configmap to a folder which it shares with the container that is using the actual configmap.

Sources:

- <https://pabraham-devops.medium.com/mapping-kubernetes-configmap-to-read-write-folders-and-files-8a548c855817>

### Using stakater reloader updating config_maps/secrets

Sources:

- <https://github.com/stakater/Reloader>

## How to setup Persistent Volume Claims(PVC) and Mounts?

### How to install longhorn?

First install iscsi manager

``` bash
aptitude install open-iscsi
```

Longhorn is used to allow volumes persistsnce

Pleas note, for this checkup to work correctly you need to run the check script as root.

``` bash
aptitude install jq
```

``` bash
curl -sSfL https://raw.githubusercontent.com/longhorn/longhorn/v1.1.1/scripts/environment_check.sh | bash
```

Ingress controller for the dashboard must be created manually

Sources:

- <https://longhorn.io/docs/1.1.1/deploy/install/#using-the-environment-check-script>
- <https://longhorn.io/docs/1.1.1/deploy/install/install-with-helm/>
- <https://longhorn.io/docs/1.1.1/deploy/accessing-the-ui/longhorn-ingress/>
- <https://longhorn.io/docs/1.1.1/deploy/install/#installing-open-iscsi>

### Set default storage class in cluster

first check default storage class in cluster:

``` bash
kubectl get storageclass
```

Next set desired storage class / undefault previous default

``` bash
kubectl patch storageclass local-path -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'
```

It seems like kubernetes is automatically resetting this which in turn makes completely removing local path the safest option.

Sources:

- <https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/>
- <https://github.com/k3s-io/k3s/issues/2767>

### How to use PVCs?

Always use manual PVCs and existing claims in helm charts. That way you can be sure that the PVC is not accidentially dropped and the helm chart is always using the correct one.

``` yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
name: longhorn-volv-pvc
spec:
accessModes:
    - ReadWriteOnce
storageClassName: longhorn
resources:
    requests:
    storage: 2Gi
```

Sources:

- <https://rancher.com/docs/k3s/latest/en/storage/>

### Mounting Subpath vs Mount

Sources:

- <https://dev.to/joshduffney/kubernetes-using-configmap-subpaths-to-mount-files-3a1i>

### Subpath mounting

mounting a folder automatically leads to all existing contents to vanish. Like Mounting the folder /opt/something will lead to all contents of something to vanish. If this is not wanted one could instead use subPath. This avoids the aformentioned issues:

``` yaml
- name: {{ MOUNT_NAME }}
  mountPath: {{ /opt/yourfile.py }}
  subpath: {{ yourfile.py }}
```

Sources:

- <https://stackoverflow.com/questions/33415913/whats-the-best-way-to-share-mount-one-file-into-a-pod>

### What is ReadOnlyOnce / ReadOnlyMany / ReadWriteMany ?

First the the Once/Many is always about then number of nodes accessing a volume not the pods. On the same node many pods can always access a volume.

Sources:

- <https://stackoverflow.com/a/57799347>
- <https://stackoverflow.com/a/44322967>

### How to mount a secret as env variables?

``` yaml
env:
  - name: mysecretenv
    valueFrom:
      secretKeyRef:
      name: mysecret
      key: password
```

Sources:

- <https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets-as-environment-variables>

### How to mount a secret as files?

``` bash
kubectl create secret generic --namespace {{ NAMESPACE  }} {{ SECRET_NAME }} \
    --from-literal=users=$MYAUTH
```

Secrets can only be mounted fully so not as a single file in a folder

``` yaml
volumeMounts:
  - name: users
    mountPath: /secret/
    readOnly: true
    volumes:
    - name: users
    secret:
      secretName: {{ SECRET_NAME }}
```

Sources:

- <https://www.jeffgeerling.com/blog/2019/mounting-kubernetes-secret-single-file-inside-pod>
