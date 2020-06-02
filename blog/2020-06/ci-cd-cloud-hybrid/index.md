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

From startups to established businesses, Platform as a Service (PaaS), Software as a Service (SaaS), and Infrastructure as a Service (IaaS) are making it easy for companies to move some of their workload from on-premise to cloud.  Often these workloads include build servers, deployment servers or both.  In this post, I will cover strategies you can employ when your CI/CD components exist in a distributed environment.

## Cloud build to on-premise Octopus
One of the scenarios that we have been presented with are customers that have transitioned thier build servers to cloud, but their Octopus Deploy server is still on their local infrastructure.  The challenge they face is how to get the cloud-based build server to push packages to their local instance of Octopus.  The answer is the most annoying answer that a techie can give, "It depends."

### Local build agents
Several of the cloud build servers offer the ability to configure local build agents.  

- [Azure DevOps](https://azure.microsoft.com/en-us/services/devops/)
- [TeamCity](https://www.jetbrains.com/lp/teamcity/cloud-new/?utm_source=blog&utm_medium=referral&utm_campaign=cloud_beta)
- [Jenkins](https://www.cloudbees.com/)
- [Github Actions](https://github.com/features/actions)
- [AppVeyor](https://www.appveyor.com/)
- (There may be more, but these are the biggies)

With a local build agent, there are fewer network considerations to take into account to allow it to push to your local instance of Octopus Deploy.  It also comes with the advantage of not incurring compute costs or eating up your allotment of build minutes.

If a local build agent is not an available option, continue to `Only cloud agents` section.

### Only cloud agents
Some of the cloud build technologies are purely cloud with no ability to offload build tasks to local infrastructure

- [Bitbucket pipelines](https://bitbucket.org/product/features/pipelines)
- [TravisCI](https://travis-ci.org/) (Technically this can be run locally, but all the resources I looked at appeared to be in beta status)
- [CircleCI](https://circleci.com/)

Without the ability to run local agents, the only option available to continue to allow the build server to push to a local instace of Octopus is opening the firewall.  To reduce your attack surface, it is advisable to consult the documentation of the cloud provider and only add the build agent IP address range.

## Local build to Octopus Cloud
Pushing to a cloud Octopus instance from a local build server is usually pretty painless, however, we have had some customers encounter some difficulties

- Restricted egress
- Proxy servers

### Restricted egress
There are a number of reasons as to why an organization might restrict external traffic.  Cloud Octopus uses SSL/TLS so it is usually just a matter of requesting the address to your Octopus instance be added to the firewall egress rules.

### Proxy servers
To monitor traffic coming in and out, many organizations implement a proxy server.  Proxy server assignment is often done by Group Policy Objects (GPO) so it's done automatically when a user signs into a machine.  This can often trip people up in that the user account the build agent is using may not be part of the GPO and therefor doesn't get the proxy server added.  

## Cloud hosted build VM to on-premise Octopus
In most respects, the issues and solutions to having a build server on a hosted VM are going to be similar to `Cloud build to on-premise Octopus`, however, there is one additional option.

### Site to Site VPN
For the big hosting providers like Microsoft Azure or Amazon AWS, you have the ability to bridge your on-premise network with your cloud network.  This can be accomplished by using a Site to Site Virtual Private Network (VPN) connection.  Using a Site to Site VPN connection, resources on your cloud network are able to speak to your local network as if they were in the same datacenter.  In these cases, pushing packages from a build server hosted on your cloud network is no different than if it was on-premise.

## Cloud hosted Octopus VM with on-premise build server
This scenario should be relatively simple to configure and will usually involve altering firewall rules to allow the connection from your local infrastructure to the cloud hosted VM.  The `Site to site VPN` solution would also work for this scenario.

## Third-party package repositories
In cases where none of above solutions will work, employing a third-party package repository (hosted or otherwise) may be your only option.  The third-party package repository would provide a centralized location for the build server and Octopus deploy to interact with

- Artifactory
- Nexus 
- GitHub
- Feedz.io

## Conclusion
Having CI/CD components distributed amongst on-premise and cloud technologies can present challenges in configurations.  In this post I have listed some options which will hopefully assist you in developing your strategy for implementation.