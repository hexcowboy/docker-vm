# Docker VM
Docker has the capabilities to create virtual machines, however the majority of it's use case right now is to host ephemeral containers, meaning they live short and aren't persistent. To make a Docker container behave more like a VM, it needs to have the following qualities:

1. **Persistence.** When you shut down and restart the VM, it should remember your file system structure.
2. **User interface.** For a lot of users, a default bash shell is not enough and a TUI or GUI is needed.

## Persistence
The solution to the first problem is to utilize a `docker-compose.yml` volumes to map the root filesystem of the container to a directory on the host machine. The following is an example of how a volume is configured:

```yaml
version: 3.8

services:
  virtualmachine:
    image: ubuntu:latest
    volumes:
      - "rootfs:/"

volumes:
  rootfs:
```

This creates a mountpoint that maps from the root of the container to a point on the filesystem, usually `/var/lib/docker/volumes`. This mount point can be found by running the following commands:

```bash
$ docker volume ls
local     virtualmachine_rootfs

$ docker volume inspect virtualmachine_rootfs | grep Mountpoint
    "Mountpoint": "/var/lib/docker/volumes/virtualmachine_rootfs/_data",
```

## User Interface
The `Dockerfile` can be used to automate the setup of a new container. Many projects exist already that create an X environment with an accompanying browser-based graphics server. This allows the GUI to be accessed by browsing to the graphics server from the host machine in a web browser. A simpler way to run graphics would be to use xterm. Here's an easy way to set up a graphics server in a `Dockerfile`:

```Dockerfile
FROM ubuntu:latest

RUN apt update && apt install xterm
```

An environment variable also needs to be set, so the `docker-compose` file gets updated to:

```yaml
version: 3.8

services:
  virtualmachine:
    image: ubuntu:latest
    environment:
      - "DISPLAY=${DISPLAY}"
    volumes:
      - "rootfs:/"

volumes:
  rootfs:
```
