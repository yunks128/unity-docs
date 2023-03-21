# Quick Start

Understanding how Unity CS works can be a little overwhelming and knowing where to start can be tricky. So we have written this step by step guide to help you through onboarding your app with Unity CS.

If, after working through this guide, you are still stuck, please create a [discussion topic](https://github.com/unity-sds/unity-cs/discussions) or email: unitycsdev@jpl.nasa.gov



The envisioned lifecycle of building, testing, and deploying of Unity service components, can be seen in the diagram below:

<figure><img src="../../.gitbook/assets/Screenshot 2023-03-21 at 2.36.32 PM.png" alt=""><figcaption></figcaption></figure>

## Application Integration

### Building

**Getting started with the common build system and containerization.**

* Dockerized builds with a common entry point
* Kickoff build
* Storing docker containers



### Unit Testing

**Unit(y) testing your application prior to acceptance and deployment with Unity CS**

* Common entry point for unit tests
* Kickoff unit tests
* Dockerized environment for unit testing
* Publishing test results



### Publish

**Information relating to central repositories and sensitive information, secrets and other items that need keeping away from prying eyes.**

* Storing sensitive credentials for publishing artifacts
* Specifying a repository to house artifacts/containers



### Deploying

**The Unity CS deployment framework can facilitate the deployment of Unity service area components to the cloud.**

\
**The main things that service areas need to do**, to integrate with the Unity CS deployment framework are as follows:

* Identify what components you need to deploy, and [create terraform scripts](https://unity-sds.gitbook.io/docs/developer-docs/common-services/docs/users-guide/deployment/creating-terraform-scripts) for them
* Make necessary changes to kubectl config files
* [Create an initial terraform metadata file](https://github.com/unity-sds/unity-cs/wiki/Initial-Terraform-Metadata-Config-File), that identifies basic information about your deployment
* Ensure that you have a proper [smoketest script](https://github.com/unity-sds/unity-cs-infra/blob/main/README\_deploy.md#common-entry-points-1) set up
* Invoke the [push-button deployment via Github Actions](https://github.com/unity-sds/unity-cs-infra/blob/main/README\_deploy.md#push-button-deployments-via-github-actions).

**Behind the scenes, the Unity CS deployment framework will:**

* Copy over core U-CS terraform "support" scripts to your repository (to be co-located with your terraform scripts)
* Perform necessary [terraform transformations and required tagging](https://github.com/unity-sds/unity-cs/wiki/Terraform-Transformer-Component)
* Deploy all resources (core supporting resources + your resources) to AWS, using the merged/modified terraform scripts.

**Other related documentation:**

* Deployment Catalog
* [Common Entry Points for Deployment](https://github.com/unity-sds/unity-cs-infra/blob/main/README.md)



### [Smoke Test](https://github.com/unity-sds/unity-cs-infra/blob/smolensk\_0.2\_mcp/README\_deploy.md#smoke-testing)

**Testing your application once its deployed. Ensure that the endpoints are accessible and functioning as expected. Also, what to do if it all goes wrong.**

* [Common entry point for smoke tests](https://github.com/unity-sds/unity-cs-infra/blob/main/README\_deploy.md#common-entry-points-1)
* Kickoff smoke tests
* Initiate rollback in case of failure



### Management and Monitoring

**How to keep track of your costs and resources within Unity CS.**

* Cost monitoring
* Resource monitoring (aws tag based queries)
* Logging (kubernetes, system logs, application monitoring)

\


### Teardown

**Removing or rolling back your application within the Unity CS environment.**

* Re-deploy previous version OR
* Terraform destroy via push-button OR
* [Manually destroying via terraform](https://github.com/unity-sds/unity-cs/wiki/Manually-Destroying-via-Terraform)

***

## Security

If you application requires authentication services please read the following section as they contain import information about the operation of security services within the Unity platform

* Authorization and Authentication
  * [Security Guidelines](https://github.com/unity-sds/unity-cs/wiki/Unity-Security-Guidelines)
  * [API Authentication](https://github.com/unity-sds/unity-cs-security/tree/main/code\_samples/hysds\_mozart\_mock\_consumer)
  * [UI Authentication](https://github.com/unity-sds/unity-cs-security/tree/main/code\_samples/hysds\_ui\_with\_auth)







