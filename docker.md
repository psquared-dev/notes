To shows all running contaners

```bash
docker ps
```

To shows all running contaners inlcuding the ones that have exited:

```bash
docker ps -a
```

Returns all the ids of the stopped or running containers

```bash
docker ps -aq
```

Shows images avaliable locally

```bash
docker images
```

Installs ubuntu image

```bash
docker install ubuntu
```

This will install the container but doesn't runs it.

```bash
docker container create hello-world
```

Creates a container from budybox image and runs it in interactive mode.

```bash
docker run -it busybox
```

to stop the running container:

```bash
docker stop <container_name|container_id>
```

Starts the nginx container and maps port and volume

```bash
docker run -p 8080:80 -v ~/nginx:/usr/share/nginx/html nginx
```

Same as above but starts the container in background

```bash
docker run -p 8080:80 -v ~/nginx:/usr/share/nginx/html -d nginx
```

To show the logs

```bash
docker logs <container_name|container_id> 
```

To remove the container

```bash
docker rm <container_name|container_id>
```

`docker container rm <container_name|container_id>` does the same thing as above

To start the exited container:

```bash
docker start <container_name|container_id>
```

Attaches the stdout to the container:

```bash
docker start <container_name|container_id> -a
```

To remove all stopped container:

```bash
docker container prune
```

The above command is equivalent to the following:

```bash
docker ps -aq | xargs docker rm
```

To specify the command to run when the container starts, this overrides the default command

```python
docker run -it -v /home/x/x/nginx/:/home python python3 /home/h.py                                                        
```

Intead of specifying the absolute path to the script, we can use the -w option to specify the current working directory as follows:

```bash
docker run -it -v /home/x/x/nginx/:/home -w /home python python3 h.py
```

To create persistent data storage for mongo:


```bash
docker run -d -v $PWD/db:/data/db mongo
```

To execute the command in a running container:

```bash
docker exec -it <container_name_or_id> <command>
```

To build an an image from the Dockerfile (assuming present in the curr directoty). The -t option is used to specify the tag.

```bash
docker build -t first-image .
```

Here is a sample Dockerfile used for the above build:

*Dockerfile*

```Dockerfile
FROM ubuntu

LABEL mintainer="pp"

USER root

COPY ./date.bash /

RUN apt -y update
RUN apt -y install curl bash
RUN chmod 755 /date.bash

USER nobody

ENTRYPOINT ["/date.bash"]
```

*date.bash*

```bash
#!/bin/bash
date
```

To run this image type the following:

```bash
docker run first-image
```

Output:

```bash
Sun Nov  5 08:14:59 UTC 2023
```

We can specify an alternate name for the Dockerfile using the --file option.

```bash
docker build -t first-image -f mydocker .
```


