# Seafile

## SSO

Also see prepared .json file in seafile folder and config maps with seafile.

Sources:

- <https://de.seafile.com/sso-seafile-keycloak/>
- <https://manual.seafile.com/deploy/oauth/>

## 2FA

Activate in System Settings. Use QTQR for scanning

``` bash
aptitude install qtqr
```

## Backup

### RClone

Configure rclone

``` bash
rclone config
```

```conf
[seafile-backup]
type = seafile
url = https://{{ SEAFILE_URL }}
user = {{ USER_NAME }}
pass =
2fa = true
auth_token = XXXXXXXXXXXXXXXXXXXXXXX
```

With crontab

```conf
@midnight rclone sync seafile-backup: /mnt/backup/seafile/
```

Sources:

- <https://rclone.org/seafile/>

### Seafile CLI

The Seafile CLI seems to be a good way to go. However, I'll have to see if I can get it built yet. Currently the CLI only supports SSO from 8.0.4.

However, it is not available for ARM64. Alternatively the client package may work.

``` bash
sudo add-apt-repository ppa:seafile/seafile-client
aptitude install seafile-cli
```

Sources:

- <https://help.seafile.com/syncing_client/linux-cli/#authenticate-with-tokens>
- <https://code.launchpad.net/~seafile/+archive/ubuntu/seafile-client>
- <https://github.com/haiwen/seafile-client>
- <https://manual.seafile.com/build_seafile/linux/>
- <https://debian.pkgs.org/sid/debian-main-arm64/seafile-gui_8.0.4-1_arm64.deb.html>

## Kubernetes

Sources:

- <https://github.com/300481/seafile-server>
- <https://forum.seafile.com/t/deploying-modern-seafile-to-kubernetes/13986>
