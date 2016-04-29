---
layout: post
title:  "Hyper-V's scarce docker support"
date:   2016-02-01 10:10:21 +0000
categories: docker hyper-v devops
description: "Hyper-V's scarce docker support"
published: false
tags:
- docker
- hyper-v
- devops
- windows
---

# Hyper-V's scarce docker support

TL,DR;  
Uninstall completely Hyper-V and enjoy virtualbox with 64-bit emulation and Android emulation.

### How to set things up

First things first, I'm assuming that you already went through with the installation process.
If not, download docker for windows, launch the installation and click next until the end. It's really that simple.

Once installed there are some first steps that are necessary to get familiarity with the new environment.
The tool I would like to start with is ```docker-machine```, it allows to provision docker containers on Windows, Mac or remote hosts. ```docker-machine``` works on top of a virtualization layer, which can be Hyper-V or, as we'll see later, something other.

[Docker-Machine with Microsoft Hyper-V]

```
// create a new VM based on Boot2Docker
> docker-machine create --driver hyperv vm

// start a previously created vm called 'vm'
> docker-machine start vm

// if certificates are unavailable
> docker-machine regenerate-certs vm

// list current environment
> docker-machine env vm

// assuming you get a response similar to the following
SET DOCKER_TLS_VERIFY=1
SET DOCKER_HOST=tcp://192.168.1.12:2376
SET DOCKER_CERT_PATH=C:\Users\jeangougou\.docker\machine\machines\vm
SET DOCKER_MACHINE_NAME=vm
REM Run this command to configure your shell:
REM    @FOR /f "tokens=*" %i IN ('docker-machine.exe env vm') DO @%i

// next step would be to verify the connection and ssh in, the easy way is
> docker-machine ssh vm

// but if we want to start using docker
// remmber to change {USERNAME} to your windows account name
> docker -D --tlsverify --host=tcp://192.168.1.12:2376 --tlscacert=C:\Users\{USERNAME}\.docker\machine\certs\ca.pem --tlskey=C:\Users\{USERNAME}\.docker\machine\certs\key.pem --tlscert=C:\Users\{USERNAME}\.docker\machine\certs\cert.pem info
```

This last command can happen to fail due to timeout, which is puzzling because the one leveraging ```docker-machine``` is perfectly fine.  

### Hyper-V limitations

If you happen to play with Android development or just need to run many different virtual machines locally, you will have figured out that Hyper-V is pretty much incompatible with anything else. As the default installation of _docker_ and other tools require _virtualbox_ you will notice the following happening:

- _VirtualBox_ will not be able to run 64-bit VMs
- Android emulator will fail to start

Luckily you can solve each and every issue by uninstalling Hyper-V completely and using only virtual box.
If you do that, not only will you be able to run 64bit images, but every other tool available in the _docker_ toolbox will work seamlessly.  
Hyper-V is nice, but just not ready for prime time in development IMHO.

## When you finally decide Hyper-V is not worth the trouble

By using only _VirtualBox_ and the default tools that docker provides you for windows, through very simple steps you can run any container.

```
// let's start by creating a new VM called dev
> docker-machine create --driver virtualbox dev
> docker-machine env --shell cmd dev

// to setup local environment
> @FOR /f "tokens=*" %i IN ('docker-machine env --shell cmd dev') DO @%i

// double checking the environment (I'm using cygwin too)
> env | grep DOCKER

// to validate the connection to the newly created instance
> docker-machine  config dev

// finally check the docker version
> docker version

// assuming you have a local Dockerfile
> docker build .
```
#### Issues you might encounter

When blocked by hypervisor not running a potential solution is to run the following command before restarting

```
bcdedit /set hypervisorlaunchtype Auto
```

When something like "Error response from daemon: client is newer than server (client API version: 1.23, server API version: 1.22)"
happens, then you might want to try updating your base vm

```
docker-machine upgrade vm
```

[Node Dockerfile]:https://github.com/nodejs/docker-node
[Docker-Machine with Microsoft Hyper-V]: https://docs.docker.com/machine/drivers/hyper-v/
[Docker Cleanup Commands]: https://www.calazan.com/docker-cleanup-commands/
[Docker – Clean Up After Yourself!]:  http://blog.yohanliyanage.com/2015/05/docker-clean-up-after-yourself/
