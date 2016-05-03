---
layout: post
title:  "Behold the puppet-master"
date:   2016-05-02 10:10:21 +0000
categories: puppet devops
description: "puppet automation tool introduction for devops"
published: false
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

### Quickstart

///////////
site.pp is the first manifest the Puppet agent checks when it connects to the master. It defines global settings and resource defaults that will apply to all nodes in your infrastructure. It is also where you will put your node definitions (sometimes called node statements).

///////////
Throughout the quests in the Learning VM, you will work in the /etc/puppetlabs/code/environments/production/modules directory. This is where you keep modules for your production environment. (Site specific modules you need to be available for all environments are kept in /etc/puppetlabs/code/modules, and modules required by Puppet Enterprise itself are kept in the /opt/puppetlabs/puppet/modules directory.)

///////////
As you continue to work with Puppet, you'll find that this package/file/service pattern is very common. These three resource types correspond to the common sequence of installing a package, customizing that package's functionality with configuration files, and starting the service provided by that package.The package/file/service pattern also describes the typical relationships of dependency among these three resources: a well-written class will define these relationships, telling Puppet to restart the service if the configuration file has been modified, and re-create the configuration file when the package is installed or updated.


[John Willis]:https://blog.docker.com/author/john-willis/
[Damon Edwards]:http://www.infoq.com/author/Damon-Edwards
[MTTR]:http://lmgtfy.com/?q=mttr
[Puppet learning vm]:https://puppet.com/download-learning-vm
[Puppet self paced training]:https://learn.puppet.com/category/self-paced-training
