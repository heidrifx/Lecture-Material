---
type: slide
slideOptions:
  transition: slide
  width: 1400
  height: 900
  margin: 0.1
---

<style>
  .reveal strong {
  font-weight: bold;
      color: orange;
  }
  .reveal p {
      text-align: left;
  }
  .reveal section h1 {
      color: orange;
  }
  .reveal section h2 {
      color: orange;
  }
</style>

# Docker

<img src="https://upload.wikimedia.org/wikipedia/en/thumb/f/f4/Docker_logo.svg/2560px-Docker_logo.svg.png" width=40%; style="margin-left:auto; margin-right:auto; padding-top: 25px; padding-bottom: 25px">

<small>Image by https://www.docker.com/, Fair use, https://en.wikipedia.org/w/index.php?curid=70663056</small>

---

## What is Docker? 1/3

- Docker Inc.
- Docker Desktop

    > Developer productivity tools and a local Kubernetes environment.

- Docker Engine

    > an open source containerization technology for building and containerizing your applications.

---

## What is Docker? 2/3

- Docker Hub

    > Cloud-based application registry and development team collaboration services.

- Docker Compose

    > a tool for defining and running multi-container Docker applications.

---

## What is Docker? 3/3

> Docker is an open platform for developing, shipping, and running applications.

---

## Introduction

