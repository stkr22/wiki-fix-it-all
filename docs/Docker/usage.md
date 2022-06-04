# Docker Usage

## Which modes are supported in docker?

detached

``` bash
docker container run -d / docker-compose up --detached
```

attached

``` bash
docker container run / docker-compose up
```

interactive

Sources:

- <https://linuxbuz.com/docker-tutorial/docker-run-command-with-examples>

## How do I enter commands directly in a docker?

``` bash
docker container exec -it {{ CONTAINER_NAME }} /bin/bash
```

## How do I inspect a part of my container?

``` bash
docker inspect --format='{{json .State.Health}}' {{ CONTAINER_NAME }}
```

## How do I view the log of an existing container?

Simply use the logs command:

``` bash
docker-compose -f {{ PATH_TO_DOCKERCOMPOSE_FILE }} logs -f
```

Sources:

- <https://stackoverflow.com/questions/37195222/how-to-view-log-output-using-docker-compose-run>

## How to copy files into a container?

simplest way to copy a folder "data" into container_name:

``` bash
docker container cp data {{ CONTAINER_NAME }}:/
```

alternatively if just the contents of a folder should be copied:

``` bash
docker container cp ./data/. {{ CONTAINER_NAME }}:/data/
```

the "." at the end specifies that only the contents should be copied.

Sources:

- <https://stackoverflow.com/questions/32566624/docker-cp-all-files-from-a-folder-to-existing-container-folder>

## How to backup containers safely?

One way is snapshots with

``` bash
docker container commit
```

that will snapshot the complete state of the contaienr and is thus fairly big depending on the container. If one just wants to save the data inside the container, there are different ways.

One way would be to either grant docker user group to a technical user or alternatively to let root do the backup. For an automatic external solution an openssh server which has access to the volumes of containers that should be backuped is better. In addition it allows to only give access to that container and the access to volumes is read only.

It is important to note that in order to mount existing volumes from other containers the external attribute must be set! Otherwise docker compose will simply create a new volumes with the container name in front of it.

``` yaml
data:
  external:
    name: {{ VOLUME_NAME }}
```

Problem how to make the backup with borgbackup?
Mount the container with sshfs
Then pull the backup with borgbackup over the local mountpoint

Sources:

- <https://github.com/linuxserver/docker-openssh-server>
- <https://jpetazzo.github.io/2014/06/23/docker-ssh-considered-evil/>
- <https://docs.docker.com/compose/compose-file/compose-file-v3/#volume-configuration-reference>
- <https://www.digitalocean.com/community/tutorials/how-to-use-sshfs-to-mount-remote-file-systems-over-ssh>
- <https://borgbackup.readthedocs.io/en/stable/quickstart.html#remote-repositories>

## How do I make application available?

use nginx-proxy

Sources:

- <https://github.com/nginx-proxy/docker-letsencrypt-nginx-proxy-companion/wiki/Advanced-usage>
