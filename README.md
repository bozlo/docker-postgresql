# PostreSQL Docker for OMV5

## Supported Architectures

Support only x86-64
Simply pulling `bozlo/PostreSQL` should retrieve the correct image for your arch, but you can also pull specific arch images via tags.

The architectures supported by this image are:

| Architecture | Tag |
| :----: | --- |
| x86-64 | amd64-latest |


## Usage

Here are some example snippets to help you get started creating a container.

### docker

```
docker create \
  --name=postgresql \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Europe/London \
  -e POSTGRES_DB=USER_DB_NAME `#optional` \
  -e POSTGRES_USER=POSTGRES_USER `#optional` \
  -e POSTGRES_PASSWORD=DATABASE_PASSWORD `#optional` \
  -p 5432:5432 \
  -v path_to_data:/config \
  --restart unless-stopped \
  bozlo/postgresql
```


### docker-compose

Compatible with docker-compose v2 schemas.

```
---
version: "2.1"
services:
  postgresql:
    image: bozlo/postgresql
    container_name: postgresql
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/London
      - POSTGRES_DB=USER_DB_NAME #optional
      - POSTGRES_USER=POSTGRES_USER #optional
      - POSTGRES_PASSWORD=DATABASE_PASSWORD #optional
    volumes:
      - path_to_data:/config
    ports:
      - 5432:5432
    restart: unless-stopped
```

## Parameters

Container images are configured using parameters passed at runtime (such as those above). These parameters are separated by a colon and indicate `<external>:<internal>` respectively. For example, `-p 8080:80` would expose port `80` from inside the container to be accessible from the host's IP on port `8080` outside the container.

| Parameter | Function |
| :----: | --- |
| `-p 5432` | PostgreSQL listens on this port. |
| `-e PUID=1000` | for UserID - see below for explanation |
| `-e PGID=1000` | for GroupID - see below for explanation |
| `-e TZ=Europe/London` | Specify a timezone to use EG Europe/London. |
| `-e POSTGRES_DB=USER_DB_NAME` | Specify the name of a database to be created on image startup. |
| `-e POSTGRES_USER=POSTGRES_USER` | This user will have superuser access to the database specified by POSTGRES_DB (do not use root here). |
| `-e POSTGRES_PASSWORD=DATABASE_PASSWORD` | Set this to the password you want to use for you POSTGRES_USER (minimum 4 characters). |
| `-v /config` | Contains the db itself and all assorted settings. |

## Environment variables from files (Docker secrets)

You can set any environment variable from a file by using a special prepend `FILE__`.

As an example:

```
-e FILE__PASSWORD=/run/secrets/mysecretpassword
```

Will set the environment variable `PASSWORD` based on the contents of the `/run/secrets/mysecretpassword` file.

## Umask for running applications

For all of our images we provide the ability to override the default umask settings for services started within the containers using the optional `-e UMASK=022` setting.
Keep in mind umask is not chmod it subtracts from permissions based on it's value it does not add. Please read up [here](https://en.wikipedia.org/wiki/Umask) before asking for support.

## User / Group Identifiers

When using volumes (`-v` flags) permissions issues can arise between the host OS and the container, we avoid this issue by allowing you to specify the user `PUID` and group `PGID`.

Ensure any volume directories on the host are owned by the same user you specify and any permissions issues will vanish like magic.

In this instance `PUID=1000` and `PGID=1000`, to find yours use `id user` as below:

```
  $ id username
    uid=1000(dockeruser) gid=1000(dockergroup) groups=1000(dockergroup)
```


&nbsp;
## Application Setup


### Loading passwords and users from files


## Support Info

* Shell access whilst the container is running: `docker exec -it postgresql /bin/bash`
* To monitor the logs of the container in realtime: `docker logs -f postgresql`
* container version number
  * `docker inspect -f '{{ index .Config.Labels "build_version" }}' postgresql`
* image version number
  * `docker inspect -f '{{ index .Config.Labels "build_version" }}' bozlo/postgresql`


### Via Docker Run/Create
* Update the image: `docker pull bozlo/postgresql`
* Stop the running container: `docker stop postgresql`
* Delete the container: `docker rm postgresql`
* Recreate a new container with the same docker create parameters as instructed above (if mapped correctly to a host folder, your `/config` folder and settings will be preserved)
* Start the new container: `docker start postgresql`
* You can also remove the old dangling images: `docker image prune`

### Via Docker Compose
* Update all images: `docker-compose pull`
  * or update a single image: `docker-compose pull postgresql`
* Let compose update all containers as necessary: `docker-compose up -d`
  * or update a single container: `docker-compose up -d postgresql`
* You can also remove the old dangling images: `docker image prune`

## Building locally

If you want to make local modifications to these images for development purposes or just to customize the logic:
```
git clone https://github.com/bozlo/docker-postgresql.git
cd docker-postgresql
docker build \
  --no-cache \
  --pull \
  -t bozlo/postgresql:latest .
```

The ARM variants can be built on x86_64 hardware using `multiarch/qemu-user-static`
```
docker run --rm --privileged multiarch/qemu-user-static:register --reset
```

Once registered you can define the dockerfile to use with `-f Dockerfile.aarch64`.

## Versions

* 09.12.20:** - Initial Release.
