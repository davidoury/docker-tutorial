# Docker tutorial

Install Docker on your system. 

> https://docs.docker.com/engine/installation/

## Starting Docker

This section has three subsections for each platform: Mac, Windows, Linux.

### Starting Docker on a Mac

Docker can be started on a Mac
from a shell (opened with a terminal program) as follows:
```
docker-machine start default
eval $(docker-machine env default)
```
The first command starts (and creates if necessary) a virtual machine named `default`
on which Docker containers will be run. 
The second line sets up the shell environment (you are currently using) 
to run `docker` commands on the `default` virtual machine.
Both commands should be run each time you want to use Docker from a new terminal window. 

You will (usually) need to know the IP address of the `default` virtual machine
in order to use the containers you run there. To obtain its IP address run: 
```
docker-machine ip default
```

For details on the `docker-machine` command see:

- https://docs.docker.com/machine/get-started/
- https://docs.docker.com/machine

### Starting Docker on Windows

Run the Docker Quickstart Terminal, which will start 
the `default` virtual machine and display its IP address. 
This also works on a Mac, but I prefer the command line, 
which allows one to create/run/destroy additional virtual machines
and is required to use the rest of the Docker ecosystem. 

### Starting Docker on Linux

Would someone that runs Linux email me <david@scatter.com> the startup commands? 

## Running containers

### Hello world

From the same terminal in which you started Docker, run: 
```
$ docker run hello-world
```
Notice, unless you have run this command already, the first two lines of output:
```
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
```
which indicates that Docker does not have the `hello-world` container in cache 
and will download the latest version. 
The output "Hello from Docker." indicates that the container ran correctly. 
Try the command a second time. 

### CentOS and Ubuntu distributions

To create a container with the latest CentOS distribution (7) 
or the latest Ubuntu distribution (16.04) as of 19 Jun 2016 run:
```
$ docker run --interactive --tty=true centos bash
```
or
```
$ docker run --interactive --tty=true ubuntu bash
```
You will see a `bash` shell prompt running in the container. 
Note the `bash` command at the end of the docker command.
Any valid command on the CentOS/Ubuntu system 
can be specified there.

When you exit the shell the container will stop running 
and all changes you have made, while using the shell, will vanish. 

These two parameters are required when you are running a shell with Docker: 

- `--tty=true` allocates a pseudo-TTY
- `--interactive` keeps a standard tty open

They can be abbreviated as `-ti`.

The `docker run` command has many parameters. 
See the documentation:

- https://docs.docker.com/v1.8/reference/commandline/run/
- https://docs.docker.com/engine/reference/run/


## Docker Hub

Many containers are available on the Docker Hub. See 

> https://hub.docker.com

Find the search field at the top of the page. 

## Building containers 

Clone this GitHub repository with the command:
```
$ git clone https://github.com/davidoury/docker-tutorial.git
```
This command creates a subdirectory `docker-tutorial` in your current directory. 
Set your current directory to this subdirectory with:
```
$ cd docker-tutorial
```
In this directory you will see the `mybuild` subdirectory. 
Change your current directory to it. 
Now run:
```
$ ls
$ cat Dockerfile
```
The file `Dockerfile` is used to configure containers (to build). 
Every such file starts with a `FROM` line that specifies the base container. 
Additional lines add to or modify that base container. 

To start, create the Dockerfile:
```
$ echo "FROM ubuntu:14.04" >  Dockerfile
$ cat Dockerfile
```

Build a container whose base is the `ubuntu:14.04` container with the command:
```
$ docker build -t mynametag .
Sending build context to Docker daemon 3.584 kB
Step 1 : FROM ubuntu:14.04
 ---> 8f1bd21bd25c
Successfully built 8f1bd21bd25c
```
The output from the command is included above. 

Run this container with the following command
```
$ docker run --interactive --tty=true mynametag bash
```
which is nearly identical to the two `docker run` commands in the previous section, 
except for the name of the image to run. 

You can run any command available to the container. Try:
```
$ docker run mynametag cat /etc/hosts
```

