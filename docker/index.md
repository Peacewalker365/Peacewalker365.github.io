# Docker


### Why Docker?
Your proj can only run on your machine but not others.
- One or more file missing.
- Software version mismatch.
- Diff config setting.

Docker can pack everything in an isolated env and make it run anywhere.
And diff docker containers can run on same machine.

#### HYPERVISORS
- VitualBox
- VMware
- Hyper-v (win only)

![Hypervisor](/img/Hypervisor.png)

![Virtual Machines](/img/Virtual_Machines.png)

#### Probs With Virtual Machines
- Each VM needs a full-blown OS 
- Slow to start
- Resource intensive (it takes a slice of the hardware)

#### Containers  
- Allow running multiple apps in isolation
- Lightweight
- Use OS of the host (all containers share the same host OS)
- Start quickly
- Need less hardware resource (share the host resources so that we don't need to give cores to it specifically)

### Arch of Docker 

![Arch of Docker](/img/Arch_of_Docker.png)

![Container As a Process And Sharing the Kernel](/img/Container_As_a_Process_And_Sharing_the_Kernel.png)

![Docker on Diff OS](/img/Docker_on_Diff_OS.png)

### Installing

```bash
# docs.docker.com/get-docker/
# open dockerHub

docker version

```
### Dev Workflow

![Dockerfile to Image](/img/Dockerfile_to_Image.png)

The images get loaded in a container.

`docker run appName` to run it in a container.

![Docker Registry Like github to git](/img/Docker_Registry_Like_github_to_git.png)

### Docker in Action

```bash

mkdir hello-docker
cd hello-docker
code . # open in vsCode, you can use others like vim

```

add a new file -> app.js
add one line -> `console.log("Hello Docker!");`
You need Node installed.
`node app.js`
then it should print Hello Docker! in terminal

#### Instructions
- start with an others
- install Node 
- Copy app files
- Run node app.js

#### BUT NOW we can write this instructions in the dockerfile!

add another file named `Dockerfile` with no extensions.
install the docker extension for vsCode.

```dockerfile
FROM node:alpine
COPY . /app # we gonna copy all the files in the current dir to the /app of that image
CMD node /app/app.js

or use
WORKDIR /app
CMD node app.js


# go to dockerHub to find these image like linux or node etc.
# after the : is the tag of the image, for example alpine is a specific ver of linux

```


```bash
docker build -t hello-docker .
# -t for tag that to identify the image
# then the app name
# then the dir to find it
```

notice that the image is not in the app dir.

```bash
docker image ls 

```

![Docker Image(latest) with Node, Linux alpine, and the app files](/img/Docker_Image(latest)_with_Node,_Linux alpine,_and_the_app_files.png)

`docker run hello-docker` run it using image name 

You can publish it on dockerhub to run it anywhere.

you can also go to play with docker to have a try.
`docker run PceWlkr/hello-docker` 

### Linux Distros
- Ubuntu
- Debian
- Alpine 
- Fedora
- CentOS

#### Ubuntu 
go to dockerhub, search for Ubuntu.
we can use `docker pull ubuntu`
but here I will use a shortcut
we can use `docker run ubuntu`, if docker cannot find it locally then it will download it and run it in a container.
However, since we didn't interact with the container, it stopped.
if we `docker ps` we cannot see it 
if we `docker ps -a` we can see it's stopped
We need to use `docker run -it ubuntu` to start it in interactive mode

And then we entered its shell.
`root@2f856e34654e5:/# `
root indicates I'm having the root user mode having the highest access permission
After the @ is the name of the container.
The / means the dir we are at.
And there is a # meaning we have the highest privilege since we are in root mode.

```bash
whoami
echo $0
# /bin/bash
# we can see the location of shell program

history

!2
# will run the 2nd command in history

```

#### Package Manager

`apt`
list - list oackages based on names
search - search in package descriptions
show - show package details
install 
reinstall
remove
autoremove - remove automatically all unused packages
update - update list of available packages 
upgrade - upgrade the sys by installing/upgrading packages 
full-upgrade - upgrade the sys by removing/installing/upgrading
edit-sources - edit the source information file 
satisfy - satisfy dependency strings

`apt-get` - look it up yourself LMAO



`apt install nano` will give a error msg since you may not have nano in your package list
`apt update` to update the package database
`apt install nano`

### File System

![Linux File Tree](/img/Linux_File_Tree.png)

bin: binaries
boot: booting related
dev: devices, in linux, everything is a file
etc: editable text configuration
home: the home dir of users
root: the home dir of root user
lib: libs, e.g. software dependencies
var: varibles, updated frequently, like logs or app data 
proc: running processes (as files)

`pwd` print current dir
`ls`
`ls -1` one item per line
`ls -l` full info list
`cd ../..` 2 level up 
`cd ~`
`mv hello.txt /etc hello123.txt`
notice that <ctrl + w> remove one word in shell 
`rm file1.txt file2.txt`
`rm file*`
`rm -r fileDir/`
`touch file1.txt`
`cat file1.txt`
`more fileLong.txt` use more if the file is long. we can use space or enter 
`apt install less` 
`less file1.txt` we can you up and down and space and enter 

`head -n 5 file1.txt`
`tail -n 2 file2.txt`

#### Redirectory
`cat file1.txt > file2.txt`
`cat file1.txt file2.txt > combined.txt`
`echo hello > hello.txt`
`ls -l /etc > files.txt`

`<` Redirectory for input







![Workflow with Docker](/img/Workflow_with_Docker.png)


## Demo Project