- 2010: Docker Inc. founded
- 2013: First Docker release
    - Open source, Then based on [LXC](https://linuxcontainers.org/)
- 2014: Replaced LXC by own execution environment
- 2017: [Moby project](https://mobyproject.org/) for open source development
- 2023: The most popular container solution ([survey](https://survey.stackoverflow.co/2023/#section-most-popular-technologies-other-tools))

---

## Typical Docker Applications

- Applications as Microservices
- Containers for consistent development environment
- Containers for consistent testing environment
- Portable format for sharing applications
- Avoid tedious installation procedures by providing Docker container ([FEniCS](https://fenicsproject.org/download/), [OpenFOAM](https://www.openfoam.com/download/openfoam-installation-on-windows-docker), [GitLab](https://docs.gitlab.com/ee/install/docker.html), etc.)

---

## Building Blocks 1/2

- Docker objects
    - **Images**
        - Read-only template for creating a container
        - An image can be based on another image
    - **Containers**
        - Runnable instance of an image

---

## Building Blocks 2/2

- Docker daemon `dockerd`
    - Controlling instance of containers and reacts to API requests
    - Server process
- Docker client
    - User interface/tools to interact with, create, manage containers etc. via daemon
    - That means no direct interaction with containers, images etc.
- Docker registries
    - Registries that manage Docker images to be used

---

## Docker Architecture

<img src="https://docs.docker.com/get-started/images/docker-architecture.webp" width=80%; style="margin-left:auto; margin-right:auto; padding-top: 25px; padding-bottom: 25px">

<small>https://docs.docker.com/get-started/docker-overview/</small>

---

## Connection to Host

- Container communicates via daemon `dockerd` (runs as root)
- Strong isolation (`namespaces` and `cgroups`)
    - You cannot access Host filesystem by default.
    - Several [mount options](https://docs.docker.com/storage) available

---

## Requirements

- Root rights for installation
- `dockerd` runs as root -> Interaction needs root rights
    - Option 1: Prefix commands with `sudo`
    - Option 2: Be member of group `docker` (=makes you root), expected by some applications (e.g. `act`)
- Alternatives:
    - [Rootless mode](https://docs.docker.com/engine/security/rootless/)
    - Run Docker in a VM for better isolation
- Check [security notes](https://docs.docker.com/engine/security/)

---

## Useful Commands 1/4

- `docker run OPTIONS`
    - Run a container
- `docker container ls`
    - List running containers
    - Add `-a` to see also the stopped containers
    - Alias: `docker ps`

---

## Useful Commands 2/4

- `docker pull NAME:TAG`
    - Pulls an image from registry, `TAG` optional
- `docker container create IMAGE`
    - Create container from image
- `docker container start/stop NAME`
    - Start/stop container
- `docker container attach NAME`
    - Attach to running container

---

## Useful Commands 3/4

- `docker build`
    - Creates an image from a given Dockerfile
- `docker cp`
    - Copy files in/out of container
- `docker image history IMAGE`
    - Show layers of image (including commands)

---

## Useful Commands 4/4

- `docker image ls`
    - List locally available images
- `docker system prune`
    - Remove all unused objects (images, containers...)

---

## Demo: Running Prebuilt images

Details available in [`docker_demo.md`](https://github.com/Simulation-Software-Engineering/Lecture-Material/blob/main/02_virtualization_and_containers/docker_demo.md)

---

## Defining and Building own Images 1/2

- Define container in `Dockerfile`
    - Git-friendly text file
- Start from base image
    - Find images on repository such as [DockerHub](https://hub.docker.com/)
- Extend image by additional layers
    - Layers are added separately -> Keep number of layers low
    - Layers are cached
    - Changed layer requires downstream layers to be recreated
- Container layers have commit hashes

---

## Defining and Building own Images 2/2

- `FROM`: Defines base image
- `RUN`: Defines commands to execute
- `WORKDIR`: Defines working directory for following commands
- `COPY`: Copy for from source to destination
- `ADD`: Add for from source to destination (powerful and confusing)
- `CMD`: Command to run under `docker run`
- `ENV`: Sets environment variable
- `ARG`: Environment variable for **only** the build process

---

## Dockerfile Example

```Dockerfile
FROM ubuntu:24.04

RUN apt update -y && apt install -y neofetch
WORKDIR /app
COPY testfile .
CMD ["neofetch"]
```

---

## Demo: Building own Image

Details available in [`docker_demo.md`](https://github.com/Simulation-Software-Engineering/Lecture-Material/blob/main/02_virtualization_and_containers/docker_demo.md)

---

## Publish own Images

- Publication on registry (e.g. [DockerHub](https://hub.docker.com/))
- `docker build -t ACCOUNT/REPOSITORY[:TAG] .`
    - Creates image
- `docker push ACCOUNT/REPOSITORY[:TAG]`
    - Push image to registry (default DockerHub)
    - Needs account and must be logged in via `docker login`

---

## Demo: Run FEniCS Container

Details available in [`docker_demo.md`](https://github.com/Simulation-Software-Engineering/Lecture-Material/blob/main/02_virtualization_and_containers/docker_demo.md)

---

## Advanced Topics

- User ID mapping
- [Multistage builds](https://docs.docker.com/develop/develop-images/multistage-build/)
    - Build image by combining layers created from different base images
- [Different mount typs](https://docs.docker.com/storage)
    - Volumes, bind mount, tmpfs mount
- [Persisting data](https://docs.docker.com/get-started/05_persisting_data/)
- [Multi-container apps](https://docs.docker.com/get-started/07_multi_container/)
- And many more. Check out the [Docker documentation](https://docs.docker.com)

---

## Summary and Outlook

- Lightweight virtualization technique
- Run application in isolated environment
- Run application in consistent environment
- Share environments and applications with containers
- Plenty of options and feature-rich CLI
- Important building block for CI/CD pipelines (future lectures)

---

## Further Reading

- [Docker](https://www.docker.com/)
- [Docker documentation](https://docs.docker.com)
- [DockerHub](https://hub.docker.com/)
- [DockerHub documentation](https://docs.docker.com/docker-hub/)
- [Open Container Initiative (OCI)](https://opencontainers.org/)
- [Malicious Docker Hub Container Images Used for Cryptocurrency Mining](https://www.trendmicro.com/vinfo/fr/security/news/virtualization-and-cloud/malicious-docker-hub-container-images-cryptocurrency-mining)
