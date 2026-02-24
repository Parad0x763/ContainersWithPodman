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
