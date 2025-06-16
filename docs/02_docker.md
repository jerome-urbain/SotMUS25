# <span style="color:#66C3FF">Docker survival kit<span>


Below you'll find some commands and explanations for your docker survival kit at this workshop.

If you're already a power user and familiar with these concepts, you can continue with the next chapter.


If, on the other hand, you're a novice but still want to delve deeper into the concepts after this workshop, I urge you to take a look at the docker documentation: https://docs.docker.com/


Let's get started!

-------
## <span style="color:#66C3FF">Docker container<span>
-------
A docker container is a __sandboxed process__ running on a host machine that is __isolated from all other processes__ running on that host machine.

-------
## <span style="color:#66C3FF">Docker image<span>
-------
A running container uses an isolated filesystem. This __isolated filesystem__ is provided by an image, and the image must __contain everything needed to run an application__:

- dependencies, 
- configurations, 
- scripts, 
- binaries, 
- etc. 

The image also contains other configurations for the container, such as 

- environment variables, 
- a default command to run, 
- other metadata.

-------
## <span style="color:#66C3FF">Container properties<span>
-------
A container 

- Is a __runnable instance of an image__. You can create, start, stop, move, or delete a container using the Docker API or CLI.
- Can be run on local machines, virtual machines, or deployed to the cloud.
- Is __portable__ (and can be run on any OS).
- Is __isolated from other containers__ and runs its own software, binaries, configurations, etc.


-------
## <span style="color:#66C3FF">Docker Hub<span>
-------
Docker Hub is the world's largest library and community for container images ( https://hub.docker.com/ ).

<div align="center">
<img src="../figures/docker_hub.png" alt="docker_hub" width="75%">
</div>


You can directly get official docker images or computed by the community.


-------
## <span style="color:#66C3FF">Hello world and docker basic commands<span>
-------



### <span style="color:#66C3FF">Docker pull<span>

This command is used to download a docker image from a registry, such as Docker Hub, to your local system.

You can try to run the following command to pull your first docker image:

```bash
docker pull hello-world
```

You can now try to pull another image which will be used later in this workshop session: a postgresql (version 17) database with postgis extension (version 3.5).

```bash
docker pull postgis/postgis:17-3.5
```

### <span style="color:#66C3FF">Docker ps<span>
This command shows the __list of currently running containers__ along with their details, such as container ID, names, and status.
```bash
docker ps
```

If you add the ```--all``` parameter, you will also see stopped containers:
```bash
docker ps --all
```



### <span style="color:#66C3FF">Docker run<span>

This command__creates and runs a new Docker container__ based on a specified image. It can also be used to specify various options and settings for the container.

If you try now to instanciate the hello-world image previously pulled:
```bash
docker run hello-world
```

You should see some kind words (coming from docker team) in the container logs! ;-)



If you check the list of active containers, you will discover that docker assign a random (fancy) name to your container. To avoid this and easily keep track of your docker containers, you can add a ```--name``` parameter.

Here is another docker run example related to our workshop: a postgis container with a custom setup:

```bash
docker run --name postgis_sotm -p 5482:5432 -e POSTGRES_PASSWORD=postgres -d postgis/postgis:17-3.5
```

You can see that we specify a particular name for the container (postgis_sotm), handle the port forwarding, set environement variables (postgres database user password), run in background (-d), version of Postgresql (17) and finally the version of Postgis (3.5).

Everything in 1 single command and deployed in only few seconds!
Impressive, isn't it?!



### <span style="color:#66C3FF">Docker start<span>
This command is used to __start one or more stopped containers__, allowing them to run in the background.

If your postgis_sotm container is not running (TIP: check it with ```docker ps``` command), you can start it with the following command:

```bash
docker start postgis_sotm 
```


### <span style="color:#66C3FF">Docker stop<span>
This command is used to __stop one or more running containers__, pausing their execution.


If your postgis_sotm container is running (TIP: check it with ```docker ps``` command), you can stop it with the following command:

```bash
docker stop postgis_sotm 
```

### <span style="color:#66C3FF">Docker rm<span>
This command removes one or more stopped containers from your system. Containers must be stopped before they can be removed.

```bash
docker rm postgis_sotm
```

```{note}
If you removed your postgis_sotm with the previous command, don't forget to recreate it for the next exercices! ;-) 
```


### <span style="color:#66C3FF">Docker exec<span>
This command allows you to run a command inside a running container. It provides a way to execute commands in an already running container without the need to start a new shell session.

If we want to access a bash terminal in the previous postgis container, we can run:

```bash
docker exec -it postgis_sotm bash
```


-------
## <span style="color:#66C3FF">BONUS: Dockerfile<span>
-------

<span style="color:red">__We won't be using dockerfiles during this workshop, just pre-built images available on the Docker Hub. This section is here for your information.__<span>

A Dockerfile is a text-based file with no file extention that contains a __script of instructions__.
Docker uses this script to __build a container image__.

Here is an example of Dockerfile for an imposm image (built by GeoPostcodes team)


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


You can see that a Dockerfile is composed of adapted linux commands such as ```groupadd```, ```wget```, ```apt-get```, ```rm``` etc. to prepare the image based on a linux dockerized OS such as Debian, Alpine or Ubuntu for example.

To build an image from a Dockerfile and with a tag, you can run a command similar to the following in your Dockerfile folder location:

```bash
docker build -t my-image-tag .
```

-------
## <span style="color:#66C3FF">Docker Volumes<span>
-------
A Docker volume is a persistent data storage mechanism that allows containers to store and share data independently from the container lifecycle. Volumes provide a way to persist data even if the container that created them no longer exists. They are used to store files, databases, or any other type of data that containers need to read from or write to during their lifetime. 

 By using volumes, developers can __separate the concerns of storage from the application logic__, making it easier to maintain, update, and scale containerized applications without losing critical data when containers are removed or replaced.

-------
## <span style="color:#66C3FF">Docker Compose<span>
-------
A ```docker-compose.yml``` file is a configuration file used to define and manage __multi-container Docker applications__. It allows you to __specify the services, networks, and volumes required__ for your application in a declarative manner. In a docker-compose file, you can define multiple services, each representing a containerized application component. These services can be configured with various options, including Docker images, environment variables, ports, volumes, and dependencies. By using a docker-compose file, you can __define the entire application stack, its relationships, and configurations in one centralized place__. 

When you run docker-compose up, Docker Compose reads the configuration from the file and starts the specified services, creating the necessary containers, networks, and volumes based on the defined configurations. This simplifies the process of managing complex applications by enabling you to launch and scale multi-container setups with a single command, making it easier to develop, test, and deploy applications in different environments.


In your ```docker-compose.yml``` folder location, you can deploy it by running the following command:

```bash
docker-compose up
```


If you want to remove the previous containers version before starting a new setup, you can use the ```docker-compose down``` command. This command stops and removes the containers defined in your ```docker-compose.yml``` file:

```bash
docker-compose down
```