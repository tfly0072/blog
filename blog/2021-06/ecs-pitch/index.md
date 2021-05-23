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

Elastic Container Service (ECS) is the original Amazon container orchestration platform, having debuted in 2014. It remains a popular platform despite competition from Kubernetes, with the [Portworx container adoption survey](https://portworx.com/wp-content/uploads/2019/05/2019-container-adoption-survey.pdf) showing a third of respondents use ECS, the [StackRox Kubernetes and Container Security and Adoption Trends](https://www.stackrox.com/kubernetes-adoption-security-and-market-share-for-containers/) showing ECS adoption at 20%, while [Datadog has reported around 16% adoption amongst its customer base](https://www.datadoghq.com/blog/3-clear-trends-in-ecs-adoption/).

One of our goals in 2020 is to expand the range of first class steps provided by Octopus to deploy to cloud Platform as a Service (PaaS) offerings, and in this post we'll run through set of steps and targets that are being proposed for deploying to ECS.