To list the images you have created and retrieved run:
```
$ docker images
```
Images can be deleted with the command:
```
$ docker rmi --force hello-world
```

Confirm that this worked as expected with:
```
$ docker images
```

## Dockerfile

The use of several Dockerfile directives are described below. 

### RUN directive

Run the following to add a command to `Dockerfile`.
```
echo "RUN echo hello > /tmp/world" >> Dockerfile`
cat Dockerfile
```
Rebuild the image.
```
$ docker build -t mynametag .
```
Run a bash shell in the container
```
$ docker run --interactive --tty=true mynametag bash
``` 
and then display the file `/tmp/world` and stop the container. 
```
$ cat /tmp/world
$ exit
```

### ENV directive 

You can create and access environment variables.
Add three more commands to `Dockerfile`.
```
echo "ENV OLDFILE /tmp/world"     >> Dockerfile
echo "ENV NEWFILE /tmp/world.new" >> Dockerfile
echo 'RUN mv "${OLDFILE}" "${NEWFILE}' >> Dockerfile
cat Dockerfile
```
Rebuild the image and run bash in the container.
```
$ docker build -t mynametag .
$ docker run --interactive --tty=true mynametag bash
```
Now check for the renamed file. 
```
$ ls /tmp
$ exit
```

### ADD directive

Files can be moved from the build directory (currently `mybuild`) 
into the container filesystem.
Add these two commands to `Dockerfile`, then build and run the image. 
```
$ echo "ADD testdir.tgz   /tmp" >> Dockerfile
$ echo "ADD testdir/file1 /tmp" >> Dockerfile
$ cat Dockerfile
$ docker build -t mynametag .
$ docker run --interactive --tty=true mynametag bash
```
Now check for the directory `testdir` and the file `file1` in `/tmp`.
```
$ ls /tmp
$ exit
```

### CMD directive, detached containers, stopping containers

The `CMD` directive provides a default command to be run, 
if none is specified, when the container is started.
Add this command to `Dockerfile` and rebuild the container.
```
$ echo "CMD sleep 10; ls /tmp" >> Dockerfile
$ cat Dockerfile
$ docker build -t mynametag .
```
Now run the container, omitting the `--interactive` and `--tty` parameters.
```
$ docker run mynametag
```
Now run the container after adding the parameter `--detach=true`. 
Then immediately run the `docker ps` command. 
```
$ docker run --detach=true mynametag 
$ docker ps
```
Notice that the container is running. 

The next three code blocks will run a detached container, 
find its name and then stop the container. 
```
$ docker run --detach=true mynametag sleep 60
```
Run the following command, then look under the `NAMES` field 
and copy the name of the running container. 
```
$ docker ps
```
Now run:
```
$ docker stop [container name]
$ docker ps
```

### VOLUME directive

Volumes are the Docker method for saving data outside of a container.

Create a new Dockerfile.
```
$ echo "FROM ubuntu:14.04" >  Dockerfile
$ echo "VOLUME /work"      >> Dockerfile
$ cat Dockerfile
```

Create a Docker container that contains persistent data in directory `/work`.
```
docker create --volume /work --name datacontainer mynametag
```
Note that the container is not running.
```
$ docker ps
```

Run the `mynametag` container and use the `/work` directory from `datacontainer`.
```
$ docker run -ti --volume /work --volumes-from datacontainer mynametag bash
```
From the container shell type:
```
$ echo sdf > /work/lkj
$ exit
```

Run the `mynametag` container
```
$ docker run -ti -v /work --volumes-from datacontainer mynametag bash
```
and check for the file we placed there:
```
$ cat /work/lkj
$ exit
```

Run: 
```
$ docker ps --all | grep datacontainer
```
Remove the container:
```
$ docker rm datacontainer
$ docker ps --all | grep datacontainer
```


### EXPOSE directive

This leads into container networking, which should wait for another day. 

See also the documentation for the `Dockerfile`:

- https://docs.docker.com/engine/reference/builder/
- https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/