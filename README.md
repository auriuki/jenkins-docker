# jenkins-docker
## Simple docker file for jenkins with docker inside
Dockerfile for Jenkins with Docker inside

### About
I was planning to run many application like python projects 
and other in Docker on Jenkins using Jenkinsfiles to automate delivery.
I did not expect that I face so many issued only 
because I decided to run Jenkins on Windows instead Linux.

The last problem overflowing bitterness was problem with using dockers.

As solution I found *jenkins-withdocker* docker image, but it was still not perfect.
This docker images are not up to date when you use global docker repository 
(it have to be rebuild every time when Jenkins releases new version). 
It cause requirement to manual plugin installation 
and solving problems with plugins security after jenkins version upgrade, etc.

#### Reason
I created this repository to be able setup docker in the future 
with simple steps described in this README.

##### Why Jenkins on windows in docker?
Jenkins on Windows have problems with: 
- running Jenkinsfiles  (error like: [Error response from daemon: the working directory ... is invalid, it needs to be an absolute path.](https://stackoverflow.com/a/48390638/11318366))
- [wsl](https://docs.microsoft.com/en-us/windows/wsl/about) is not fully supported (problems with paths, tools visibility/execution) 
- [dockers](https://issues.jenkins-ci.org/browse/JENKINS-50857) are not supported on windows (there are many other tickets)

I spent too much time on solving integration between windows, wsl and docker, configuration, etc. so I decided to move Jenkins to linux using Dockers.

#### Solution
The best solution turned out to be use Docker image with Jenkins with Docker inside. 


Therefore, as the solution I have created simple Dockerfile (source [jenkins-withdocker](https://github.com/getintodevops/jenkins-withdocker/blob/master/Dockerfile)) 
and README about how to setup a docker with jenkins (with docker inside) locally on Windows.

# Installation
## Docker on Windows
### Requirements:
- CPU Virtualization enabled (read below)
- Create account on docker.com (required to start docker - it allows to publish private docker image to repository - not needed but account is required)

### Instruction and detail: 
[Install Docker Desktop for Windows](https://docs.docker.com/docker-for-windows/install/)

#### In short steps:
1. Download docker installer [Docker Desktop Installer.exe](https://download.docker.com/win/stable/Docker%20for%20Windows%20Installer.exe)
2. Follow the install wizard
3. Click Finish
4. Run application and login

#### CPU Virtualization
In order to run docker CPU Virtualization must be enabled. You can enable it in BIOS.
Check if virtualization [is enabled](https://docs.docker.com/docker-for-windows/troubleshoot/#virtualization-must-be-enabled)

## Build docker image and run
- Checkout this repository or copy Dockerfile to any location
- Open Command Prompt (Win+R, typ cmd)
- Go to place with Dockerfile
- Build docker container:
```bash
$ docker build .
```
or:
```bash
$ docker build -f <path_to_Dockerfile_file>
```
- Find build docker container
```bash
$ docker container ls
```
```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                                              NAMES
2535364c7a3e        3f56b90c6bb8        "/sbin/tini -- /usr/…"   About an hour ago   Up About an hour    0.0.0.0:8080->8080/tcp, 0.0.0.0:50000->50000/tcp   jenkins
```
- Copy docker *CONTAINER ID* 
- Run docker. E.g.
```bash
docker run -d -v "D:/Jenkins_docker":/var/jenkins_home -p 8080:8080 -p 50000:50000 --name jenkins 2535364c7a3e
```
Where:<br/>
`D:/Jenkins_docker` - place where jenkins will be installed (configuration + workspace)<br/>
`2535364c7a3e` - example copied docker container ID

### Stopping docker
```bash
$ docker container stop 2535364c7a3e
```

Where:
`2535364c7a3e` - example copied docker container ID

### Running created container
```bash
$ docker container start 2535364c7a3e
```

### Removing container and image
Find CONTAINER ID and IMAGE
```bash
$ docker container ls
```
E.g.:
```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                                              NAMES
2535364c7a3e        3f56b90c6bb8        "/sbin/tini -- /usr/…"   About an hour ago   Up About an hour    0.0.0.0:8080->8080/tcp, 0.0.0.0:50000->50000/tcp   jenkins
```
###### Stop container
Stop container before removing. 
```bash
$ docker container stop 2535364c7a3e
```
###### Remove container
Stopping and removing container is required to be able rebuild docker (container)
```bash
$ docker container rm 2535364c7a3e
```
###### Remove image
```bash
$ docker image rm 3f56b90c6bb8
``` 

## Jenkins configuration
After *Running created container* Jenkins is starting up.

Open web browser and type address `localhost:8080` and follow the installation.<br/> 

The `/var/jenkins_home/secrets/initialAdminPassword` file is located under mounted place. e.g. in my case it is `D:/Jenkins_docker/secrets/initialAdminPassword`
(`/var/jenkins_home` is "mapped" to `D:/Jenkins_docker`)

I suggest install without any plugins (select None) and add it later.

### Blue ocean
- after installation go to Jenkins Settings 
- install `Blue ocean` plugin 
- select *Restart Jenkins when installation is complete and no jobs are running* option  

#### Testing
- Open Blue Ocean view (icon on the left)
- Click Add new Pipeline
- Select your source - e.g. github project (generate token on github, etc.)
- Build project with docker image

## Source
- https://docs.docker.com/get-started/ - official docker documentation
- https://docs.docker.com/docker-for-windows/install/ - Docker for windows - installation instruction
- https://www.ictshore.com/devops/docker-jenkins-tutorial/ - how to setup newest jenkins in docker (without docker inside)
- https://github.com/getintodevops/jenkins-withdocker - Dockerfile with docker inside (docker image jenkins-withdocker from global repository doesn't contain the newest jenkins - it need be build from this Jenkinsfile)
