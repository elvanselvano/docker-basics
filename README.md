![](https://thingsolver.com/wp-content/uploads/docker-cover.png)
---
### Tools

- Shell: [iTerm2 + oh-my-zsh + much more](https://medium.com/seokjunhong/customize-the-terminal-zsh-iterm2-powerlevel10k-complete-guide-for-beginners-35c4ba439055)

- Docker: [Docker for Mac](https://hub.docker.com/editions/community/docker-ce-desktop-mac)

- Code Editor: [Visual Studio Code](https://code.visualstudio.com/download)

### Containers

![](https://www.docker.com/sites/default/files/d8/2018-11/docker-containerized-appliction-blue-border_2.png)

Containers are just processes running in the host OS which are limited to what resources they can access and exit when process stops.

`docker container run --publish 80:80 --detach webhost nginx`

1. Looks for the image `nginx` locally in image cache
    - If it doesn't find anything, looks in the remote image repository (defaults to Docker Hub) and download the image (latest by default)
2. Creates a new container based on the image
3. Gives it a virtual IP on a private network inside Docker Engine
4. Opens up port 80 on host (bind error if port is being used) and forwards to port 80 in container IP
5. Starts container by using the command in the Dockerfile

Note that:
- `Detach` will run the container in background
- `webhost` will be the name of the container

There are tons of commands in Docker (cheatsheet available [here](https://www.docker.com/resources/datasheets/docker-cheat-sheet)), such as:

```
docker version
docker info
docker <command> <sub-command> (options)
docker container ls -a
docker container rm (container ID), use -f for force
docker container stop (container ID, first few digits)
docker container logs webhost
docker container top webhost
...
```

### Shell Inside Container

The following commands will start a new container interactively based on alpine image. We can also do `docker container exec -it` to run additional command in existing container.

```
docker pull alpine
docker image ls
docker container run -it alpine sh
```

```
docker container run --rm -it ubuntu:14.04 bash
apt-get update && apt-get install -y curl
curl --version
```

### Docker Networks 

![](https://k21academy.com/wp-content/uploads/2020/06/bmExZyvGWidultcwx9hCb7nTzqrqzN7Y9aBZTaXoQ8Q-1024x955.png)

"Batteries included, but removeable" meaning defaults work well but it is easy to swap out parts to cuztomize it.

- Each container is connected to a private virtual network "bridge"
- Each virtual network routes through NAT firewall on host IP
- All containers on a VN can talk to each other without -p to expose the ports
- Best practice is to create a new virtual network for each app:
    - network `my_web_app` for mysql and php/apache containers
    - network `my_api` for mongo and nodejs containers

The following command shows that the IP is different in mac and nginx.

```
docker container run -p 80:80 --name webhost -d nginx
docker container port webhost
docker container inspect --format '{{ .NetworkSettings.IPAddress }}'
webhost
ipconfig getifaddr en1
```

Other commands includes:

```
docker network ls
docker network inspect
docker network create --driver
docket network connect
docker network disconnect
...
```

```
docker network create my_net
docker container run -d --net my_net --net-alias search elasticsearch:2
docker container run -d --net my_net --net-alias search elasticsearch:2
docker container ls
docker container run --rm --net my_net alpine nslookup search
docker container run --rm --net my_net centos curl -s search:9200
```

Read more [here](https://docs.docker.com/network/network-tutorial-standalone/).

### Docker Image

![](https://docs.microsoft.com/en-us/dotnet/architecture/microservices/container-docker-introduction/media/docker-containers-images-registries/taxonomy-of-docker-terms-and-concepts.png)

- Image: app binaries, dependencies, metadata about the image and how to run it
- Container: a single read / write layer on top of image
- Host provides the kernel, not the image
- We can download images from [Docker Hub](https://hub.docker.com/), the "apt package system" for containers

![](https://static.packt-cdn.com/products/9781788992329/graphics/assets/5c8fd414-799b-43e3-9623-0dcbdabfe7ff.png)

Every image starts from a blank layer and every set of changes that happen on the file system is a uniquely identified layer and only stored once on a host. This saves storage space on host and transfer time on push / pull. 

```
docker image history nginx:latest
docker image inspect nginx:latest
docker image tag nginx elvanselvano/nginx
cat .docker/config.json
docker image push elvanselvano/nginx
```

### Dockerfile
Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image.

```
docker image build -t custom_nginx . --platform linux/amd64
docker container run -p 80:80 --rm nginx
```

### Container Lifetime and Persistent Data
- Containers are usually immutable and temporary.
- "immutable infrastructure": only re-deploy containers, never modify
- Docker ensures "separation of concerns" for databases using:
    - Volumes: makes special location outside of container UFS (union file system)
    - Bind Mounts: mount container path to host path