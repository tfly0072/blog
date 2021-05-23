---
title: The ECS Pitch
description: A proposal for new ECS steps in Octopus
author: matthew.casperson@octopus.com
visibility: private
published: 2999-01-01
metaImage: 
bannerImage: 
tags:
 - Octopus
---

Elastic Container Service (ECS) is the original Amazon container orchestration platform, having debuted in 2014. It remains a popular platform despite competition from Kubernetes, with the [Portworx container adoption survey](https://portworx.com/wp-content/uploads/2019/05/2019-container-adoption-survey.pdf) showing a third of respondents use ECS, [StackRox Kubernetes and Container Security and Adoption Trends](https://www.stackrox.com/kubernetes-adoption-security-and-market-share-for-containers/) showing ECS adoption at 20%, while [Datadog has reported around 16% adoption amongst its customer base](https://www.datadoghq.com/blog/3-clear-trends-in-ecs-adoption/).

One of our goals in 2020 is to expand the range of first class steps provided by Octopus to deploy to cloud Platform as a Service (PaaS) offerings, and in this post we'll run through set of steps and targets that are being proposed for deploying to ECS.

## Streamlined task definition updates

An ECS task definition configures one or more Docker images that are deployed as a single unit into an ECS cluster. Typical settings include memory and CPU requirements, security settings, networking, environment variables, and of course the Docker images themselves.

Most of these settings remain static, with the exception of the Docker image. Teams that have embraced Continuous Delivery (CD) will frequently publish new Docker images for deployment.

We are proposing a new step in Octopus that allows ECS task definitions to be defined. The Docker image(s) to be included in the task definition will be defined on the step. However, only the image name will be defined on the step, leaving the image tag (or version) to be selected at deployment time.

This step streamlines the process of creating new task definition revisions by allowing image versions, which are the values most likely to change with each deployment, to be selected at deployment time. We think this will appeal to anyone who has had to dig through the new task revision dialog in ECS each time a Docker image tag has to be updated.

![](https://via.placeholder.com/500x300 "width=500")
* A mock up of the ECS task step. *