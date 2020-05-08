

[![Docker Pulls](https://img.shields.io/docker/pulls/xbgmsharp/docker-geneweb.svg)](https://hub.docker.com/r/xbgmsharp/docker-geneweb/)[![Build Status](https://travis-ci.com/xbgmsharp/docker-geneweb.svg?branch=master)](https://travis-ci.com/xbgmsharp/docker-geneweb)

# Docker Geneweb

This multi architecture Alpine Linux based Docker image allows you to run [GeneWeb](https://github.com/geneweb/geneweb).

GeneWeb is an open source genealogy software written in OCaml. It comes with a Web interface and can be used off-line or as a Web service.

# Supported tags:

- `latest` - Latest Geneweb server ([Dockerfile](https://github.com/xbgmsharp/docker-geneweb/blob/master/Dockerfile))

> The docker images are updated daily by a cron job from ([Travis CI](https://travis-ci.com/xbgmsharp/docker-geneweb)).

> This docker image is based on the popular Alpine Linux project, available in the alpine official image. Alpine Linux is much smaller than most distribution base images (~5MB), and thus leads to much slimmer images in general (less than 400MB).

# Supported architectures: ([more info](https://github.com/docker-library/official-images#architectures-other-than-amd64))
`amd64`, `arm32v6`, `arm32v7`, `arm64v8`, `i386`

| **Docker** | **uname -m architecture** | **Annotate flage** | **Boards** |
| --- | --- | --- | --- |
| amd64 | x86_64 | (none) |
| arm32v6 | armhf | Raspberry Pis | --os linux --arch arm |
| arm64v8 | aarch64 | A53, H3, H5 ARMs |--os linux --arch arm64 --variant armv8 |

If you compiled for other architectures and know other combinations, let me know!

> The docker image tag `latest` is available for all supported architectures.

# Run GeneWeb
After a successful [Docker installation](https://docs.docker.com/engine/installation/) you just need to execute the following command in the shell:

```bash
docker pull xbgmsharp/docker-geneweb
docker run  -d \
  --publish 2316:2316 \
  --publish 2317:2317 \
  --publish 2322:2322 \
  --restart always \
  --volume $(pwd)/geneweb/etc:/usr/local/share/geneweb/etc \
  --volume $(pwd)/geneweb/data:/usr/local/share/geneweb/share/data \
  --volume $(pwd)/geneweb/log:/usr/local/share/geneweb/log \
  --name geneweb \
  xbgmsharp/docker-geneweb:latest
```

# Parameters
Container images are configured using parameters passed at runtime (such as those above). These parameters are separated by a colon and indicate `<external>:<internal>` respectively. For example, `-p 8080:80` would expose port `80` from inside the container to be accessible from the host's IP on port `8080` outside the container.

| Parameter | Function |
| :----: | --- |
| `-p 2316` | require for Geneweb setup portal |
| `-p 2317` | require for Geneweb portal |
| `-p 2322` | require for Geneweb API |
| `-e PUID=1000` | for UserID (optional) |
| `-e PGID=1000` | for GroupID (optional) |
| `-e TZ=Europe/Paris` | TimeZone (optional) |
| `-v /usr/local/share/geneweb/etc` | Mount this folder to insert your own config into the docker container. |
| `-v /usr/local/share/geneweb/share/data` | Mount this directory to store all geneweb databases. |
| `-v /usr/local/share/geneweb/log` | Mount this directory to store all log files into this folder.|

# Config

```bash
# prepare shared folders
mkdir -p geneweb/{etc,log,data,backup,import}
echo "172.17.0.1" > geneweb/etc/gwsetup_only
```

For gwsetup you must edit the `geneweb/etc/gwsetup_only` file and replace the IP with the local IP or Hostname where the docker container runs within.

-   gwsetup (Geneweb setup portal) will be available on localhost:2316
-   gwd (Geneweb portal) will be available on localhost:2317

There are 3 folders which are currently exposed:

-   log -> all log files are written into this folder
-   config -> all neccessary config files. Note you can overwrite the default `redis.conf`
-   data -> all geneweb databases. You can create an authority file for gwd where each line is e.g `user:password`. The file **must** be called `gwd_passwd` because the `geneweb-launch.sh` starts gwd with the correct runtime argument when the file is found.

# Updating Info

The image is static, versioned, and require an image update and container recreation to update the app inside. 

Below are the instructions for updating containers:

## Via Docker Run/Create
* Update the image: `docker pull xbgmsharp/docker-geneweb`
* Stop the running container: `docker stop geneweb``
* Delete the container: `docker rm geneweb``
* Recreate a new container with the same docker create parameters as instructed above (if mapped correctly to a host folder, your `log` and `config` and `data` folder with settings will be preserved)
* Start the new container: `docker start geneweb`
* You can also remove the old dangling images: `docker image prune`

```bash
docker pull xbgmsharp/docker-geneweb
docker stop geneweb
docker rm geneweb
docker create \
  -e PUID=1000 \
  -e PGID=100 \
  -e TZ=Europe/Paris \
  --restart unless-stopped \
  --publish 2316:2316 \
  --publish 2317:2317 \
  --publish 2322:2322 \
  --volume ~/geneweb/etc:/usr/local/share/geneweb/etc \
  --volume ~/geneweb/data:/usr/local/share/geneweb/share/data \
  --volume ~/geneweb/log:/usr/local/share/geneweb/log \
  --name geneweb xbgmsharp/docker-geneweb:latest
docker start geneweb
```

References
----------
Adapted from: https://gitlab.rimkus.it/genealogy/geneweb-arm64-docker/-/tree/release/master

# Contribution
I'm happy to accept Pull Requests! Please note that this project is released with a [Contributor Code of Conduct](https://github.com/xbgmsharp/docker-geneweb/blob/master/CODE_OF_CONDUCT.md). By participating in this project you agree to abide by its terms.
