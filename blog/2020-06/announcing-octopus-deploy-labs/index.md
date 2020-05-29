---
title: Announcing Octopus Deploy Labs
description: Octopus Deploy Labs is a new GitHub organization we created to store tools, apps, scripts to extend and manage Octopus Deploy.
author: bob.walker@octopus.com
visibility: private
published: 2020-12-31
metaImage: 
bannerImage: 
tags:
 - Engineering 
---

I'm excited to announce [Octopus Deploy Labs](https://github.com/OctopusDeployLabs).  It is a new GitHub organization that will contain repositories managed by Octopus Deploy containing tools, scripts and other items to help manage and extend your Octopus Deploy instance.

## Background

Octopus Deploy is an [API-first application](https://octopus.com/docs/octopus-rest-api).  Meaning, anything done in the UI can be done via the API.  On top of that, Octopus Deploy can call [webhooks via subscriptions](https://octopus.com/docs/administration/managing-infrastructure/subscriptions/webhook-slack).  Several Octopods have written scripts and apps that help manage and extend Octopus Deploy.  One such example is a script I have been working on called the Space Cloner.  The Space Cloner is a PowerShell script written by the Customer Success team to help us solve the following use cases we encountered with our personal instances and the [samples instance](https://samples.octopus.app). 

- As a user, I want to split my one massive default space into [multiple spaces on the same instance](docs/UseCase-BreakUpSpace.md).
- As a user, I have two Octopus Deploy instances.  One for dev/test deployments.  Another for staging/prod deployments.  I have the [same set of projects I want to keep in sync](docs/UseCase-KeepInstancesInSync.md).
- As a user, I want to clone a set of projects to a test instance to [verify an upgrade](docs/UseCase-CopyToTestInstance.md).
- As a user, I have a set of "parent" projects.  I clone from that project when I need to create a new project.  However, when the process on the "parent" project is updated, I would like to [update the existing "child" projects](docs/UseCase-ParentChildProjects.md).
- As a user, I would like to copy my projects from [self-hosted Octopus to Octopus Cloud](docs/UseCase-MigrateFromSelfHostedToCloud.md).

Here is the kicker.  It accomplishes that by leveraging the Octopus API.  However, it is not a full migration tool.  Because it leverages the API it cannot access sensitive variables, account information, or external feed credentials.  And it cannot copy over releases or deployments (it could, but the audit information would not be the same as the source).

The Space Cloner isn't a 100% solution.  Those limitations were known from the start, and honestly, I was fine with them.  I didn't need something as heavy-handed as the migrator.  I wanted a script that would copy a set of items from one space to another, or from the samples instance to my personal instance.  In a lot of cases the spaces or instances would have different packages, accounts, connection strings, releases, deployments, or external feeds.  I just needed something that got me there 90%.

As I was wrapping up the script I thought, I bet a lot of our customers would benefit from something like this.  I should share it.  I started passing around the script to various people within Octopus Deploy.  Almost everyone agreed, the script was good, but it isn't something we give to every customer and say "this will solve all your cloning problems, if it doesn't email support and we will fix it."

If I published the repository in the "official" Octopus Deploy organization there is a certain expectation of support.  We needed a place to publish repositories like these while at the same managing expectations of support.  This is why we created a new organization, Octopus Deploy Labs.  

## Support

All repositories in the Octopus Deploy Labs GitHub Organization are provided as-is with no support.  They will all use the [Apache 2.0 license](https://www.apache.org/licenses/LICENSE-2.0).  All repositories will have the issue tracker feature disabled as well.  If you encounter an issue the only way to fix it is to fork the repository and fix it yourself.   

## Pull Requests

Our default policy is to _not_ accept pull requests.  This goes back to the whole support conversation.  And in a lot of cases, it doesn't make sense to accept pull requests.  The repository could be a bare bones starter web app you host yourself.  You'd fork it, and then modify it to meet your hyper-specific use case.  

However, the author of the repository can opt to accept pull requests.  Some repositories have a lot of potential for growth.  We didn't want to stifle that.  In the case of the Space Cloner, I opted to accept pull requests.  I can see that set of scripts growing and expanding to cover more use cases.  

## Supported Octopus Deploy versions

Octopus Deploy is always evolving.  Since I've started in April of 2018 we have added Workers, K8s support, Spaces, Runbooks, Linux Tentacle, Jira Support, Build Information and more.  All repositories in the Octopus Deploy Labs GitHub Organization will inform you of what versions of Octopus Deploy it was developed against as well as expected supported versions.  

## Wrapping Up

I am excited with the launch of Octopus Deploy Labs.  When I brought it up to the Customer Success team they immediately thought of a half-dozen potential repositories we could migrate or create in that organization.  Keep an eye out for future announcements of new repositories!