# Piwigo

## Install

``` yaml
 version: '3'

services:
  piwigo:
    image: ghcr.io/linuxserver/piwigo
    container_name: piwigo
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Berlin
    restart: unless-stopped
    depends_on: 
      - db
    network_mode: service:db
  db:
    image: mariadb:latest
    restart: unless-stopped
    ports:
      - 80:80
      - 3307:3306
    environment:
      - MYSQL_ROOT_PASSWORD={{ MYSQL_ROOT_PASSWORD }}
      - MYSQL_USER=piwigouser
      - MYSQL_PASSWORD={{ MYSQL_PASSWORD }}
      - MYSQL_DATABASE=piwigo
```

Sources:

- <https://lycheeorg.github.io/demo/>
- <https://lycheeorg.github.io/>
- <https://www.linuxlinks.com/Lychee/>
- <https://github.com/Piwigo/Piwigo>
- <https://hub.docker.com/r/linuxserver/piwigo/>