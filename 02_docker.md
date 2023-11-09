# <span style="color:darkblue">Docker survival kit<span>

-------
## <span style="color:darkblue">Docker container<span>
-------
= Sandboxed process running on a host machine that is isolated from all other processes running on that host machine

-------
## <span style="color:darkblue">Docker image<span>
-------
= A running container uses an isolated filesystem. This isolated filesystem is provided by an image, and the image must contain everything needed to run an application:
- dependencies, 
- configurations, 
- scripts, 
- binaries, 
- etc. 

The image also contains other configurations for the container, such as environment variables, a default command to run, and other metadata.
environment variables,
a default command to run,
other metadata

-------
## <span style="color:darkblue">Container properties<span>
-------
A container 
- Is a runnable instance of an image. You can create, start, stop, move, or delete a container using the Docker API or CLI.
- Can be run on local machines, virtual machines, or deployed to the cloud.
- Is portable (and can be run on any OS).
- Is isolated from other containers and runs its own software, binaries, configurations, etc.


-------
## <span style="color:darkblue">Docker Hub<span>
-------
= the world's largest library and community for container images ( https://hub.docker.com/ ).

```{image} ./figures/docker_hub.png
:alt: docker_hub
:width: 500px
:align: center
```

You can directly get official docker images or computed by the community.






-------
## <span style="color:darkblue">Hello world and docker basic commands<span>
-------



### <span style="color:darkblue">Docker pull<span>

This command is used to download a Docker image from a registry, such as Docker Hub, to your local system.

```bash
docker pull hello-world
```


```bash
docker pull postgis/postgis:16-3.4
```
### <span style="color:darkblue">Docker ps<span>
This command shows the __list of currently running containers__ along with their details, such as container ID, names, and status.
```bash
docker ps
```

### <span style="color:darkblue">Docker run<span>

This command__creates and runs a new Docker container__ based on a specified image. It can also be used to specify various options and settings for the container.
```bash
docker run hello-world
```

If you check the list of active containers, you will discover that docker assign a random (fancy) name to your container. To avoid this and easily keep track of your docker containers, you can add a ```--name``` parameter.

Here is another docker run example related to our workshop: a postgis container with a custom setup

```bash
docker run \
    --name postgis_sotm \
    -p 5452:5432 \
    -e POSTGRES_PASSWORD=postgres \
    -d postgis/postgis:16-3.4
```

You can see that we specify a particular name (postgis_osm), handle the port forwarding, set environement variables (postgres database user password), version of Postgresql (16) and finally the version of Postgis (3.4).



### <span style="color:darkblue">Docker start<span>
This command is used to __start one or more stopped containers__, allowing them to run in the background.


### <span style="color:darkblue">Docker stop<span>
This command is used to __stop one or more running containers__, pausing their execution.


### <span style="color:darkblue">Docker rm<span>
This command removes one or more stopped containers from your system. Containers must be stopped before they can be removed.

### <span style="color:darkblue">Docker exec<span>
This command allows you to run a command inside a running container. It provides a way to execute commands in an already running container without the need to start a new shell session.

-------
## <span style="color:darkblue">Docker File<span>
-------
= text-based file with no file extention that contains a script of instructions.
Docker uses this script to build a container image.

Example of docker file: imposm image from geopostcodes

:::{toggle} add_municipality function
```Dockerfile
# docker build --platform linux/amd64,linux/arm64 -t geopostcodes/imposm:1 --push .
FROM debian:latest
 
# don't use root as the running user
RUN groupadd -g 999 imposm && \
    useradd -r -u 999 -g imposm imposm
 
# Install base utilities
RUN apt-get update && \
    apt-get install -y wget && \
    apt-get install -y tar gzip && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
 
# install imposm
USER 999
WORKDIR /usr/app
RUN wget --quiet https://github.com/omniscale/imposm3/releases/download/v0.11.1/imposm-0.11.1-linux-x86-64.tar.gz && \
    tar -xf imposm-0.11.1-linux-x86-64.tar.gz && \
    mv ./imposm-0.11.1-linux-x86-64/* . && \
    rm -rf ./imposm-0.11.1-linux-x86-64 && \
    rm imposm-0.11.1-linux-x86-64.tar.gz

```
:::

You can see that a Dockerfile is composed of adapted linux commands such as ```groupadd```, ```wget```, ```apt-get```, etc. to prepare the image based on a linux dockerized OS such as debian, alpine or Ubuntu for example.

To build an image from a Dockerfile and with a tag, you can run a command similar to the following in your Dockerfile folder location:

```bash
docker build -t my-image-tag .
```

-------
## <span style="color:darkblue">Docker Volumes<span>
-------


-------
## <span style="color:darkblue">Docker Compose<span>
-------
A Docker Compose file is a configuration file used to define and manage multi-container Docker applications. It allows you to specify the services, networks, and volumes required for your application in a declarative manner. In a Docker Compose file, you can define multiple services, each representing a containerized application component. These services can be configured with various options, including Docker images, environment variables, ports, volumes, and dependencies. By using a Docker Compose file, you can define the entire application stack, its relationships, and configurations in one centralized place. When you run docker-compose up, Docker Compose reads the configuration from the file and starts the specified services, creating the necessary containers, networks, and volumes based on the defined configurations. This simplifies the process of managing complex applications by enabling you to launch and scale multi-container setups with a single command, making it easier to develop, test, and deploy applications in different environments.