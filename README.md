# Docker tutorial

Install Docker as appropriate for your system. 

> https://docs.docker.com/engine/installation/

## Starting Docker

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
Both commands need to be run each time you start Docker in another terminal window. 

You need to know the IP address of the `default` virtual machine
in order to use the containers you run there. To obtain its IP address run: 
```
docker-machine ip default
```

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

From the same terminal you started, run: 
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

The two parameters are required in this case (when you are running a shell). 
The `--tty=true` parameter allocates a pseudo-TTY.
The `--interactive` parameter keeps a standard tty open. 
They can be abbreviated with `-ti`.

The `docker run` command has many parameters. 
See [the documentation](https://docs.docker.com/v1.8/reference/commandline/run/).



## Docker Hub

https://hub.docker.com


