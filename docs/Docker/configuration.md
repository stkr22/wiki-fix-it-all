# Docker Configuration

## General

### What is the difference between RUN/EXEC or CMD/CMD-SHELL?

RUN always works even if an image does not have a shell installed or not default shell defined. EXEC on the other hand is dependent on a default shell but has the advantage of offering all features of that shell like PATH or variables etc. CMD/CMD-SHELL are similar. One particular problem for executions outside healthechecks can be that SHELL commands have PID 1, which gets problematic if the container should be shut down (but I did not get why).

Sources:

- <https://devops.stackexchange.com/questions/11501/healthcheck-cmd-vs-cmd-shell>

### What are Capabilities?

Capabilities give system priviliges to containers like controlling iptables with NET_ADMIN. By default containers are unpriviliged and do not have any rights on the host system.

Sources:

- <https://stackoverflow.com/questions/58377469/difference-between-cap-add-net-admin-and-add-capabilities-in-yml>

### Difference between build context and Dockerfile location

``` bash
docker build -f {{ PATH_TO_DOCKERFILE }} . --tag="{{ YOUR_REGISTRY_URL }}{{ YOUR_REPOSITORY_PATH }}:{{ YOUR_TAG }}"
```

### How to clean up?

Several options are available. First you want to clean up all container related resources like dangling images(no tags), dangling volumes(not associated with a container), dangling networks etc. Then do:

``` bash
docker system prune --volumes
```

Besides that you also might loose a lot of space due to tagged images that you do not use respectively are currently not used by any container. That can be done by:

``` bash
docker image prune --all
```

Sources:

- <https://yallalabs.com/devops/how-to-remove-all-unused-dangling-docker-images/>

## Using Github ghcr.io registry

First generate a personal access token(PAT) via settings > developer settings > personal access token or use this [link](https://github.com/settings/tokens/new?scopes=read:packages)

``` bash
docker login ghcr.io -u USERNAME
```

Sources:

- <https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry>
- <https://docs.github.com/en/packages/managing-github-packages-using-github-actions-workflows/publishing-and-installing-a-package-with-github-actions#upgrading-a-workflow-that-accesses-ghcrio>
- <https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository#creating-a-release>
- <https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry#authenticating-to-the-container-registry>
- <https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token>

## How do I connect my container to the host network?

By default docker isolates the container from the host and thus the container cannot access any services on the host. On e way to solve this is by binding the service to the docker adress ( which I did not figured out yet). Alternatively one can set the network to host

``` bash
docker run --network=host alpine
```

Sources:

- <https://www.baeldung.com/linux/docker-connecting-containers-to-host>

## How to set up a systemd service for docker?

Not that easy especially because systemd does observe the docker client and not the actual container. Type should be "simple", because it waits e.g. with notify for an exit, which does not come, if the container is not started detached. The small "-" at the beginning ensures that this part continues even with errors. The easiest for now seems to keep it simple:

```conf
[Unit]
Description={{ CONTAINER_DESCRIPTION }}
After=docker.service
Requires=docker.service


[Service]
Type=simple
User=ubuntu
Group=docker
Restart=always
ExecStartPre=-docker container stop {{ CONTAINER_NAME }}
ExecStartPre=-docker container rm {{ CONTAINER_NAME }}
ExecStartPre=-docker pull {{ CONTAINER_REPOSITORY }}
ExecStart=docker container run {{ CONTAINER_REPOSITORY }}
ExecStop=docker container stop -t 4 {{ CONTAINER_NAME }}
ExecRestart=docker container restart {{ CONTAINER_NAME }}

[Install]
WantedBy=multi-user.target
```

Sources:

- <https://blog.container-solutions.com/running-docker-containers-with-systemd>
- <https://dev.to/suntong/autostart-docker-container-with-systemd-5aod>

## Docker Compose

### Docker and docker compose pull most recent images how?

``` bash
docker-compose pull
docker container pull
```

for docker dompose there is not --pull flag for

``` bash
docker-compose up
```

thus the easiest is to:

``` bash
docker-compose pull && docker-compose up --build --detach
```

### Network

#### Network security with docker

By default docker is creating iptable rules for all exposed ports that do not reside on internal network. These iptable rules are always evaluated before ufw or firewalld rules. This effectively renders the firewall useless for docker purposes and one should never expose docker ports to all interfaces like

``` yaml
ports:
  - 53:53
```

that are not meant to be publicly available. Instead use the loopback interface for bind like so

``` yaml
ports:
  - 127.0.0.1:53:53
```

Sources:

- <https://degreesofzero.com/article/docker-and-firewalls.html>
- <https://stackoverflow.com/questions/66592057/how-to-correct-configuration-for-firewalld-and-docker-nginx>

#### How do I reach another Docker container on the same network from one container?

The Docker container must be in the same internal or external network as the Docker container we are in. Then it can be referenced by its "tag". e.g.

``` yaml
app:
  build: ./
  container_name: {{ CONTAINER_NAME }}
```

would be the "tag" app.

#### How to set up an internal dns alias?

by referencing it as an alias of the network.

``` yaml
services:
  {{ CONTAINER_NAME }}:
    networks:
      default:
        aliases: 
          - {{ YOUR_ALIAS_URL }}
```

Sources:

- <https://stackoverflow.com/questions/47577490/resolve-container-name-in-extra-hosts-option-in-docker-compose>

### How to set the build directive correctly if the build directory is outside the compose directory?

``` yaml
build:
  context: ../
  dockerfile: {{ PATH_TO_DOCKERCOMPOSE_FILE }}
```

### How do I use docker secrets in docker compose?

In general secrets are similar to reference a file (add "_FILE" to our env name) instead of value when using environmental variables:

``` yaml
services:
 postgres_db:
  image: postgres
  secrets:
    - db_user
    - db_password
  environment:
    - POSTGRES_USER_FILE=/run/secrets/db_user
    - POSTGRES_PASSWORD_FILE=/run/secrets/db_password
```

All secrets need to referenced in the docker compose file at the end. For non production environments we can use the secret directive and refer to files:

``` yaml
 secrets:
  psql_user:
    file: ./psql_user.txt
  psql_password:
    file: ./psql_password.txt
```

For production we should correctly initialize these secrets:

``` bash
printf "{{ MY_DB_USER }}" | docker secret create {{ SECRET_NAME }} -
```

and then will refer to external secrets:

``` yaml
 secrets:
  db_user:
    external: true
  db_password:
    external: true
```

Please note: Secrets will be available in containers as literal files. Thus if an app simply "loads" a secret based environmental variable it will get the path back and not the actual secret. Most images now automatically switch to check the contents of the referenced file if they find "_FILE" at the end of the name. But it is not officially supported by Docker. Alternativel one can use an entrypoint script(see link 2).

Secrets are a docker swarm only feature!!!

Sources:

- <https://www.rockyourcode.com/using-docker-secrets-with-docker-compose/>
- <https://medium.com/@adrian.gheorghe.dev/using-docker-secrets-in-your-environment-variables-7a0609659aab>

