# jenkins-docker

# Read before
[ALWAYS START FROM OFFICIAL DOCUMENTAION!!! - click it](https://jenkins.io/doc/book/installing/)
It's strongly said to run Jenkins in Docker on Windows!!! **Don't even try run Jenkins on Windows directly!!!** Well you can, for simple tasks, but don't expect that everything will work (like Jenkinsfile, dockers). For it use Jenkins on [Docker](https://docs.docker.com/get-started/).

## Using Simple Docker for Jenkins with Docker inside
Project contains instruction how to setup simple Docker container with Jenkins with Docker inside.
I also contains simple Jenkinsfile and Dockerfile just to verify that Docker file from official repository is still working - it should.
And it also show how can you use Dockerfile(s) created for application (this Dockerfile can contains instruction how to install your application) in Jenkinsfile. 

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
The best solution turned out to be use Docker image with Jenkins with Docker inside as it is suggested in official Jenkins documentation, so don't event try run Jenkins on Wondows without doceker! 
Well you can use it without docker, but only for a simple tasks. 


Therefore, as the solution I create this README to guide you how to setup easily working Jenkins on Windows.

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
- create any directory for Jenkins files 
(Jenkins in Docker will be installed outside docker image and will be mounted - to be able 
recreate environment without losing jenkins configuration, jobs, workspace, installed additional plugins, etc.)
- use command line and execute instruction below
```cmd
docker run ^
  -u root ^
  --rm ^
  -d ^
  -p 8080:8080 ^
  -p 50000:50000 ^
  -v "E:/Docker/Jenkins":/var/jenkins_home ^
  -v /var/run/docker.sock:/var/run/docker.sock ^
  --name jenkins ^
  jenkinsci/blueocean
```
Where:<br>
- `-u root` - is needed to be able bind docker service with jenkins<br>
- `-d` - run as deamon, so you can detach console window<br>
- `-p 8080:8080` and `-p 50000:50000` - expose jenkins ports for main Jenkins and slave communication<br>
- `-v /var/run/docker.sock:/var/run/docker.sock` - mount/bid docker sockets<br>
- `--name jenkins` - alias for created container - e.g. to be able login docker machine easily<br>
- `jenkinsci/blueocean` - name of docker used to create Jenkins - released everytime when new blue ocean is released <br>
More details on official Jenkins documentation

### Running created container
```bash
$ docker container start jenkins
```

### Removing container and image
###### Listing running container
```bash
C:\Users\Yuki>docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                              NAMES
42e3edd6087b        jenkinsci/blueocean   "/sbin/tini -- /usr/…"   12 minutes ago      Up 12 minutes       0.0.0.0:8080->8080/tcp, 0.0.0.0:50000->50000/tcp   jenkins
``````
###### Stop container
Stop container before removing. 
```bash
$ docker container stop jenkins
```
You can use Container ID instead alias name
###### Remove container
Stopping and removing container is required to be able rebuild docker (container)
```bash
$ docker container rm jenkins
```
###### Remove image
```bash
$ docker image rm jenkinsci/blueocean
``` 

###### Bulk removing images and containers:
Windows:
```cmd
@echo off
FOR /f "tokens=*" %%i IN ('docker ps -aq') DO docker rm %%i
FOR /f "tokens=*" %%i IN ('docker images --format "{{.ID}}"') DO docker rmi %%i
```
Linux
```bash
#!/bin/bash
# Delete all containers
$ docker rm $(docker ps -a -q)
# Delete all images
$ docker rmi $(docker images -q)
```
Or short (on windows as well):
```bash
$ docker system prune
```

## Jenkins configuration
Open web browser and type address `localhost:8080` and follow instruction: [Unlocking Jenkins](https://jenkins.io/doc/book/installing/#unlocking-jenkins).<br/> 

The `/var/jenkins_home/secrets/initialAdminPassword` file is located under mounted place. e.g. in my case it is `D:/Jenkins_docker/secrets/initialAdminPassword`
(`/var/jenkins_home` is "mapped" to `D:/Jenkins_docker`)

I suggest install without any plugins (select None) and add it later.

And that's it. You can open webbrowser, typ [http://localhost:8080](http://localhost:8080) and start using jenkins with docker.<br>
E.g. Open Blue ocean view, create new pipeline, use e.g. github, add token, project and that's it. You can run Jenkinsfile pipeline with dockers.

## Source
- https://docs.docker.com/get-started/ - official docker documentation
- https://docs.docker.com/docker-for-windows/install/ - Docker for windows - installation instruction
- https://www.ictshore.com/devops/docker-jenkins-tutorial/ - how to setup newest jenkins in docker (without docker inside)
- https://github.com/getintodevops/jenkins-withdocker - Dockerfile with docker inside (docker image jenkins-withdocker from global repository doesn't contain the newest jenkins - it need be build from this Jenkinsfile)
