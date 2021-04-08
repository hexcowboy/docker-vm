# Docker VM
Docker has the capabilities to create virtual machines, however the majority of it's use case right now is to host ephemeral containers, meaning they live short and aren't persistent. To make a Docker container behave more like a VM, it needs to have the following qualities:

1. **User Interface.** For a lot of users, a default bash shell is not enough and a TUI or GUI is needed.
2. **Persistence.** When you shut down and restart the VM, it should remember your file system structure.

## User Interface
A simple solution to this problem is to install an X server.

### macOS
1. Install the latest version of [XQuartz](https://www.xquartz.org/)
    - Or use brew: `brew install --cask xquartz`
2. Launch XQuartz and in the menu, choose `XQuartz` -> `Preferences` -> `Security`
3. Check the box for `Allow connections from network clients` and restart XQuartz
4. In your macOS terminal, type `xhost + 127.0.0.1`

### Windows
1. Install the latest version of [VcXsrv](https://sourceforge.net/projects/vcxsrv/)
    - Or use choco: `choco install vcxsrv`
2. Launch VcXsrv to start the setup
3. Use all default settings, **checking** `✅ Disable access control`

### Linux
(wip)

This Dockerfile will run `xterm` which is a simple GUI terminal.
```Dockerfile
FROM ubuntu:latest
RUN apt-get update && apt-get install -y xterm
CMD xterm
```

And now we can run our container, setting the `DISPLAY` environment variable to [`host.docker.internal:0`](https://docs.docker.com/docker-for-mac/networking/#use-cases-and-workarounds).
```
$ docker build -t vm .
$ docker run -e DISPLAY=host.docker.internal:0 vm
```

## Persistence
-- NOT YET WORKING ---
