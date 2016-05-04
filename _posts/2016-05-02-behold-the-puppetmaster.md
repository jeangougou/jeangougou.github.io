---
layout: post
title:  "Behold the puppet-master"
date:   2016-05-02 10:10:21 +0000
categories: puppet devops
description: "puppet automation tool introduction for devops"
published: true
tags:
- puppet
- devops
---

### What is DevOps?

_DevOps_ is a term that [John Willis][John Willis] and [Damon Edwards][Damon Edwards] came up with (according to my sources) around 2010. Initially defined as a set of principles with the acronym CAMS.
*Culture*: the right mindset to address constructively issues and solve them rapidly and efficiently;  
*Automation*: all with the click of a button, so it is repeatable;  
*Measurement*: deployment frequency, [MTTR][MTTR], and other relevant metrics can identify bottlenecks and show where the efforts should concentrate;  
*Sharing*: in and outside the team.

### What is Puppet?

_Puppet_ is an open-source IT automation tool. The _Puppet_ Domain Specific Language (DSL) is a Ruby-based coding language that provides a precise and adaptable way to describe a desired state for each machine in your infrastructure. Once you've described a desired state, _Puppet_ does the work to bring your systems in line and keep them there. Yes, you can do it all with scripts, but _Puppet_ gives you platform independent abstraction and the ability to manage quite a considerable number of nodes without losing your mind.

### Puppeteering 101

_Puppet_ is build with a client server architecture, the _Master_ (or server) builds a catalog of collected facts from the different _Nodes_ (or clients). This catalog is then pushed to all the involved _Nodes_ and diffed against local existing configuration, updating when necessary. This last operation will generate a report for the _Master_ used to update the dashboard and, more in general, to have a feedback on the operation.

The catalog is build in ```.pp``` files, usually a main manifest and one or more classes. These files are a representation of the desired state of the configuration. Through the DSL any element of a system is mapped to a resource with the generic format of:

```
  type {  'title':
    key1 => value1,
    key2 => value2,
    ...
  }
```

Where *type* represents the type of the resource described. It can be a file, a user, a package, a service [and so on][Puppet resource type].  
The *title* is the unique identifier, within the current module, for the resource. For instance, assuming I'm writing a user management tool, it makes sense to have a single place that maps my user _John Smith_, that would be the title.  
The list of *keys and values* are the attributes and the parameters needed to complete the configuration description.  
Some *keys* are package specific, needed for its complete configuration, some though, are for _Puppet_'s validation.
The most relevant *keys* are _ensure_ and the four *metaparameters* to define relationships _before_, _require_, _notify_ and _subscribe_.  

- _ensure_ will validate that a resource is present or belongs to a specific resource *type*
- _before_ (read as "is father of"), applies the current resource before the one specified in the values
- _require_ applies the value before the current resource definition
- _notify_ like _before_ with forced refreshes when the dependency changes
- _subscribe_ like _require_ with forced refreshes when the dependency changes

#### Where are my Puppetry things ?

There are some paths and files to be aware of so as to not mix up concerns in the configuration description and avoid conflicts. The first manifest file the _Puppet_ agent checks when connecting to _master_ is ```site.pp```. This file contains the node definitions and defines global settings and resources on a per environment basis. By default, the main manifest for a given environment is ```<ENVIRONMENTS DIRECTORY>/<ENVIRONMENT>/manifests```.  

The master manifest will therefore be in ```/etc/puppetlabs/code/environments/production/manifests```.
Similarly ```/etc/puppetlabs/code/environments/production/modules``` will contain modules for your production environment.
For site specific modules that need to be available for all environments ```/etc/puppetlabs/code/modules``` is the expected folder. _Puppet_ itself uses modules and stores them in ```/opt/puppetlabs/puppet/modules```.

#### I've clothed my Puppet

We are now ready to address the process workflow for an average _Puppet_ configuration to be applied.
A suggested common approach is the cycle package/file/service. This pattern addresses installing a package as a first step, followed by customizing that package's functionality with configuration files, and finally starting the service as expected.  By defining relationships correctly with *metaparameters* we can ensure that the configuration file is re-created every time a package is installed or updated and the service is restarted if said configuration file has changed.


[John Willis]:https://blog.docker.com/author/john-willis/
[Damon Edwards]:http://www.infoq.com/author/Damon-Edwards
[MTTR]:http://lmgtfy.com/?q=mttr
[Puppet learning vm]:https://puppet.com/download-learning-vm
[Puppet self paced training]:https://learn.puppet.com/category/self-paced-training
[Puppet architecture]:http://aosabook.org/en/puppet.html
[Puppet resource type]:https://docs.puppet.com/puppet/latest/reference/type.html
