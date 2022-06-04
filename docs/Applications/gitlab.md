# Gitlab

## Security

Go to Admin Area > Settings > General and expand Sign-up restrictions.
Clear the Sign-up enabled checkbox, then select Save changes.

Sources:

- <https://stackoverflow.com/questions/46195852/how-to-run-gitlab-in-docker-container-with-nginx-proxy-over-ssl-with-letsencrypt>
- <https://docs.gitlab.com/omnibus/settings/nginx.html>
- <https://docs.gitlab.com/omnibus/docker/>
- <https://docs.gitlab.com/omnibus/settings/configuration.html#setting-initial-root-password-on-installation>-
- <https://docs.gitlab.com/ce/user/admin_area/settings/sign_up_restrictions.html#enable-or-disable-soft-email-confirmation>
- <https://docs.gitlab.com/omnibus/settings/backups.html>

## Using external storage

As written by gitlab, it is sufficient to just use appConfig.object_store.connection for specifying the secret. It will then be used by all services for storing data.

Sources:

- <https://docs.gitlab.com/charts/charts/globals.html#consolidated-object-storage>
- <https://docs.gitlab.com/charts/advanced/external-object-storage/index.html#docker-registry-images>

## Kaniko

### Basics

Sources:

- <https://stackoverflow.com/questions/3162385/how-to-split-a-string-in-shell-and-get-the-last-field>
- <https://docs.gitlab.com/ee/ci/variables/predefined_variables.html>
- <https://docs.gitlab.com/ee/ci/docker/using_kaniko.html>
- <https://docs.gitlab.com/ee/ci/jobs/job_control.html>
- <https://stackoverflow.com/questions/63483953/gitlab-ci-only-trigger-only-merge-request-specific-branch>

### Gitlab CI variables for kaniko

Create Environment scope
Create new personal access toeken
Projekt settings > CI/CD > Variables

Sources:

- <https://gitlab.com/guided-explorations/containers/kaniko-docker-build/-/tree/master>
- <https://docs.gitlab.com/ee/api/project_level_variables.html>
- <https://docs.gitlab.com/ee/ci/environments/index.html>
- <https://stackoverflow.com/questions/37468084/what-is-the-special-gitlab-ci-token-user>
- <https://www.youtube.com/watch?v=d96ybcELpFs>

## Kubernetes

### Using external cert manager

Sources:

- <https://gitlab.com/gitlab-org/charts/gitlab/blob/master/doc/installation/tls.md>

### Using external nginx

Sources:

- <https://docs.gitlab.com/charts/advanced/external-nginx/#helm-deployment>

### Backups

It is important to backup the rails secrets once an instance is set up:

``` bash
kubectl get secrets gitlab-rails-secret -o jsonpath="{.data['secrets\.yml']}" | base64 --decode > {{ PATH_TO_SECRET }}
```

Manual

``` bash
kubectl exec --namespace gitlab -it {{ GITLAB_RUNNER_POD_NAME }} -- backup-utility
```

or using conr based helm chart settings.

Secrets also need to be updated

Sources:

- <https://docs.gitlab.com/ee/raketasks/backup_restore.html#back-up-gitlab>
- <https://gitlab.com/gitlab-org/charts/gitlab/blob/master/doc/backup-restore/backup.md#cron-based-backup>
- <https://docs.gitlab.com/charts/backup-restore/#object-storage>
- <https://gitlab.com/gitlab-org/charts/gitlab/blob/master/doc/backup-restore/backup.md#backup-the-secrets>

### Restore

It is vital to always restore to the same gitlab version! Double check the corresponding application version to the chart version you are using.

First, restore gitlab rails secrets and note that you cannot use relative path like ~/Downloads:

``` bash
kubectl delete secret gitlab-rails-secret
kubectl create secret generic gitlab-rails-secret --from-file=secrets.yml={{ SECRET_PATH }}
```

Next restart pods:

``` bash
kubectl delete pods -lapp=sidekiq,release=gitlab
kubectl delete pods -lapp=webservice,release=gitlab
kubectl delete pods -lapp=task-runner,release=gitlab
```

Next upload the backup tar to the gitlab-backups bucket, e.g.:

``` bash
mc alias set gitlab-minio-restore https://{{ MINIO_URL }}:443 \
  $(kubectl get secret --namespace gitlab gitlab-minio-secret -o jsonpath="{.data.accesskey}" | base64 --decode) \
  $(kubectl get secret --namespace gitlab gitlab-minio-secret -o jsonpath="{.data.secretkey}" | base64 --decode)
```

``` bash
mc cp {{ SOURCE_PATH }} {{ MINIO_BACKUP_PATH }}
```

Next execute the backup:

``` bash
kubectl exec $(kubectl get pods -lrelease=gitlab,app=task-runner -o jsonpath="{.items[0].metadata.name}") -it -- backup-utility --restore -t 1634936403_2021_10_22_14.3.3
```

restart pods:

``` bash
kubectl delete pods -lapp=sidekiq,release=gitlab
kubectl delete pods -lapp=webservice,release=gitlab
```

Sources:

- <https://docs.gitlab.com/charts/backup-restore/restore.html>
