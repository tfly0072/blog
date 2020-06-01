---
title: CI/CD considerations in cloud hybrid environment
description: Discusses the options when running a CI/CD pipeline in a cloud hybrid environment.
author: shawn.sesna@octopus.com
visibility: private
published: 2020-12-31
metaImage: 
bannerImage: 
tags:

---

It is becomming more common that companies are offloading things like email management, Virtual Machine (VM) infrastructure, or build servers to cloud providers.  It is also becomming common that companies find themselves in a mixed environment where some of their resources are on-premise and others exist in the cloud.  This can someimes be problematic when your CI/CD pipeline becomes distributed between on-premise and cloud based instracture.  In this post, I will cover strategies you can employ when your CI/CD components exist in a distributed environment.

## Transitioning from on-premise to cloud
An increasingly common scenario that we have encountered are customers that have been using the Self-Hosted version of Octopus Deploy, but are transitioning from their own infrastructure to using cloud offerings.

### Cloud build, on-premise Octopus
One of the scenarios that we have been presented with are customers that have transitioned thier build servers to cloud, but their Octopus Deploy server is still on their local infrastructure.  The challenge they face is how to get the cloud-based build server to push packages to their local instance of Octopus.  The answer is the most annoying answer that a techie can give, "It depends."

#### Local build agents
Several of the cloud build servers offer the ability to configure local build agents.  

- [Azure DevOps](https://azure.microsoft.com/en-us/services/devops/)
- [TeamCity](https://www.jetbrains.com/lp/teamcity/cloud-new/?utm_source=blog&utm_medium=referral&utm_campaign=cloud_beta)
- [Jenkins](https://www.cloudbees.com/)
- [Github Actions](https://github.com/features/actions)
- [AppVeyor](https://www.appveyor.com/)
- (There may be more, but these are the biggies)

With a local build agent, there are fewer network considerations to take into account to allow it to push to your local instance of Octopus Deploy.  It also comes with the advantage of not incurring compute costs or eating up your allotment of build minutes.

If a local build agent is not an available option, continue to `Only cloud agents` section.

#### Only cloud agents
Some of the available cloud build servers are purely cloud with no ability to offload build tasks to local infrastructure

- [Bitbucket pipelines](https://bitbucket.org/product/features/pipelines)
- [TravisCI](https://travis-ci.org/) (Technically this can be run locally, but all the resources I looked at appeared to be in beta status)
- [CircleCI](https://circleci.com/)

Without the ability to run local agents, the only option available to continue to allow the build server to push to a local instace of Octopus is opening the firewall.  To reduce your attack surface, it is advisable to consult the documentation of the cloud provider and only add the build agent IP address range.

### Local build, cloud Octopus
Pushing to a cloud Octopus instance from a local build server is usually pretty painless, however, we have had some customers encounter some difficulties

- Restricted egress
- Proxy servers

#### Restricted egress
There are a number of reasons as to why an organization might restrict external traffic.  Cloud Octopus uses SSL/TLS so it is usually just a matter of requesting the address to your Octopus instance be added to the firewall egress rules.

#### Proxy servers
To monitor traffic coming in and out, many organizations implement a proxy server.  Proxy server assignment is often done by Group Policy Objects (GPO) so it's done automatically when a user signs into a machine.  This can often trip people up in that the user account the build agent is using may not be part of the GPO and therefor doesn't get the proxy server added.  



