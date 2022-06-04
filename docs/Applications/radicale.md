# Radicale

## Config

Radicale is a simple CalDav/CardDav [Server](https://github.com/tomsquest/docker-radicale). I had several issues with authentication and nginx-ingress. Most of my issues originated from the fact that nginx strips most headers from the answer it sends back to the service/pod. In my case the authentication headers were often lost and radicale treated me as an unauthenticated user.

In the end I could solve this by adding the following annotation:

``` yaml
nginx.ingress.kubernetes.io/configuration-snippet: |-
  proxy_set_header X-Remote-User $remote_user;
```

Config radicale to expect X-Remote-User by:

```conf
type = http_x_remote_user
```

I decided against nginx and I am now using radicale to make use to the delay option. The secret is mounted inside the container and nginx is using:

``` yaml
nginx.ingress.kubernetes.io/configuration-snippet: |-
  proxy_pass_header Authorization;
```

In general I think the best solution is to use keycloak in combination with unicorn for auth but that is more of a longterm solution.

Sources:

- <https://github.com/h3ndrk/radicale-k8s>
- <https://radicale.org/3.0.html#tutorials/reverse-proxy>

## Backup

### Rclone

Install rclone

``` bash
curl https://rclone.org/install.sh | sudo bash
```

Configuration should look like this:

```conf
[minio-backup]
type = s3
provider = Minio
access_key_id = key
secret_access_key = key2
endpoint = https://{{ MINIO_URL }}
acl = private
```

set new crontab

``` bash
rclone move minio-backup:backups/ /mnt/backup/radicale
```

Sources:

- <https://rclone.org/seafile/>
