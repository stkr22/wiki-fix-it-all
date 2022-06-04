# Nextcloud

Installing Nextcloud using Docker.

Send a file to [shared folder](https://github.com/tavinus/cloudsend.sh)

In Docker Compose together with Redis and Mariadb.

Update container:

``` bash
docker -f nextcloud-docker-compose.yml down
docker-compose pull && docker-compose -f nextcloud-docker-compose.yml up
```

Nginx is initialized with its own network:

``` bash
docker network create nginx-proxy
```

After initialization once

``` bash
bash init.sh
```

Create users with:

``` bash
docker container exec -it --user www-data nextcloud_app php occ user:add --display-name="{{ YOUR_NAME }}" {{ USERNAME }}
```

Move from previous Nextcloud docker:

``` bash
mv /path/to/files /var/lib/docker/volumes/docker-nextcloud_nextcloud-data/_data/{{ USERNAME }}/files/
docker container exec {{ CONTAINER_NAME }} chown -R www-data:www-data /var/nextcloud_data
docker container exec --user www-data {{ CONTAINER_NAME }} php occ files:scan --all
```

create calendars and adressbooks:

``` bash
bash init.sh
```

Sources:

- <https://hub.docker.com/_/redis/?tab=description&page=1&ordering=last_updated>
- <https://github.com/nextcloud/docker>
- <https://github.com/nextcloud/docker/tree/master/.examples>
- <https://www.addictivetips.com/ubuntu-linux-tips/integrate-libreoffice-with-nextcloud/>
- <https://docs.nextcloud.com/server/latest/admin_manual/configuration_server/reverse_proxy_configuration.html>
- <https://dennisnotes.com/note/20180831-nextcloud-docker-nginx-reverse-proxy/>
- <https://github.com/nginx-proxy/docker-letsencrypt-nginx-proxy-companion/blob/master/docs/Docker-Compose.md>
- <https://help.nextcloud.com/t/nextcloud-in-docker-not-accepting-trusted-domains/64208/8>
- <https://docs.nextcloud.com/server/20/admin_manual/configuration_server/config_sample_php_parameters.html>
- <https://docs.nextcloud.com/server/20/admin_manual/configuration_server/occ_command.html#config-commands>
- <https://help.nextcloud.com/t/create-new-calendars-using-lightning-without-the-gui/61055/5>
- <https://help.nextcloud.com/t/birthdays-have-disappeared-from-the-nextcloud-calendar/29779>
