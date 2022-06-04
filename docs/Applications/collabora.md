# Collabora

## Using the built-in Server

adding |.+\/richdocumentscode\/proxy seems to be important to nginx.

Sources:

- <https://github.com/nextcloud/documentation/blob/1f7dd0bb814045d37ff38fbb1217274695c79cae/admin_manual/installation/nginx.rst>
- <https://www.collaboraoffice.com/online/connecting-collabora-online-built-in-code-server-with-nginx/>

## Using the external Server

Multiple things seem to be relevant here:

- overwritehost and overwriteprotocoll need to be set in nextcloud otherwise collabora will not recognize the domain if it is coming from behind the reverse proxy(localhost)
- collabora needs to use ssl.terminate and ssl.enable=false to work behind reverseproxy otherwise traffic will not match
- if collabora is used behind nginx with nginx-proxy, it should forward all traffic to collabora in http not https
- generally it seems to work best if collabora is connected right to the nginx-proxy without another in front of it.
- Theoretically it might be possible to use it without another domain and just use the nextcloud domain and referencing it in the same nginx site as the nextcloud but that does not seem advisable.

Sources:

- <https://help.nextcloud.com/t/failed-to-connect-to-collabora-online/53118/13>
- <https://github.com/nextcloud/docker/pull/630>
- <https://github.com/ReinerNippes/nextcloud_on_docker>
- <https://help.nextcloud.com/t/collabora-nextcloud-dockerized-http-requests-blocked-by-iptables-behind-reverse-proxy/90278/6>
- <https://icewind.nl/entry/collabora-online/>
- <https://help.nextcloud.com/t/nginx-collabora-behind-nginx-reverse-proxy/77889>

## Kubernetes

document locked

Sources:

- <https://forum.seafile.com/t/collabora-document-locked-read-only/14305>
- <https://manual.seafile.com/deploy/libreoffice_online/#config-seafile>