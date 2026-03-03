# Podman

We are typically working in a console environment where we have a remote connection to a linux server or container. So, most of our podman work is with command line. Big thing is to understand the concepts behind containerization and how podman can be used to help with developing and deploying applications. For example, we typically have premade images for our development environment that you’ll need to pull from the repository, start up and interact within. Hopefully, that helps frame the context to which we use podman as you’re diving in to learn about it

- [Podman](https://podman.io/)
- [Suse-Container-Guide](https://documentation.suse.com/container/all/single-html/Container-guide/Container-guide.html)
- [doc.podman.io/Tutorials](https://docs.podman.io/en/latest/Tutorials.html)
- [podman_tutorial](https://github.com/containers/podman/blob/main/docs/tutorials/podman_tutorial.md)

## Key Concepts

- Basic **Podman** workflow: **Registry** -> **Base image** -> **Dockerfile** -> **Custom image** -> **Container**
- To run a container you need an **image**
- **Podman** can automatically fetch a base image when building a custom image
- To build a custom image, first create a file called `Containerfile` or `Dockerfile`containing building instructions

## Running a Sample Test Container

- A sample container that is build with Nginx that only serves an index page
- `podman run --name basic_httpd -d -p 8080:80/tcp docker.io/nginx`
- Use **Podman** `ps` command to list creating and running containers

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
