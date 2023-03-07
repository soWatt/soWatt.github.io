---
title: Docker Notes 
published: true
---
<br/>
# [](#header-1) Docker and containers
<br/>
It's been over a year since I added any writings to this blog, so where have I been? Have I been meditating under a waterfall hoping that kubernetes makes sense all of a sudden? Nothing that admirable, I've just been studying for certs, working and trying to figure out what's next for my career. I'm working through the [DevOps Roadmap](https://roadmap.sh/devops) and looking to land a role in DevOps, DevSecOps, or SRE. In order to continue on this journey, I'll have to face my demons and become confortable with containerization. The following are my notes on the topic.
<br/>
<br/>
What is a container?
<br/>
<br/>
A container (per the definition straight from docker) is "A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another."; wow, that is a solid piece of engineering. Too many times have we told our friends and coworkers "well it works just fine on my machine." while their computers throw a series of dependency issues, exception, and the eventual Blue Screen. 
<br/>
A Docker container is lightweight and standalone, it has everything contained within it to run an application. All the libraries, code, runtime, system tools, settings, absolutely everything you need. You share the image with someone and the Docker Engine works its magic to convert that image to a container. This sounds a lot like virtualization, what are the differences?:
<br/>
1. containers run on top of the Host operating system which means that the containers running on that host need to match the host OS's Kernel (linux containers for linux OS and Windows containers for Windoes.
2. Virtual Machines are built on top of a hypervisor. The VM has it's own kernel and everything (even though it's defined through software) 
3. Use them together for max flexibility!
<br/>
<br/>
Docker documentation provides plenty of tutorial material for Docker/Docker Desktop which can be found [here](https://docs.docker.com/get-started/). 
<br/>
<br/>
So what about container security? Since it's so easy to spin a new container from an image, the most obvious path to exploitation is malicious changes to the build files for an image (I.E Dockerfile). It's good practice to have a generic base image which is used to build all images from (and keep this base image secure). Other areas of focus should be OS security that the contianer runs on and secure coding best practices.
<br/>
<br/>
And this is enough info on containers to start to look at contianer escapes ;)
<br/>
![](/images/dockernotes/1.png)
