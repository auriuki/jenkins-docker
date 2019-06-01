# jenkins-docker
## Simple docker file for jenkins with docker inside
Dockerfile for Jenkins with Docker inside

### About
I was planning to run many application like python projects and other in Docker on Jenkins using Jenkinsfiles to automate delivery.
I didn't expect that I face so many issued only because I decided run Jenkins on Windows instead on Linux.

The last problem overflowing bitterness was problem with dockers.

As solution I found jenkins-withdocker, but It was still not perfecr.
This docker images are not up to date when you use global docker repository (it have to be rebuild everytine Jenkins release new wersion) it cause requirement to manual plugin installation and solving problems with plugins security after jenkins version upgrade, etc. 

#### Reason
I created this repository to be able setup docker in the future with simple steps described in this README.

##### Why Jenkins on windows in docker?
Jenkins on windows have problems with: 
- running Jenkinsfiles  (error like: [Error response from daemon: the working directory ... is invalid, it needs to be an absolute path.](https://stackoverflow.com/a/48390638/11318366))
- [wsl](https://docs.microsoft.com/en-us/windows/wsl/about) 
- supporting [dockers](https://issues.jenkins-ci.org/browse/JENKINS-50857) and many other tickets

I spent too much time on solving integration between windows, wsl and docker, configuration, etc. 

#### Solution
The best solution turned out to be use Docker image with Jenkins with Docker inside. 

So as solution I created simple Dockerfile (source [jenkins-withdocker](https://github.com/getintodevops/jenkins-withdocker/blob/master/Dockerfile)) and README about how to setup a docker with jenkins (with docker inside) locally on Windows.

# Installation
## Docker on Windows
## Build docker image and run
## Jenkins configuration
### Blue ocean
#### Testing

## Source
- https://www.ictshore.com/devops/docker-jenkins-tutorial/ - how to setup newest jenkins in docker (without docker inside)
- https://github.com/getintodevops/jenkins-withdocker - Dockerfile with docker inside (docker image jenkins-withdocker from global repository doesn't contain the newest jenkins - it need be build from this Jenkinsfile)
