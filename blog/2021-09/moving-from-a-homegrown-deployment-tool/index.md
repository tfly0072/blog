---
title: Considerations when moving from a homegrown deployment tool to Octopus Deploy
description:
author: ryan.rousseau@octopus.com
visibility: private
published: 3020-01-01
metaImage: to-be-added-by-marketing
bannerImage: to-be-added-by-marketing
bannerImageAlt: 125 characters max, describes image to people unable to see it.
isFeatured: false
tags:
 - DevOps
 - Company
 - Product
 - Events and Webinars
 - Engineering
---

There hasn't always been a surplus of deployment automation tools to choose from. Because of this, a lot of teams have developed and maintained their own deployment tools. At some point, those teams may decide that they no longer want to develop and support a bespoke deployment tool.

If you are currently looking to replace your homegrown deployment tool with Octopus, there are some points that you will need to consider for the migration.

In this post, I will cover some of the changes and decisions you will have to make to effectively move from your existing solution to Octopus Deploy.

## Differences between Octopus and your current system

During your migration, you will likely run into a few differences between Octopus Deploy and your system.

In some cases, you can customize your Octopus deployment process to mirror your existing process with custom scripts.

## Packages

One area where your solution and Octopus may differ is how applications are packaged and distributed to your infrastructure.

Octopus supports many [package formats](https://octopus.com/docs/packaging-applications). If you are already distributing your application as a zip file, JAR file, Docker container, or other supported format, you may not have to do much work to supply those packages to Octopus.

You can upload packages of most formats directly to the built-in repository of Octopus deploy. Deploying Docker images requires the use of an external Docker registery.

If you are already using a [package repository](https://octopus.com/docs/packaging-applications/package-repositories) supported by Octopus, you can configure Octopus to pull your applications packages directly from there.

### Versioning

If you are using a supported package format, ensure that the filename includes a valid version number. For example, for version 1.2.3 of an application named `octofx.web`, the package filename would be `octofx.web.1.2.3.zip`.

You can read more about versioning schemes [here](https://octopus.com/docs/packaging-applications/create-packages/versioning).

### Creating packages

A common trait I have seen in homegrown tools is that they do not use an archive file format for distributing applications. Instead, they work by moving application assets from a fileshare to the intended destinations.

Octopus does not have built-in support for deploying application files directly from a fileshare. Using an archive file format for packages lets Octopus distribute versioned applications efficiently, taking advantage of caching and in some cases, [delta compression](https://octopus.com/docs/deployments/packages/delta-compression-for-package-transfers).

If you are using a [CI system that has an integration](https://octopus.com/docs/packaging-applications/build-servers) with Octopus, you can create and push packages to Octopus with tasks provided by the integration.

If your CI system doesn't have a provided integration or extension, you can also create and push packages using a few [Octopus CLI commands](https://octopus.com/docs/packaging-applications/create-packages/octopus-cli).

```
octo pack .\builtApplication --id="octofx.web" --version="1.2.3" --format=zip
octo push --package="octofx.web.1.2.3.zip" --server="https://your-instance.octopus.app" --apiKey="API-YOURAPIKEY"
```

## Targets and infrastructure

You will need to provide Octopus with the connection details and the network access to distribute and configure your applications.

### Windows and Linux machines

If you deploy your applications to Windows or Linux machines, you will install an agent called a Tentacle on those machines. The [Octopus Server communicates with the Tentacle](https://octopus.com/docs/security/octopus-tentacle-communication) to execute the deployment.

In most cases, I recommend using Listening Tentacles which require the Octopus Server to be able to connect to the Tentacle agent over a specified port (10933 by default). If a network connection cannot be made from the Octopus Server to the target machine, using a Polling Tentacle would be the alternative option. In that case, the Tentacle makes the connection to the Octopus Server over a specified port (10943 by default).

In either case, you will need to install the agent and ensure that the appropriate ports are open between the Octopus Server and the target machines. The Tentacle installation can also be scripted which makes a large migration more manageable.

### Kubernetes and other cloud targets

Deployments to cloud targets differ a bit from deployments to Windows and Linux machines.

Instead of a Tentacle running directly on the machine hosting the application, you'll use a combination of an [Account](https://octopus.com/docs/infrastructure/accounts), a [Worker](https://octopus.com/docs/infrastructure/workers), and connection details to the cloud resource.

Octopus will use the Worker to call the appropriate tooling, authenticating with the provided Account.

For example, when deploying to Kubernetes hosted in GKE, Octopus will run a script on a Worker that calls `kubectl` against a cluster and uses a Google cloud account to authenticate the request.

Like with the previous scenario, there are network considerations. The Worker can reside either in your network or can be hosted in your cloud infrastructure. If it is hosted within your network, it will need network access to the target cloud resource. If it is hosted in your cloud infrastructure, then a connection between Octopus and the Worker will need to be established. Tentacles registered as Workers can also run in Listening or Polling mode.

## Existing scripts

One of the most flexible features of Octopus is the ability to run custom scripts during a deployment.

It is common for teams adopting Octopus to start with a collection of deployment scripts. This is especially true for teams that have built their own deployment tool.

Octopus has a large collection of built-in and community steps. If you have a common deployment scenario like deploying a web application to IIS or Tomcat, then you may not need your previous scripts.

However, Octopus cannot cover all scenarios with the built-in step templates. This is where your existing scripts can augment Octopus and complete your deployment process.

You will likely have to modify them so that they work with Octopus - especially if you want to use [Variables](https://octopus.com/docs/projects/variables) within your scripts.

### Calling custom scripts

There are a few different ways to call your custom script from your Octopus process.

The first way would be to use a [Run a Script](https://octopus.com/docs/deployments/custom-scripts/run-a-script-step) step. You can embed your script directly in the step or source it from a package. If it is a common script, you can save it to the Step Template Library and share it across multiple projects.

For scripts that are specific to a specific application, you can also call those as custom pre-deploy, deploy, and post-deploy scripts. This can be added to your deployment step directly or can be included in your application package directly.

For example, in our octofx.web package, if we have a file named deploy.ps1 or deploy.sh, Octopus will execute that script automatically during the deployment of that package.

## Self hosted or Cloud

There is one last decision point that I will mention here. Teams that are retiring their homegrown tool are often doing so because they no longer want to manage the infrastructure it's hosted on.

Octopus Deploy comes in two editions: Server, which you host yourself, and Cloud, which we host and manage for you.

Read more about the differences about Server and Cloud.

## Conclusion


## Learn more

- [link](https://www.example.com/resource)
- [repo with examples if used](https://www.github.com/repo)
