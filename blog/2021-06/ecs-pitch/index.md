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

*A mockup of the ECS task step.*

## Streamlined service deployments

Just as updating a task definition as part of a CD workflow means selecting new Docker image versions, deploying new ECS services means selecting new task definition revisions.

To support this workflow we're proposing a new feed in Octopus that exposes task definitions as "packages" and task revisions as "versions".

![](https://via.placeholder.com/500x300 "width=500")

*A mockup of the ECS task definition feed.*

The ECS service step is then configured to consume task definitions from the new feed. This means the step defers the selection of a task definition until deployment time.

![](https://via.placeholder.com/500x300 "width=500")

*A mockup of the ECS service step.*

The best thing about exposing task definition revisions as versions is that we get to treat them as single digit version strings. This means existing functionality like channel rules can be used to customize ECS service deployments.

## Environment management

ECS has no native concept of environments. However, with the proposed new Octopus ECS target, progressing deployments from the development environment, through test, and then to production becomes simple.

![](https://via.placeholder.com/500x300 "width=500")

*A mockup of the ECS target.*

This target will support you whether you choose to model environments within a single cluster using a service naming convention, or via separate clusters, regions, or accounts.

Targets can also take advantage of the existing security layers in Octopus, providing rules to limit access to targets based on the environment they belong to.

## Interactive blue/green and canary deployments

[ECS has native support for blue/green and canary deployments via CodeDeploy](https://docs.aws.amazon.com/AmazonECS/latest/userguide/deployment-type-bluegreen.html), providing a convenient tick-box solution for these advanced deployment patterns. However, the CodeDeploy deployment configurations only provide the ability to shift a fixed amount of traffic in fixed intervals. This model of deployment forces you [to actively stop a deployment](https://docs.amazonaws.cn/en_us/codedeploy/latest/userguide/deployments-stop.html) rather than permit it to progress.

The manual intervention step in Octopus is a simple but effective workflow feature that puts control of a deployment in your hands. This is especially important when performing a canary deployment, as the decision to increase the amount of traffic to a new deployment may require the kind of discussions that can not be automated. For example, you may need to  talk to your support team to verify the canary deployment hasn't introduced any unwanted problems.

To give you back complete control of your ECS deployments, the service step proposes an Octopus managed deployment type. When a deployment is managed by Octopus, the resulting ECS service will make use of task sets to allow traffic to be gradually, and if needed manually, transitioned to the new deployment.

You can find more information on externally managed ECS deployments in our blog post [
Canary deployments with ECS
](https://octopus.com/blog/ecs-canary-deployments).

![](https://via.placeholder.com/500x300 "width=500")

*A mockup of the ECS service deployment style.*

## Deployment verification

One of the advantages that task sets bring is the ability to deploy a new task definition in the background, away from any public traffic, but still accessible from a URL known only to you. With a specially configured listener rules, the newly deployed stack can be made available for testing, either manually or with automated tools, before the decision to promote traffic to it is made.

When deploying an ECS task with the Octopus deployment type, a number of listener rules can be defined to expose the existing and new stacks. By defining a special header or query string parameter, these rules can expose the new deployment only to your testers, keeping public traffic away until you are happy with the test results.

![](https://via.placeholder.com/500x300 "width=500")

*A mockup of the ECS service deployment style.*

## Conclusion

At Octopus we care deeply about enabling world class deployments, and in the post we saw how the proposed new ECS step:

* Makes continuous delivery easy by exposing Docker image tags and task definition revisions at deployment time.
* Allowing deployments to be promoted through multiple environments through the use of targets.
* Put control back in your hands with a custom deployment type taking advantage of task sets.
* Expose opportunities for testing deployments through custom network rules.

However, these are just proposals and subject to change, so if you have any feedback or comments, please feel free to reach out in the comments section below, or via twitter at @mjcasperson.

Happy deployments!