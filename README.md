# Podman

- [Podman](https://podman.io/)
- [Suse-Container-Guide](https://documentation.suse.com/container/all/single-html/Container-guide/Container-guide.html)
- [doc.podman.io/Tutorials](https://docs.podman.io/en/latest/Tutorials.html)
- [podman_tutorial](https://github.com/containers/podman/blob/main/docs/tutorials/podman_tutorial.md)

## Key Concepts

- Basic **Podman** workflow: **Registry** -> **Base image** -> **Dockerfile** -> **Custom image** -> **Container**
- To run a container you need an **image**
- **Podman** can automatically fetch a base image when building a custom image
- To build a custom image, first create a file called `Containerfile` or `Dockerfile`containing building instructions
- Works with **pods** that collections of containers
- Default container registries: [quay.io](https://quay.io/?ref=devopscube.com), [docker.io](https://hub.docker.com/?ref=devopscube.com)

### Logging into private container images

- `podman login docker.io # log into docker hub`

### Pulling Images

- By default **podman** searches for images in `quay.io` first then in `docker.io`, so it is better to be explicit
- `podman pull docker.io/nginx`
- `podman pull quay.io/quay/busybox`

## Running a Sample Test Container

- A sample container that is build with Nginx that only serves an index page
- `podman run --name basic_httpd -d -p 8080:80/tcp docker.io/nginx`
- `sudo podman port -l # shows the port being used, for this command you see: 80/tcp -> 0.0.0.0:8080`
- `sudo podman inspect -l # inspects the container`
- Use **Podman** `ps` command to list creating and running containers

## Create a Pod

- `podman pod create --name <pod_name>`
- `podman pod ls # lists pods`
- `podman ps -a --pod # lists all containers inside a pod`

### Add Containers to a Pod

- `podman run -dt --pod <pod_nam> <container>`

### Start, Stop and Remove Containers inside a Pod

- `podman start <container_id>`
- `podman stop <container_id>`
- `podman rm <container_id>`

### Start, Stop and Delete a Pod

- `podman pod stop <podname>`
- `podman pod start <podname>`
- To remove a pod, first stop all containers inside the pod
  - `podman pod rm <podname>`
- Or to forcefully remove without stopping
  - `podman pod rm -f <podname>`

### Inspecting a running container

- `podman ps # adding -a will have Podman show all containers`
- `podman inspect basic_httpd | grep IPAddress\":`
  - `"IPAddress": "",`

### Viewing the container's logs

- `podman logs <container_id>`

### Viewing the container's pids

- `podman top <container_id>`

### Checkpointing the container

- Checkpointing a container stops the container while writing the state of all processes in the container to disk, this can not be used in rootless
- `sudo podman container checkpoint <container_id>`

### Restoring the container

- This is only possible from a previously checkpointed container. It will continue from the exact point it was checkpointed
- `sudo podman container restore <container_id>`

### Stopping the container

- `podman stop <container_id>`

### Removing the container

- `podman rm <container_id>`

## Podman with a Custom Linux VM or external Linux System

- The core **Podman** runtime environment can only run on Linux operating systems
- The remote client uses a client-server model
- A **SSH Daemon** needs to be running because executing a **Podman** command connects to a server via **SSH**
  - then it connects to the **Podman** service using **systemd** socket activation

### Creating the first connection

- Must enable `podman.sock` SystemD service on Linux server
- `systemctl --user enable --now podman.socket # enable and start socket permanently`
- Enable `linger` for the user in order for the socket to work when the user is not logged in
  - `sudo loginctl enable-linger $USER`
  - `podman --remote info # verify the socket is listening`

#### Enable sshd

- `sudo systemctl enable --new sshd # enable and start SSH daemon`

#### Setting up SSH

- `ssh-keygen # generate SSH keys for client and server communication`
- The public key by default should be in `home` directory under `.ssh\id_rsa.pub`
- `cp id_rsa.pub ~/.ssh/authorized_keys # copy the public key on the Linux server`

### Using the client

- `C:\Users\parad0x763> podman system connection addd parad0x763 --identity C:\Users\parad0x763\.shh\id_rsa ssh://192.168.122.1/run/user # add a connection`
- If this is the first connection added, then it will be set to the default
- `podman system connection list # shows your connections`
- `podman system connection --help`

## Create a simple container

- `sudo podman run hello-world # where hello-world is the container name`
- `sudo podman pull ubuntu:20.04 # pulls an ubuntu version 20.04 image from the default repo`
- `sudo podman ps -a # shows all containers including stopped`
- `sudo podman images # shows all images downloaded`
- `sudo podman run -it --rm ubuntu:20.04 /bin/bash # runs an interactive shell: '-it' and removes it after exit '--rm' using a bash shell '/bin/bash'`
- `sudo podman stop <container_ID> # stops the container`
- `sudo podman pod create --name <pod_name> # creates a pod`
- `sudo podman run -d --pod <pod_name> <container> # '-d' to run in a detached mode so it runs itself, <container> such as nginx will be created and ran inside the pod`
- `sudo podman pull <repository> # docker.io/library/<image>:<version> where <version> can be latest`
- Other repos: `registry.redhat.io/`, `quay.io/`

### Have a container start with system

- `sudo nano /etc/systemd/system/<file_name>.service`
- Example content of the file:
```
[Unit]
Description=<description>

[Service]
ExecStart=/usr/bin/podman run -d --name <container_name> <image>
Restart=always

[Install]
WantedBy=multi-user.target
```
- `sudo systemctl enable <file_name>.service # enable the file to be run as a service`
- `sudo systemctl start <file_name>.service # starts the service`
